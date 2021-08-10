---
layout: page
title: "Lab 0: Intro to Go"
permalink: /lab0.html
---

## Important Dates and Other Stuff

**Due** Thursday, 09/02, midnight.


## Introduction

In this warm-up assignment, you will solve two short problems as
a way to familiarize yourself with the Go programming language. We
expect you to already have a basic knowledge of the language. If
you're starting from nothing, we highly recommend going through the
[Golang tour](https://tour.golang.org/list) before you begin this
lab.

## GitLab Repo

We use Go's package `testing` for automated testing. You'll fetch
(fork or download) the initial software with
[git](https://git-scm.com/) (a version control system).  To learn
more about git, look at the [git user's
manual](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/user-manual.html),
or, if you are already familiar with other version control systems,
you may find this [CS-oriented overview of
git](https://eagain.net/articles/git-for-computer-scientists/)
useful.

The URL for the course git repository is
[https://git.gmu.edu/cs475-fall21/labs](https://git.gmu.edu/cs475-fall21/labs).
Refer to [GitLab Setup](./gitlab_setup.html) for instructions of
creating a new **Private** project repository on our Mason GitLab
server.  You'll commit you changes locally and push them to your
private GitLab repo for grading. 

## Software

You will find the code in the same directory as this readme. The two
problems that you need to solve are in `q1.go` and `q2.go`. You should
only add code to places that say `TODO: implement me`. Do not change
any of the function signatures as our testing framework uses them.  

**Q1 - Top K words:**
The task is to find the `K` most common words in a given document. To
exclude common words such as "a" and "the", the user of your program
should be able to specify the minimum character threshold for a word.
Word matching is case insensitive and punctuation should be removed.
You can find more details on what qualifies as a word in the comments
in the code. 

**Q2 - Parallel sum:**
The task is to implement a function that sums a list of numbers in a
file in parallel. For this problem you are required to use goroutines
(the `go` keyword) and channels to pass messages across the goroutines.
While it is possible to just sum all the numbers sequentially, the
point of this problem is to familiarize yourself with the
synchronization mechanisms in Go. 

## Testing

Our grading uses the tests in `q1_test.go` and `q2_test.go` provided to
you. To test the correctness of your code, run the following: 

```bash
% cd intro-go
% go test
```

If all tests pass, you should see the following output: 

```bash
% go test
PASS
ok	/path/to/intro-go  0.009s
```

To run a single test (e.g., `Test1`), run the following:

```bash
% go test -run Test1
--- FAIL: Test1 (0.00s)
    q2_test.go:11: Sum of q2_test1.txt failed: got 0, expected 499500

FAIL
exit status 1
FAIL	intro-go	0.001s
```

Apparently, the above test fails due to a certain reason. It is your
job to finish the implementation and pass the test.

Please post questions on Piazza.

## Point distribution

<p><table>
<tr><th>Test</th><th>Points</th></tr>
<tr><td>Q1Simple</td><td>4</td></tr>
<tr><td>Q1DeclarationOfIndependence</td><td>4</td></tr>
<tr><td>Q2_1</td><td>3</td></tr>
<tr><td>Q2_2</td><td>3</td></tr>
<tr><td>Q2_3</td><td>3</td></tr>
<tr><td>Q2_4</td><td>3</td></tr>
</table></p>


## What (and how) to hand in

You will submit your assignment using GitLab. The submission will
consist of whatever is contianed in your **private**
`intro-go` repository.

1. You will need to share your **private** repository with our GTA Michael (his GitLab ID
is the same as his mason email ID: `mcrawsha`).

2. Commit all your changes by typing:

	```
	% git commit -am 'the final awesome solution of proj0b: [Your Name] and [Your GMU ID]'
	```

And that's all. We will check the timestamp (your last commit
timestamp) for late submission. So make sure to submit before the
deadline.


