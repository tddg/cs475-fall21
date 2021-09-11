---
layout: page
title: "Lab 1: MapReduce"
permalink: /lab1.html
---

## Important Dates and Other Stuff

**Due** Tuesday, 09/28, 11:59pm.

Overview video: [link](https://edstem.org/us/courses/8902/discussion/601030)

Overview notes: [pdf](./public/lecs/lab1_mapreduce_arch.pdf)

## Introduction

In this lab assignment you'll build a MapReduce library as a way to
learn the Go programming language and as a way to learn about fault
tolerance in distributed systems. In the first part you will write a
simple MapReduce program. In the second part you will write a Master
that hands out tasks to workers, and handles failures of workers. The
interface to the library and the approach to fault tolerance is
similar to the one described in the original [MapReduce
paper](https://tddg.github.io/cs475-fall21/public/papers/mapreduce_osdi04.pdf). 


## Software

You'll implement this lab in [Go](https://golang.org/). The Go web
site contains lots of tutorial information which you may want to look
at. 

We supply you with parts of a MapReduce implementation that supports
both distributed and non-distributed operation (just the boring
bits). You can download the software skeleton from 
[https://tddg.github.io/cs475-fall21/public/lab1.tar](https://tddg.github.io/cs475-fall21/public/lab1.tar). 
To download the tar file from Zeus:

```sh
% cd $HOME
% wget https://tddg.github.io/cs475-fall21/public/lab1.tar
% tar -xvf lab1.tar
% ls
src/
```

Copy `src/` into your CS475 lab directory:

```sh
% cp -r src $HOME/cs475-fall21/labs/
% cd $HOME/cs475-fall21/labs/
% ls
lab0/ src/
```

Now your local Git reporitory should contain two separate
sub-directories, `lab0/` and `src/`. `src/` further contains two
sub-directories, `main/` and `mapreduce/`.  You will be working on
`src/` for this lab assignment.

You can also view a snapshot of Lab 1's src
from [https://git.gmu.edu/cs475-fall21/labs/-/tree/master/src](https://git.gmu.edu/cs475-fall21/labs/-/tree/master/src). 

You must make sure your code runs on x86 or x86_64 machines: that is,
`uname -a` should mention `i386 GNU/Linux` or `i686 GNU/Linux` or
`x86_64 GNU/Linux`. Autolab is running on an x86_64 cloud server, so
you should test your submission on Zeus before submitting on Autolab.


## Preamble: Getting familiar with the source

The MapReduce implementation we give you has support for two modes
of operation, **sequential** and **distributed**. 
In the former, the map and reduce tasks are all executed serially:
first, the first map task is executed to completion, then the second,
then the third, etc. When all the map tasks have finished, the first
reduce task is run, then the second, etc. This mode, while not very
fast, can be very useful for debugging, since it removes much of the
noise seen in a parallel execution. The distributed mode runs many
worker threads that first execute map tasks in parallel, and then
reduce tasks. This is much faster, but also harder to implement and
debug. 

The `mapreduce` package provides a simple MapReduce library with a
sequential implementation. Applications should normally call
`Distributed()` [located in `master.go`] to start a job, but may instead
call `Sequential()` [also in `master.go`] to get a sequential execution
for debugging purposes. 

The flow of the mapreduce implementation is as follows: 

1. The application provides a number of input files, a map function,
a reduce function, and the number of reduce tasks (`nReduce`).

2. A master is created with this knowledge. It spins up an RPC server
(see `master_rpc.go`), and waits for workers to register (using the RPC
call `Register()` [defined in `master.go`]). As tasks become available
(in steps 4 and 5), `schedule()` [`schedule.go`] decides how to assign
those tasks to workers, and how to handle worker failures. 

3. The master considers each input file one map task, and makes a
call to `doMap()` [`common_map.go`] at least once for each task. It does
so either directly (when using `Sequential()`) or by issuing the `DoTask`
RPC on a worker [`worker.go`]. Each call to `doMap()` reads the
appropriate file, calls the map function on that file's contents, and
writes the resulting key/value pairs to `nReduce` intermediate files.
`doMap()` hashes each key to pick the intermediate file and thus the
reduce task that will process the key. There will be `nMap` X `nReduce`
files after all map tasks are done. Each file name contains a prefix,
the map task number, and the reduce task number. If there are two map
tasks and three reduce tasks, the map tasks will create these six
intermediate files: 

	```sh
	mrtmp.xxx-0-0
	mrtmp.xxx-0-1
	mrtmp.xxx-0-2
	mrtmp.xxx-1-0
	mrtmp.xxx-1-1
	mrtmp.xxx-1-2
	```
	
	Each worker must be able to read files written by any other worker,
	as well as the input files. Real deployments use distributed storage
	systems such as GFS (for input) and transfer files (for map-to-reduce
	intermediate results) between workers to allow this access even
	though workers run on different machines. In this lab you'll run all
	the workers on the same machine, and use the local file system. 

4. The master next makes a call to `doReduce()` [`common_reduce.go`] at
least once for each reduce task. The `doReduce()` for reduce task `r`
collects the `r`th intermediate file from each map task, and calls the
reduce function for each key that appears in those files. The reduce
tasks produce `nReduce` result files. 

5. The master calls `mr.merge()` [`master_splitmerge.go`], which merges
all the `nReduce` files produced by the previous step into a single
output. 

6. The master sends a Shutdown RPC to each of its workers, and then
shuts down its own RPC server. 

> **NOTE:** Over the course of the following exercises, you will have
to write/modify `doMap`, `doReduce`, and `schedule` yourself. These are
located in `common_map.go`, `common_reduce.go`, and `schedule.go`
respectively. You will also have to write the map and reduce
functions in `../main/wc.go`. 

You should not need to modify any other files, but reading them
might be useful in order to understand how the other methods fit into
the overall architecture of the system. 


## Part I: MapReduce input and output

The MapReduce implementation you are given is missing some pieces.
Before you can write your first MapReduce function pair, you will
need to fix the sequential implementation. In particular, the code we
give you is missing two crucial pieces: the function that divides up
the output of a map task, and the function that gathers all the
inputs for a reduce task. These tasks are carried out by the `doMap()`
function in `common_map.go`, and the `doReduce()` function in
`common_reduce.go` respectively. The comments in those files should
point you in the right direction.

To help you determine if you have correctly implemented `doMap()` and
`doReduce()`, we have provided you with a Go test suite that checks the
correctness of your implementation. These tests are implemented in
the file `test_test.go`. To run the tests for the sequential
implementation that you have now fixed, run: 

```sh
$ cd cs475-fall21/labs
$ export GOPATH="$PWD" # Must do this to tell go where the lab top-level is.
$ cd src/mapreduce
$ go test -run Sequential mapreduce/...
ok  	mapreduce	2.451s
```

If the output did not show ok next to the tests, your implementation
has a bug in it. To give more verbose output, set `debugEnabled =
true` in `common.go`, and add `-v` to the test command above. You
will get much more output along the lines of: 

```sh
$ go test -v -run Sequential mapreduce/... 
=== RUN   TestSequentialSingle
master: Starting Map/Reduce task test
Merge: read mrtmp.test-res-0
master: Map/Reduce task completed
--- PASS: TestSequentialSingle (1.54s)
=== RUN   TestSequentialMany
master: Starting Map/Reduce task test
Merge: read mrtmp.test-res-0
Merge: read mrtmp.test-res-1
Merge: read mrtmp.test-res-2
master: Map/Reduce task completed
--- PASS: TestSequentialMany (1.47s)
PASS
ok  	mapreduce	2.824s
```


## Part II: Single-worker word count

Now that the map and reduce tasks are connected, we can start
implementing some interesting MapReduce operations. For this lab,
we will be implementing word count — a simple and classical MapReduce
example. Specifically, your task is to modify `mapF` and `reduceF` so
that `wc.go` reports the number of occurrences of each word. A word
is any contiguous sequence of letters, as determined by
`unicode.IsLetter`.

There are some input files with pathnames of the form `pg-*.txt` in
src/main, downloaded from Project Gutenberg. Go ahead and try to
compile the initial software we provide you and run it with the
provided input files: 

```sh
$ cd "$GOPATH/src/main"
$ go run wc.go master sequential pg-*.txt
# command-line-arguments
./wc.go:14: missing return at end of function
./wc.go:21: missing return at end of function
```

The compilation fails because we haven't written a complete map
function (`mapF()`) and reduce function (`reduceF()`) in `wc.go` yet.
Before you start coding read Section 2 of the [MapReduce
paper](https://tddg.github.io/cs475-fall21/public/papers/mapreduce_osdi04.pdf).
Your `mapF()` and `reduceF()` functions will differ a bit from those
in the paper's Section 2.1. Your `mapF()` will be passed the name of a
file, as well as that file's contents; it should split it into words,
and return a Go slice of key/value pairs, of type `mapreduce.KeyValue`.
Your `reduceF()` will be called once for each key, with a slice of all
the values generated by `mapF()` for that key; it should return a
single output value. 

> * **Hint:** a good read on what strings are in Go is the [Go Blog
on strings](https://blog.golang.org/strings).
* **Hint:** you can use `strings.FieldsFunc` to split a string into components.
* **Hint:** the strconv package ([http://golang.org/pkg/strconv/](http://golang.org/pkg/strconv/)) is handy to convert strings to integers etc. 


You can test your solution using: 

```sh
$ cd "$GOPATH/src/main"
$ time go run wc.go master sequential pg-*.txt
master: Starting Map/Reduce task wcseq
Merge: read mrtmp.wcseq-res-0
Merge: read mrtmp.wcseq-res-1
Merge: read mrtmp.wcseq-res-2
master: Map/Reduce task completed
14.59user 3.78system 0:14.81elapsed
```

The output will be in the file "`mrtmp.wcseq`". You can remove the
output file and all intermediate files with: 

```sh
$ rm mrtmp.*
```

Your implementation is correct if the following command produces the
following top 10 words: 

```sh
$ sort -n -k2 mrtmp.wcseq | tail -10
he: 34077
was: 37044
that: 37495
I: 44502
in: 46092
a: 60558
to: 74357
of: 79727
and: 93990
the: 154024
```

To make testing easy for you, run: 

```sh
$ sh ./test-wc.sh
```

and it will report if your solution is correct or not. 


## Part III: Distributing MapReduce tasks

Your current implementation runs the map and reduce tasks one at a
time. One of MapReduce's biggest selling points is that it can
automatically parallelize ordinary sequential code without any extra
work by the developer. In this part of the lab, you will complete a
version of MapReduce that splits the work over a set of worker
threads that run in parallel on multiple cores. While not distributed
across multiple machines as in real MapReduce deployments, your
implementation will use RPC to simulate distributed computation. 

The code in mapreduce/master.go does most of the work of managing a
MapReduce job. We also supply you with the complete code for a worker
thread, in `mapreduce/worker.go`, as well as some code to deal with RPC
in `mapreduce/common_rpc.go`. 

Your job is to implement `schedule()` in `mapreduce/schedule.go`. The
master calls `schedule()` twice during a MapReduce job, once for the
Map phase, and once for the Reduce phase. `schedule()`'s job is to hand
out tasks to the available workers. There will usually be more tasks
than worker threads, so `schedule()` must give each worker a sequence
of tasks, one at a time. `schedule()` should wait until all tasks have
completed, and then return.

`schedule()` learns about the set of workers by reading its
`registerChan` argument. That channel yields a string for each worker,
containing the worker's RPC address. Some workers may exist before
`schedule()` is called, and some may start while `schedule()` is running;
all will appear on `registerChan`. `schedule()` should use all the
workers, including ones that appear after it starts.

`schedule()` tells a worker to execute a task by sending a
`Worker.DoTask` RPC to the worker. This RPC's arguments are defined by
DoTaskArgs in `mapreduce/common_rpc.go`. The File element is only used
by Map tasks, and is the name of the file to read; `schedule()` can
find these file names in mapFiles.

Use the `call()` function in `mapreduce/common_rpc.go` to send an RPC to
a worker. The first argument is the the worker's address, as read
from registerChan. The second argument should be `"Worker.DoTask"`. The
third argument should be the `DoTaskArgs` structure, and the last
argument should be nil.

Your solution to Part III should only involve modifications to
`schedule.go`. If you modify other files as part of debugging, please
restore their original contents and then test before submitting.

To test your solution, you should use the same Go test suite as you
did in Part I, except swapping out `-run Sequential` with `-run
TestBasic`. This will execute the distributed test case without worker
failures instead of the sequential ones we were running before: 

```sh
$ go test -run TestBasic mapreduce/...
```

> * **Hint:** [RPC package](https://golang.org/pkg/net/rpc/) documents
> the Go RPC package. 
> * **Hint:** `schedule()` should send RPCs to the workers in parallel so
> that the workers can work on tasks concurrently. You will find the
> go statement useful for this purpose; see [Concurrency in
> Go](https://golang.org/doc/effective_go#concurrency). 
> * **Hint:** `schedule()` must wait for a worker to finish before it
> can give it another task. You may find Go's channels useful. You
> may find [sync.WaitGroup](https://golang.org/pkg/sync/#WaitGroup) useful. 
> * **Hint:** The easiest way to track down bugs is to insert print
> state statements (perhaps calling `debug()` in `common.go`), collect
> the output in a file with `go test -run TestBasic > out`, and then
> think about whether the output matches your understanding of how
> your code should behave. The last step (thinking) is the most
> important. 
> * **Hint:** To check if your code has race conditions, run Go's
> [race detector](https://golang.org/doc/articles/race_detector) with
> your test: `go test -race -run TestBasic > out`.

> **NOTE:**  The code we give you runs the workers as threads within
> a single UNIX process, and can exploit multiple cores on a single
> machine. Some modifications would be needed in order to run the
> workers on multiple machines communicating over a network. The RPCs
> would have to use TCP rather than UNIX-domain sockets; there would
> need to be a way to start worker processes on all the machines; and
> all the machines would have to share storage through some kind of
> network file system. 


## Part IV: Handling worker failures

 In this part you will make the master handle failed workers.
MapReduce makes this relatively easy because workers don't have
persistent state. If a worker fails, any RPCs that the master issued
to that worker will fail (e.g., due to a timeout). Thus, if the
master's RPC to the worker fails, the master should re-assign the
task given to the failed worker to another worker.

An RPC failure doesn't necessarily mean that the worker didn't
execute the task; the worker may have executed it but the reply was
lost, or the worker may still be executing but the master's RPC timed
out. Thus, it may happen that two workers receive the same task,
compute it, and generate output. Two invocations of a map or reduce
function are required to generate the same output for a given input
(i.e. the map and reduce functions are "functional"), so there
won't be inconsistencies if subsequent processing sometimes reads one
output and sometimes the other. In addition, the MapReduce framework
ensures that map and reduce function output appears atomically: the
output file will either not exist, or will contain the entire output
of a single execution of the map or reduce function (the lab code
doesn't actually implement this, but instead only fails workers at
the end of a task, so there aren't concurrent executions of a task). 

> **NOTE:** You don't have to handle failures of the master; we will
> assume it won't fail. Making the master fault-tolerant is more
> difficult because it keeps persistent state that would have to be
> recovered in order to resume operations after a master failure.

Your implementation must pass the two remaining test cases in
`test_test.go`. The first case tests the failure of one worker, while
the second test case tests handling of many failures of workers.
Periodically, the test cases start new workers that the master can
use to make forward progress, but these workers fail after handling a
few tasks. To run these tests: 

```sh
$ go test -run Failure mapreduce/...
```


## Part V: Inverted index generation (optional challenge)

Word count is a classical example of a MapReduce application, but
it is not an application that many large consumers of MapReduce use.
It is simply not very often you need to count the words in a really
large dataset. For this challenge exercise, we will instead have you
build Map and Reduce functions for generating an inverted index.

Inverted indices are widely used in computer science, and are
particularly useful in document searching. Broadly speaking, an
inverted index is a map from interesting facts about the underlying
data, to the original location of that data. For example, in the
context of search, it might be a map from keywords to documents that
contain those words.

We have created a second binary in `main/ii.go` that is very similar to
the wc.go you built earlier. You should modify `mapF` and `reduceF` in
`main/ii.go` so that they together produce an inverted index. Running
`ii.go` should output a list of tuples, one per line, in the following
format: 

```sh
$ go run ii.go master sequential pg-*.txt
$ head -n5 mrtmp.iiseq
A: 16 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-metamorphosis.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
ABC: 2 pg-les_miserables.txt,pg-war_and_peace.txt
ABOUT: 2 pg-moby_dick.txt,pg-tom_sawyer.txt
ABRAHAM: 1 pg-dracula.txt
ABSOLUTE: 1 pg-les_miserables.txt
```

If it is not clear from the listing above, the format is: 

```sh
word: #documents documents,sorted,and,separated,by,commas
```

For full credit on this optional task, you must pass `test-ii.sh`,
which runs: 

```sh
$ sort -k1,1 mrtmp.iiseq | sort -snk2,2 mrtmp.iiseq | grep -v '16' | tail -10
women: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-metamorphosis.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
won: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-metamorphosis.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
wonderful: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
words: 15 pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-metamorphosis.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
worked: 15 pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-metamorphosis.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
worse: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
wounded: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
yes: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-metamorphosis.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
younger: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
yours: 15 pg-being_ernest.txt,pg-dorian_gray.txt,pg-dracula.txt,pg-emma.txt,pg-frankenstein.txt,pg-great_expectations.txt,pg-grimm.txt,pg-huckleberry_finn.txt,pg-les_miserables.txt,pg-moby_dick.txt,pg-sherlock_holmes.txt,pg-tale_of_two_cities.txt,pg-tom_sawyer.txt,pg-ulysses.txt,pg-war_and_peace.txt
```


## Running all tests

You can run all the tests by running the script `src/main/test-mr.sh`.
With a correct solution, your output should resemble: 

```sh
$ sh ./test-mr.sh
==> Part I
ok  	mapreduce	3.234s

==> Part II
Passed test

==> Part III
ok  	mapreduce	2.026s

==> Part IV
ok  	mapreduce	10.462s

==> Part V (challenge)
Passed test
```

Note that your Zeus tests may show different execution times than
what is shown above. 

Please post questions on Ed.



## Point distribution

<p><table>
<tr><th>Test</th><th>Points</th></tr>
<tr><td>Part I (Sequential)</td><td>5</td></tr>
<tr><td>Part II (./test-wc.sh)</td><td>5</td></tr>
<tr><td>Part III (TestBasic)</td><td>5</td></tr>
<tr><td>Part IV (Failure)</td><td>5</td></tr>
</table></p>

Your code will be tested on Autolab. No marks will be awarded if your
code does not pass the test. You will receive full marks only if your
code successfully passes the test.



## What (and how) to hand in


### Submitting on Autolab

You must turn in your lab assignment using
[Autolab](https://autolab-gmu-systems.org).  Read this
[document](https://docs.google.com/document/d/1bwWx8p4_vSaNwVPzImRVKOXuj58qVMWWYu89Ejigs4A/edit?usp=sharing) 
for instructions on how to sign-up for Autolab. If you did not
receive a confirmation email from Autolab to set a password, enter
your @gmu.edu (**NOT** masonlive) email, and click "Forgot your
password" to get a new password. You may skip the Autolab signup 
step if you have done this in lab0.

Create a **tar** file of only the following Go source files,
`common_map.go`, `common_reduce.go`, `wc.go`, and `schedule.go`.
Please, .tar only, not .tgz, nor .7z/.zip.  Name your tar file as
`lab1-handin.tar`. 

```sh
% tar -cvf lab1-handin.tar common_map.go common_reduce.go schedule.go wc.go
```

**Please do not put any directory in your tar file
as our autograder is scripted to directly fetch src files not
directories.** Use the following command to examine the content 
of your tar file:

```sh
% tar -tvf lab1-handin.tar
-rw-r--r--  0 yue    staff    5781 Aug 30 16:38 common_map.go
-rw-r--r--  0 yue    staff    2911 Aug 30 16:38 common_reduce.go
-rw-r--r--  0 yue    staff    2911 Aug 30 16:38 schedule.go
-rw-r--r--  0 yue    staff    2911 Aug 30 16:38 wc.go
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
% git commit -am 'the final awesome solution of lab1: [Your Name] and [Your GMU email]'
```


## Acknowledgment

The lab assignment is adapted from MIT's 6.824 course. Thanks to
Frans Kaashoek, Robert Morris, and Nickolai Zeldovich for their
support.


