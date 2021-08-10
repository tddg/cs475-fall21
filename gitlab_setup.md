---
layout: page
title: "GitLab Setup for CS 475 Labs"
permalink: /gitlab_setup.html
---

# Introduction

This walk-through should help you setup the tools you'll need for
this class, and it should familiarize you with the workflow you'll
use for development and hand-in.
We will be using [Mason GitLab](https://git.gmu.edu/users/sign_in)
for all the CS 475 programming assignments. 

You'll need to complete these steps:

1. Login to your Mason GitLab account.
2. Find the `cs475-fall21` group on GitLab.
3. Fork the `cs475-fall21/labs` repo to a private repo under your own GitLab user.
4. Set your fork of the repo to `Private` visibility.
5. Create an SSH public/private key pair on Zeus.
6. Configure Git on Zeus.
7. Clone your private repo to a local repo on Zeus.
8. Take a look at [Lab 0](./lab0.html) and see if you can make a
local commit and push to your private GitLab repo.
9. Start learning a bit of Go.


> **IMPORTANT:** To create a new repository.
You must **fork** the original project GitLab repository
and then immediately change the permission of the project to
`Private`.
**Leaving your repository publicly visible violates GMU Honor Code
and CS Department's Honor Code policies and thus is treated as
cheating!**


# Detailed Lab Setup Steps

Detailed lab setup process is listed below:

## Step 1: Create a GitLab Repo

First, you will need to login to <a
href="https://git.gmu.edu/users/sign_in">GitLab</a> by clicking
`Sign in with: GMU Login`. *Username and Password never work as 
it is for non-mason users.*

## Step 2: Fork the cs475-fall21/labs Repo

Find the `cs475-fall21` group on GitLab, and fork the `cs475-fall21/labs`
repo to a private repo under your own GitLab user. 

## Step 3: Make Your Forked Repo Private

Immediately set your fork of the `labs` repo to "Private" visibility.
To do so, go to your forked project, from `Settings`, go to
`General`, from there change `Visibility` level to `Private`.

## Step 4: Clone Your Private Repo

Finally, to start development you'll have to do some further setup
on a computing environment (preferably one of our student.cs Zeus Linux
server or a cloud virtual machine server). Assume you are working on 
[Zeus](https://labs.vse.gmu.edu/index.php/Systems/Zeus), shell back
into one of the Zeus machines.

```bash
% ssh zeus.vse.gmu.edu
```

> **Note:** You don't need to install Go on Zeus as Go is already
installed. You may like to read more on [how to install
Go](https://golang.org/doc/install) wherever you like to setup your
development environment.


### Step 4.1: Set up a public/private key pair

(If you've already got a public/private key pair you use for SSH that
you'd like to keep using you can skip this step. If that doesn't mean
much to you then you'll need to take a few more steps.)

Before cloning your private repo to your computing environment,
you will need to first create an RSA SSH key by typing:

```bash
% ssh-keygen -t rsa -C "your_email"
```

This should create two files: `~/.ssh/id_rsa`, which you should never
share with anyone and `~/.ssh/id_rsa.pub`, which you can add to
GitLab to give you access to your repos.

Or you can follow 
<a href="https://git.gmu.edu/help/ssh/README#generating-a-new-ssh-key-pair">this tutorial</a>
to create a public/private key pair.

Then, add the public key to your GitLab account.

```bash
% cat ~/.ssh/id_rsa.pub
```

Copy the contents of that file. In GitLab go to your `User Setting` 
page and click `SSH Keys`, give the key a title, and paste the
contents you copied into the Key box. Once you've done this git on
Zeus should be able to access your repos on GitLab.

Or you can follow
<a href="https://git.gmu.edu/help/ssh/README#adding-an-ssh-key-to-your-gitlab-account">these instructions</a> for adding an SSH key to your GitLab account.


### Step 4.2: Set up Git

Tell git about the email address you used on GitLab and your name,
if you haven't done this already:

```bash
% git config --global user.email <your_email>
% git config --global user.name <your_name>
```

Now you'll want to 'clone' your private repo from GitLab.  Click on
the **Clone** button at the right-top corner of your GitLab repo's
webpage, copy the string under **Clone with SSH** to clipboard.
Then, create a new directory called <tt>cs475-fall21</tt> under your
`$HOME` directory, `cd` to your working directory, and clone your
repo on to your Linux box (make sure to sub in your GID here for
`<your_gid>`):

```bash
% mkdir $HOME/cs475-fall21
% cd $HOME/cs475-fall21
% git clone git@git.gmu.edu:<your_gid>/labs.git

Cloning into 'labs'...
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (12/12), done.
Receiving objects: 100% (13/13), 10.19 KiB | 10.19 MiB/s, done.
remote: Total 13 (delta 0), reused 0 (delta 0), pack-reused 0

% ls
labs/
```

If that all works you should now have a new directory called `labs/`
with all of the labs' skeleton code inside.



## Git Version Control Workflow

So far, we've seen three Git repos of code. The first two were on
GitLab (i.e., cloud) with a third in our home directory on Zeus.  For
most students this is probably starting to seem complicated so let's
take a step back and think about what they are each for and how each
can/should be used.

The first, is the cs475-fall21/labs repo on GitLab. This repo
contains the basis for all the projects. You'll never have to modify
this repo or `push` anything to it - it just supplies the basis for
the class.  There may be times when this gets updated to deal with
bugs in the lab code; in these cases instructions will be sent out
about how to merge those changed into your lab work with as little
pain as possible.

The second repo was the private GitLab repo you created under your
own user. This serves a few purposes. It's a place where you can
permanently store or `push` code that you'd like to make sure stays
safe. It also provides a place where you can push your code changes
for me to pick up for grading.

Finally, the third repo you created was a clone of the second into
your home directory on Zeus. This is the repo where you'll do your
development and make your commits. Once you're happy with how you've
set things up there, or if you want to make sure your data is safely
stored off of the Zeus machine then you can `push` the commits you've
made to your local Zeus repo over to your private GitLab repo.



## Developing Go

You'll want to walk through the [Go
Tour](https://tour.golang.org/welcome/1) after you've got a working
installation. The tour is interactive - so it'll give you an easy way
to play with the language and get a sense of how it differs from C,
C++, Java, etc.

[Effective Go](https://golang.org/doc/effective_go) is an optional
but interesting read that will undoubtedly improve your Go code - it
details nearly all of the more nuanced aspects of the language.

It may be helpful to read [How to Write Go
Code](https://golang.org/doc/code), which outlines the details of the
`go` tool, though we'll only use a subset of its capabilities for the
labs. Go has a stylized way to run, compile, and test code - this
document gives the full details of how it works.


## Step 5: Check in Your Initial Source Code

Now, check in the source code which you have already copied into the git directory:

```bash
% git add *
% git commit -m "init commit" 
% git push  
```

If you are checking in for the first time on an empty repo (which is your case here),
you should run:

```bash
% git push -u origin master
```

Instead of

```bash
% git push
```

## Step 4: Share the Repo with Your Teammate

If you are working in a group, it is a good idea for all group
members to share access to a single GitLab repo. You can enable
sharing through the GitLab web interface. Hover over to **Settings**
on the left sidebar, and click **Members**. Enter your team member's
Patriot ID and choose a role (Developer or Maintainer) for him/her.


