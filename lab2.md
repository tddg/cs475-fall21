---
layout: page
title: "Lab 2: Raft"
permalink: /lab2.html
---

## Important Dates and Other Stuff

**Part 2A Due** Thursday, 10/14, 11:59pm.

**Part 2B Due** TBD

**Part 2C Due** TBD


## Resources

* How to read a paper (Raft example) [[slides](https://tddg.github.io/cs475-fall21/public/lecs/how_to_read_a_paper.pdf)] [[video](https://edstem.org/us/courses/8902/discussion/674977)]
* Code walk-thru [[video](https://edstem.org/us/courses/8902/discussion/674977)]
* Students' guide to Raft [[link](https://thesquareplanet.com/blog/students-guide-to-raft/)]



## Introduction

This is the first in a series of labs in which you'll build a
fault-tolerant key/value storage system. In this lab you'll implement
Raft, a replicated state machine protocol. In the next lab (lab 3)
you'll build a key/value service on top of Raft. Then you will
“shard” your service over multiple replicated state machines for
higher performance. 

A replicated service (e.g., key/value database) achieves fault
tolerance by storing copies of its data on multiple replica servers.
Replication allows the service to continue operating even if some of
its servers experience failures (crashes or a broken or flaky
network). The challenge is that failures may cause the replicas to
hold differing copies of the data.

Raft manages a service's state replicas, and in particular it helps
the service sort out what the correct state is after failures. Raft
implements state machine replication. It organizes client requests
into a sequence, called the log, and ensures that all the replicas
agree on the contents of the log. Each replica executes the client
requests in the log in the order they appear in the log, applying
those requests to the replica's local copy of the service's state.
Since all the live replicas see the same log contents, they all
execute the same requests in the same order, and thus continue to
have identical service state. If a server fails but later recovers,
Raft takes care of bringing its log up to date. Raft will continue to
operate as long as at least a majority of the servers are alive and
can talk to each other. If there is no such majority, Raft will make
no progress, but will pick up where it left off as soon as a majority
can communicate again.

In this lab you'll implement Raft as a Go object type with associated
methods, meant to be used as a module in a larger service. A set of
Raft instances talk to each other with RPC to maintain replicated
logs. Your Raft interface will support an indefinite sequence of
numbered commands, also called log entries. The entries are numbered
with index numbers. The log entry with a given index will eventually
be committed. At that point, your Raft should send the log entry to
the larger service for it to execute. 

> **NOTE:** Only RPC may be used for interaction between different
> Raft instances. For example, different instances of your Raft
> implementation are not allowed to share Go variables. Your
> implementation should not use files at all. 

In this lab you'll implement most of the Raft design described in
the extended paper, including saving persistent state and reading it
after a node fails and then restarts. **You will not implement cluster
membership changes (Section 6) or log compaction / snapshotting
(Section 7).** 

You should consult the [extended Raft paper](https://tddg.github.io/cs475-fall21/public/papers/raft.pdf) and the Raft lecture
notes. You may also find [the Runway model](https://runway.systems/?model=github.com/ongardie/runway-model-raft) for Raft or the original [Raft Visualization](https://raft.github.io/) useful to get a
sense of the high-level workings of Raft.  

> * **Hint:** Start early. Although the amount of code to implement
> isn't large, getting it to work correctly will be **very challenging**.
> Both the algorithm and the code is tricky and there are many corner
> cases to consider. When one of the tests fails, it may take a bit
> of puzzling to understand in what scenario your solution isn't
> correct, and how to fix your solution. 
> * **Hint:** Read and understand the [extended Raft paper](https://tddg.github.io/cs475-fall21/public/papers/raft.pdf) and the Raft
> lecture notes before you start. Your implementation should follow
> the paper's description closely, particularly Figure 2, since
> that's what the tests expect. 

This lab is due in three parts. You must submit each part on the
corresponding due date. This lab does not involve a lot of code, but
concurrency makes it very challenging to debug; **START EACH PART
EARLY!**



## Getting Started

We supply you with
skeleton code and tests. You can download the software from 
[https://tddg.github.io/cs475-fall21/public/lab2.tar](https://tddg.github.io/cs475-fall21/public/lab2.tar). The skeleton code and tests are in `raft`, and a simple
RPC-like system is in `labrpc`.

To download the tar file from Zeus:

```sh
% cd $HOME
% wget https://tddg.github.io/cs475-fall21/public/lab2.tar
% tar -xvf lab2.tar
% ls
labgob/  labrpc/  raft/
```

Copy these three directories into `src/` under your CS475 lab directory:

```sh
% cp -r labgob labrpc raft $HOME/cs475-fall21/labs/src/
% cd $HOME/cs475-fall21/labs/src
% ls
labgob/  labrpc/  raft/
```

You will be working on `src/raft` for this lab assignment.


To get up and running, execute the following commands: 

```sh
...
$ cd cs475-fall21/labs
$ export GOPATH="$PWD"
$ cd src/raft
$ go test
Test (2A): initial election ...
--- FAIL: TestInitialElection2A (4.84s)
    config.go:326: expected one leader, got none
Test (2A): election after network failure ...
--- FAIL: TestReElection2A (4.90s)
    config.go:326: expected one leader, got none
Test (2B): basic agreement ...
--- FAIL: TestBasicAgree2B (10.03s)
    config.go:471: one(100) failed to reach agreement
...
```


When you've finished all three parts of the lab, your implementation
should pass all the tests in the `src/raft` directory:

```sh
$ go test
Test (2A): initial election ...
  ... Passed --   3.0  3   46   13164    0
Test (2A): election after network failure ...
  ... Passed --   4.9  3  120   24309    0
Test (2B): basic agreement ...
  ... Passed --   1.1  3   16    4598    3
Test (2B): RPC byte count ...
  ... Passed --   3.0  3   48  114626   11
Test (2B): agreement despite follower disconnection ...
  ... Passed --   6.4  3  110   29737    8
Test (2B): no agreement if too many followers disconnect ...
  ... Passed --   3.9  5  184   38454    3
Test (2B): concurrent Start()s ...
  ... Passed --   0.8  3   10    2862    6
Test (2B): rejoin of partitioned leader ...
  ... Passed --   4.5  3  128   30888    4
Test (2B): leader backs up quickly over incorrect follower logs ...
  ... Passed --  31.2  5 2204 1779548  102
Test (2B): RPC counts arent too high ...
  ... Passed --   2.3  3   36   10862   12
Test (2C): basic persistence ...
  ... Passed --   4.4  3   76   20311    6
Test (2C): more persistence ...
  ... Passed --  17.6  5  856  187976   16
Test (2C): partitioned leader and one follower crash, leader restarts ...
  ... Passed --   2.1  3   34    8839    4
Test (2C): Figure 8 ...
  ... Passed --  36.3  5  936  199945   22
Test (2C): unreliable agreement ...
  ... Passed --   6.4  5  212   79855  246
Test (2C): Figure 8 (unreliable) ...
  ... Passed --  33.4  5 2764 6182207  491
Test (2C): churn ...
  ... Passed --  16.3  5  644  317082  211
Test (2C): unreliable churn ...
  ... Passed --  16.3  5  540  236498  121
PASS
ok  	raft	193.964s
```


## The code

Implement Raft by adding code to `raft/raft.go`. In that file you'll
find a bit of skeleton code, plus examples of how to send and receive
RPCs.

Your implementation must support the following interface, which the
tester and (eventually) your key/value server will use. You'll find
more details in comments in `raft.go`. 

```sh
// create a new Raft server instance:
rf := Make(peers, me, persister, applyCh)

// start agreement on a new log entry:
rf.Start(command interface{}) (index, term, isleader)

// ask a Raft for its current term, and whether it thinks it is leader
rf.GetState() (term, isLeader)

// each time a new entry is committed to the log, each Raft peer
// should send an ApplyMsg to the service (or tester).
type ApplyMsg
```

A service calls `Make(peers,me,...)` to create a Raft peer. The peers
argument is an array of established RPC connections, one to each Raft
peer (including this one). The `me` argument is the index of this peer
in the peers array. `Start(command)` asks Raft to start the processing
to append the command to the replicated log. `Start()` should return
immediately, without waiting for this process to complete. The
service expects your implementation to send an `ApplyMsg` for each new
committed log entry to the `applyCh` argument to `Make()`. 

Your Raft peers should exchange RPCs using the labrpc Go package that
we provide to you. It is modeled after Go's [rpc library](https://pkg.go.dev/net/rpc), but
internally uses Go channels rather than sockets. `raft.go` contains
some example code that sends an RPC (`sendRequestVote()`) and that
handles an incoming RPC (`RequestVote()`). The reason you must use
labrpc instead of Go's RPC package is that the tester tells `labrpc` to
delay RPCs, re-order them, and delete them to simulate challenging
network conditions under which your code should work correctly. Don't
rely on modifications to `labrpc` because we will test your code with
the `labrpc` as handed out. 

Your first implementation may not be clean enough that you can easily
reason about its correctness. Give yourself enough time to rewrite
your implementation so that you can easily reason about its
correctness. Subsequent labs will build on this lab, so it is
important to do a good job on your implementation. 


## Part 2A

**Task:** 
Implement leader election and heartbeats (`AppendEntries` RPCs with no
log entries). The goal for Part 2A is for a single leader to be
elected, for the leader to remain the leader if there are no
failures, and for a new leader to take over if the old leader fails
or if packets to/from the old leader are lost. Run `go test -run 2A` to
test your 2A code. 


> * **Hint:** Add any state you need to the `Raft` struct in `raft.go`.
> You'll also need to define a struct to hold information about each
> log entry. Your code should follow Figure 2 in the paper as closely
> as possible.
> * **Hint:** Fill in the `RequestVoteArgs` and RequestVoteReply
> structs. Modify `Make()` to create a background goroutine that will
> kick off leader election periodically by sending out `RequestVote`
> RPCs when it hasn't heard from another peer for a while. This way a
> peer will learn who is the leader, if there is already a leader, or
> become the leader itself. Implement the `RequestVote()` RPC handler
> so that servers will vote for one another. 
> * **Hint:** To implement heartbeats, define an `AppendEntries` RPC
> struct (though you may not need all the arguments yet), and have
> the leader send them out periodically. Write an `AppendEntries` RPC
> handler method that resets the election timeout so that other
> servers don't step forward as leaders when one has already been
> elected. 
> * **Hint:** Make sure the election timeouts in different peers
> don't always fire at the same time, or else all peers will vote
> only for themselves and no one will become the leader. 
> * **Hint:** The tester requires that the leader send heartbeat RPCs
> no more than ten times per second. 
> * **Hint:** The tester requires your Raft to elect a new leader
> within five seconds of the failure of the old leader (if a majority
> of peers can still communicate). Remember, however, that leader
> election may require multiple rounds in case of a split vote (which
> can happen if packets are lost or if candidates unluckily choose
> the same random backoff times). You must pick election timeouts
> (and thus heartbeat intervals) that are short enough that it's very
> likely that an election will complete in less than five seconds
> even if it requires multiple rounds. 
> * **Hint:** The paper's Section 5.2 mentions election timeouts in
> the range of 150 to 300 milliseconds. Such a range only makes sense
> if the leader sends heartbeats considerably more often than once
> per 150 milliseconds. Because the tester limits you to 10
> heartbeats per second, you will have to use an election timeout
> larger than the paper's 150 to 300 milliseconds, but not too large,
> because then you may fail to elect a leader within five seconds. 
> * **Hint:** You may find Go's [rand](https://pkg.go.dev/math/rand) useful. 
> * **Hint:** You'll need to write code that takes actions
> periodically or after delays in time. The easiest way to do this is
> to create a goroutine with a loop that calls
> [time.Sleep()](https://pkg.go.dev/time#Sleep). The hard
> way is to use Go's `time.Timer` or `time.Ticker`, which are difficult
> to use correctly. 
> * **Hint:** If you are puzzled about locking, you may find 
> [this advice](https://tddg.github.io/cs475-fall21/public/raft-locking.txt) helpful. 
> * **Hint:** If your code has trouble passing the tests, read the
> paper's Figure 2 again; the full logic for leader election is
> spread over multiple parts of the figure. 
> * **Hint:** A good way to debug your code is to insert print
> statements when a peer sends or receives a message, and collect the
> output in a file with `go test -run 2A > out`. Then, by studying the
> trace of messages in the out file, you can identify where your
> implementation deviates from the desired protocol. You might find
> `DPrintf` in `util.go` useful to turn printing on and off as you debug
> different problems. 
> * **Hint:** Go RPC sends only struct fields whose names start with
> capital letters. Sub-structures must also have capitalized field
> names (e.g. fields of log records in an array). The `labgob` package
> will warn you about this; don't ignore the warnings. 
> * **Hint:** You should check your code with `go test -race`, and fix
> any races it reports. 

Be sure you pass the 2A tests before submitting Part 2A, so that you
see something like this: 

```sh
$ go test -run 2A
Test (2A): initial election ...
  ... Passed --   2.5  3   30    0
Test (2A): election after network failure ...
  ... Passed --   4.5  3   78    0
PASS
ok      raft    7.016s
```

Each "Passed" line contains four numbers; these are the time that the
test took in seconds, the number of Raft peers (usually 3 or 5), the
number of RPCs sent during the test, and the number of log entries
that Raft reports were committed. Your numbers will differ from those
shown here. You can ignore the numbers if you like, but they may help
you sanity-check the number of RPCs that your implementation sends.
For all of labs 2, 3, and 4, the grading script will fail your
solution if it takes more than 600 seconds for all of the tests (`go
test`), or if any individual test takes more than 120 seconds. 



Your code will be tested on Autolab. No marks will be awarded if your
code does not pass the test. You will receive full marks only if your
code successfully passes the test.


Please post questions on Ed.


## Point distribution

There are a total of 2 tests for Part 2A. 
Each individual test takes 5 points. That is, Part 2A carries 10
points. 

Your code will be tested on Autolab. No marks will be awarded if your
code does not pass the test. You will receive full marks only if your
code successfully passes the test.


### Submitting 2A on Autolab

You must turn in your lab assignment using
[Autolab](https://autolab-gmu-systems.org).  Read this
[document](https://docs.google.com/document/d/1bwWx8p4_vSaNwVPzImRVKOXuj58qVMWWYu89Ejigs4A/edit?usp=sharing) 
for instructions on how to sign-up for Autolab. If you did not
receive a confirmation email from Autolab to set a password, enter
your @gmu.edu (**NOT** masonlive) email, and click "Forgot your
password" to get a new password. You may skip the Autolab signup 
step if you have done this in lab0.

Create a **tar** file of only the following Go source file:
`raft.go`.
Please, .tar only, not .tgz, nor .7z/.zip.  Name your tar file as
`lab2a-handin.tar`. 

```sh
% tar -cvf lab2a-handin.tar raft.go
```

**Please do not put any directory in your tar file
as our autograder is scripted to directly fetch src files not
directories.** Use the following command to examine the content 
of your tar file:

```sh
% tar -tvf lab2a-handin.tar
-rw-r--r--  0 yue    staff    2911 Sep 30 16:38 raft.go
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
% git commit -am 'the final awesome solution of lab2a: [Your Name] and [Your GMU email]'
```


## Part 2B 

TBD...


## Acknowledgment

The lab assignment is adapted from MIT's 6.824 course. Thanks to
Frans Kaashoek, Robert Morris, and Nickolai Zeldovich for their
support.


