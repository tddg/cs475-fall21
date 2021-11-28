---
layout: page
title: "Lab 3: Fault-tolerant Key/Value Service"
permalink: /lab3.html
---

## Important Dates and Other Stuff

**Due** Tuesday, 12/07, 11:59pm.

## Changes

* Please re-download the [`lab3.tar`](https://tddg.github.io/cs475-fall21/public/lab3.tar) 
file if your previous download missed the `linearizability/` library. 

* Updated the submission instruction: there are four source files to
submit instead of three; please check.



## Resources

* How to read a paper (Raft example) [[slides](https://tddg.github.io/cs475-fall21/public/lecs/how_to_read_a_paper.pdf)] [[video](https://edstem.org/us/courses/8902/discussion/674977)]
* Students' guide to Raft [[link](https://thesquareplanet.com/blog/students-guide-to-raft/)]



## Introduction

In this lab you will build a fault-tolerant key/value storage
service using your Raft library from [Lab
2](https://tddg.github.io/cs475-fall21/lab2.html). You key/value
service will be a replicated state machine, consisting of several
key/value servers that use Raft to maintain replication. Your
key/value service should continue to process client requests as long
as a majority of the servers are alive and can communicate, in spite
of other failures or network partitions. 

The service supports three operations: `Put(key, value)`, `Append(key,
arg)`, and `Get(key)`. It maintains a simple database of key/value
pairs. `Put()` replaces the value for a particular key in the database,
`Append(key, arg)` appends arg to key's value, and `Get()` fetches the
current value for a key. An `Append` to a non-existant key should act
like `Put`. Each client talks to the service through a `Clerk` with
`Put/Append/Get` methods. A `Clerk` manages RPC interactions with the
servers. 

Your service must provide strong consistency to applications calls
to the `Clerk` `Get/Put/Append` methods. Here's what we mean by strong
consistency. If called one at a time, the Get/Put/Append methods
should act as if the system had only one copy of its state, and each
call should observe the modifications to the state implied by the
preceding sequence of calls. For concurrent calls, the return values
and final state must be the same as if the operations had executed
one at a time in some order. Calls are concurrent if they overlap in
time, for example if client X calls `Clerk.Put()`, then client Y calls
`Clerk.Append()`, and then client X's call returns. Furthermore, a call
must observe the effects of all calls that have completed before the
call starts (so we are technically asking for linearizability). 

Strong consistency is convenient for applications because it means
that, informally, all clients see the same state and they all see the
latest state. Providing strong consistency is relatively easy for a
single server. It is harder if the service is replicated, since all
servers must choose the same execution order for concurrent requests,
and must avoid replying to clients using state that isn't up to date. 

In this lab, you will implement a key/value store service directly atop
the Raft library that you've built in Lab 2.


> * **Hint:** This lab doesn't require you to write much code, but you
> will most likely spend a substantial amount of time thinking and
> staring at debugging logs to figure out why your implementation
> doesn't work. Debugging will be more challenging than in the Raft
> lab because there are more components that work asynchronously of
> each other. Start early. 
> * **Hint:** You should reread the [extended Raft paper](https://tddg.github.io/cs475-fall21/public/papers/raft.pdf),
> in particular Sections 8 (client interaction).
> * **Hint:** You are allowed to add fields to the Raft `ApplyMsg`, and
> to add fields to Raft RPCs such as `AppendEntries`. But be sure that
> your code continues to pass the Lab 2 tests. 



## Getting Started

We supply you with
skeleton code and tests in `src/kvraft`. You can download the software from 
[https://tddg.github.io/cs475-fall21/public/lab3.tar](https://tddg.github.io/cs475-fall21/public/lab3.tar). 
You will need to modify `kvraft/client.go`, `kvraft/server.go`, and perhaps `kvraft/common.go`. 

To download the tar file from Zeus:

```sh
% cd $HOME
% wget https://tddg.github.io/cs475-fall21/public/lab3.tar
% tar -xvf lab3.tar
% ls
kvraft          lab3.tar        linearizability
```

Copy these two directories `kvraft/` and `linearizability/` into `src/` under your CS475 lab directory:

```sh
% cp -r kvraft linearizability $HOME/cs475-fall21/labs/src/
% cd $HOME/cs475-fall21/labs/src
% ls
kvraft/  labgob/  labrpc/  linearizability/  raft/
```

(You should note that the above dir does not list the dirs for Lab 0 and Lab 1, which should be included in your CS475 lab directory assuming you haven't changed to a new directory.)


To get up and running, execute the following commands: 

```sh
...
% cd cs475-fall21/labs
% export GOPATH="$PWD"
% cd src/kvraft
% go test
...
%
```

### Key/value service without log compaction


Each of your key/value servers ("kvservers") will have an associated
Raft peer. Clerks send `Put()`, `Append()`, and `Get()` RPCs to the
kvserver whose associated Raft is the leader. The kvserver code
submits the Put/Append/Get operation to Raft, so that the Raft log
holds a sequence of Put/Append/Get operations. All of the kvservers
execute operations from the Raft log in order, applying the
operations to their key/value databases; the intent is for the
servers to maintain identical replicas of the key/value database.

A `Clerk` sometimes doesn't know which kvserver is the Raft leader. If
the `Clerk` sends an RPC to the wrong kvserver, or if it cannot reach
the kvserver, the Clerk should re-try by sending to a different
kvserver. If the key/value service commits the operation to its Raft
log (and hence applies the operation to the key/value state machine),
the leader reports the result to the `Clerk` by responding to its RPC.
If the operation failed to commit (for example, if the leader was
replaced), the server reports an error, and the `Clerk` retries with a
different server. 


**Task:**
Your first task is to implement a solution that works when there are
no dropped messages, and no failed servers.

You'll need to add RPC-sending code to the Clerk `Put/Append/Get`
methods in `client.go`, and implement `PutAppend()` and `Get()` RPC
handlers in `server.go`. These handlers should enter an `Op` in the Raft
log using `Start()`; you should fill in the `Op` struct definition in
`server.go` so that it describes a `Put/Append/Get` operation. Each
server should execute `Op` commands as Raft commits them, i.e. as they
appear on the `applyCh`. An RPC handler should notice when Raft commits
its `Op`, and then reply to the RPC.

You have completed this task when you **reliably** pass the first test in
the test suite: "One client". You may also find that you can pass the
"concurrent clients" test, depending on how sophisticated your
implementation is. 


> * **Note:**  Your kvservers should not directly communicate; they
> should only interact with each other through the Raft log.


> * **Hint:** After calling `Start()`, your kvservers will need to wait
> for Raft to complete agreement. Commands that have been agreed upon
> arrive on the `applyCh`. You should think carefully about how to
> arrange your code so that it will keep reading `applyCh`, while
> `PutAppend()` and `Get()` handlers submit commands to the Raft log
> using `Start()`. It is easy to achieve deadlock between the kvserver
> and its Raft library. 
> * **Hint:** Your solution needs to handle the case in which a
> leader has called `Start()` for a `Clerk`'s RPC, but loses its
> leadership before the request is committed to the log. In this case
> you should arrange for the `Clerk` to re-send the request to other
> servers until it finds the new leader. One way to do this is for
> the server to detect that it has lost leadership, by noticing that
> a different request has appeared at the index returned by `Start()`,
> or that Raft's term has changed. If the ex-leader is partitioned by
> itself, it won't know about new leaders; but any client in the same
> partition won't be able to talk to a new leader either, so it's OK
> in this case for the server and client to wait indefinitely until
> the partition heals. 
> * **Hint:**  You will probably have to modify your `Clerk` to
> remember which server turned out to be the leader for the last RPC,
> and send the next RPC to that server first. This will avoid wasting
> time searching for the leader on every RPC, which may help you pass
> some of the tests quickly enough. 
> * **Hint:** A kvserver should not complete a `Get()` RPC if it is not
> part of a majority (so that it does not serve stale data). A simple
> solution is to enter every `Get()` (as well as each `Put()` and
> `Append()`) in the Raft log. You don't have to implement the
> optimization for read-only operations that is described in Section 8.
> * **Hint:** It's best to add locking from the start because the
> need to avoid deadlocks sometimes affects overall code design.
> Check that your code is race-free using `go test -race`. 

In the face of unreliable connections and server failures, a `Clerk`
may send an RPC multiple times until it finds a kvserver that replies
positively. If a leader fails just after committing an entry to the
Raft log, the `Clerk` may not receive a reply, and thus may re-send the
request to another leader. Each call to `Clerk.Put()` or `Clerk.Append()`
should result in just a single execution, so you will have to ensure
that the re-send doesn't result in the servers executing the request
twice. 


**Task:**
Add code to cope with duplicate `Clerk` requests, including situations
where the `Clerk` sends a request to a kvserver leader in one term,
times out waiting for a reply, and re-sends the request to a new
leader in another term. The request should always execute just once.
Your code should pass the `go test -run 3A` tests. 


> * **Hint:** You will need to uniquely identify client operations to
> ensure that the key/value service executes each one just once. 
> * **Hint:** Your scheme for duplicate detection should free server
> memory quickly, for example by having each RPC imply that the
> client has seen the reply for its previous RPC. It's OK to assume
> that a client will make only one call into a Clerk at a time. 


Your code should now pass the Lab 3A tests, like this: 


```sh
% go test -run 3A
Test: one client (3A) ...
  ... Passed --  15.5  5  4653  126
Test: many clients (3A) ...
  ... Passed --  16.9  5 14409  631
Test: unreliable net, many clients (3A) ...
  ... Passed --  20.2  5  1872  391
Test: concurrent append to same key, unreliable (3A) ...
  ... Passed --   3.9  3   188   52
Test: progress in majority (3A) ...
  ... Passed --   0.6  5    53    2
Test: no progress in minority (3A) ...
  ... Passed --   1.3  5   120    3
Test: completion after heal (3A) ...
  ... Passed --   1.1  5    53    3
Test: partitions, one client (3A) ...
  ... Passed --  22.5  5  7795  112
Test: partitions, many clients (3A) ...
  ... Passed --  24.1  5 32868  554
Test: restarts, one client (3A) ...
  ... Passed --  19.8  5 16537  126
Test: restarts, many clients (3A) ...
  ... Passed --  21.9  5 63309  632
Test: unreliable net, restarts, many clients (3A) ...
  ... Passed --  25.2  5  2556  392
Test: restarts, partitions, many clients (3A) ...
  ... Passed --  28.0  5 33568  487
Test: unreliable net, restarts, partitions, many clients (3A) ...
  ... Passed --  28.8  5  2783  306
Test: unreliable net, restarts, partitions, many clients, linearizability checks (3A) ...
  ... Passed --  26.3  7 10383  609
PASS
ok  	/kvraft	256.436s
```


## Point distribution

There are a total of 15 tests for Lab 3.  

**However, you will only need to pass the first 9 tests in order to get 
the full marks, which is 9 x 5 = 45 points (10% of the overall grade).** 

Test 10-15 are for **extra credits**, each worth 2.5 points.  That is,
Lab 3's bonus part carries 6 x 2.5 = 15 points. 

Your code will be tested on Autolab. No marks will be awarded if your
code does not pass the test. You will receive full marks only if your
code successfully passes the test.


### Submitting Lab 3 on Autolab

You must turn in your lab assignment using
[Autolab](https://autolab-gmu-systems.org).  Read this
[document](https://docs.google.com/document/d/1bwWx8p4_vSaNwVPzImRVKOXuj58qVMWWYu89Ejigs4A/edit?usp=sharing) 
for instructions on how to sign-up for Autolab. If you did not
receive a confirmation email from Autolab to set a password, enter
your @gmu.edu (**NOT** masonlive) email, and click "Forgot your
password" to get a new password. You may skip the Autolab signup 
step if you have done this in Lab 0.

Create a **tar** file of the following Go source files:
`client.go`, `server.go`, `common.go`, and `raft.go`.
Please, .tar only, not .tgz, nor .7z/.zip.  Name your tar file as
`lab3-handin.tar`. 

```sh
% tar -cvf lab3-handin.tar client.go server.go common.go raft.go
```

**Please do not put any directory in your tar file
as our autograder is scripted to directly fetch src files not
directories.** Use the following command to examine the content 
of your tar file:

```sh
% tar -tvf lab3-handin.tar
-rw-r--r--  0 yue    staff    2911 Sep 30 16:38 client.go
-rw-r--r--  0 yue    staff    2911 Sep 30 16:38 raft.go
-rw-r--r--  0 yue    staff    2911 Sep 30 16:38 server.go
-rw-r--r--  0 yue    staff    2911 Sep 30 16:38 common.go
```

When you upload your assignment, Autolab will automatically untar it
and test it. You should verify that the result that Autolab generates
is what you expect. Test your code on Zeus before submitting it to
Autolab.  Your code is tested in a cloud Linux VM. Assignments that
do not compile or run will receive a maximum of 50%. Note that we
have provided ample resources for you to verify that our view of your
assignment is the same as your own: you will see the result of the
test execution for your assignment when you submit it. 

You can resubmit your assignment an unlimited number of times before
the deadline. Note the late submission policy: assignments will be
accepted up until 3 days past the deadline at a penalty of 10% per
late day; after 3 days, no late assignments will be accepted, no
exceptions.


### Sharing your repo with GTA 


You will need to share your **private** repository with our GTA
Rui (his GitLab ID is the same as his Mason Email ID: `ryang22`).
You may skip the above sharing step if you have done it already for
lab0.

**The final, important step:** Don't forget to commit all your changes:

```sh
% git commit -am 'the final awesome solution of lab3: [Your Name] and [Your GMU email]'
```


## Acknowledgment

The lab assignment is adapted from MIT's 6.824 course. Thanks to
Frans Kaashoek, Robert Morris, and Nickolai Zeldovich for their
support.


