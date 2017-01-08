---
layout: post
title: Reflect after Reading - The UNIX-HATERS Handbook
description: Description
---
First things first: I don't hate UNIX.  To the contrary, I started reading this book because I wanted to learn more about UNIX 
(actually I wanted to learn about Linux, see the appendix for more on this point).  Over time I have acquired some basic UNIX skills: 
I can write basic shell scripts, I can find what I am looking for with grep, I can monitor running processes...  But these
skills feel more like tricks than knowledge based on a thorough understanding of the underlying system. UNIX is 
meant to be "simple and elegant".  But in my experience to date it has always felt confusing and disjointed.  Every command I use
I feel like I have memorized rather than constructed out of basic building blocks.  This feeling has always worried me 
because one of the tenets of UNIX is that its fundamental constructs (such as pipes) allow programmers to build
complex programs by easily composing together small modular ones.  I was convinced that something essential was missing from
my understanding of UNIX and that this was preventing me from grasping it as a coherent whole.  The UNIX-HATERS handbook 
soothed this worry.  Of course, the book has a sarcastic tone, but as Dennis Ritchie (one of the creators of UNIX) notes in the 
"anti-foreword", the sentiment that lurks under the sarcasm may actually be one of fondness.  The editors of this book (which is 
actually a narrated compilation of posts to the UNIX-HATERS mailing list) have spent a lot of time with UNIX, 
and have certainly got to know it well. 

The handbook guides the reader through the fundamental features of UNIX and exposes in each one serious design 
and implementation flaws.  The lack of coherency that I have experienced while using UNIX is addressed in the very first
chapter.  UNIX lacks homogeneity precisely because it promotes the proliferation of small programs that "do one thing and do it
well".  Many of the UNIX tools are written by different authors, following different coding conventions. This leads to UNIX commands 
with cryptic names and inconsistent command line parameters.  Would you know what the **awk** command does?  
Or **fsck** or **nroff**?  Could you imagine what the 18 different possible parameters to **ls** are,
the simple program for listing the files in a directory?  The handbook was first published in 1994 and it is reassuring to see that 
progress has been made since then.  The UNIX file system for instance takes a particularly strong beating in the handbook. One of the
most fundamental criticisms is that files do not have version numbers and that "file deletion is forever".  The advent of version 
control systems (such as git), have since largely addressed this problem.

## Worst is Better vs. The Right Thing: Competing design philosophies

But this book offers more than a critical tour of UNIX fundamentals. Beyond the jokes and sarcasm lies a 
reflection on the different philosophies that guide the design of software. Throughout the book the authors 
contrast two schools of thought, which are formulated most elegantly by Richard P. Gabriel in appendix C. On the one hand there is
the "Worse is Better" school of which UNIX is an example, and on the other "The Right Thing" which is associated with Common Lisp 
and CLOS (Common Lisp Object System).  Both these schools of thought can be characterized by the approach they take to the following
four values: simplicity, correctness, consistency and completeness. The "The Right Thing" school is idealistic and does not compromise:

* The design be simple in both implementation and interface.  
* The design must be correct in all observable aspects.
* The design must not be inconsistent.
* The design must cover as many important situations as is practical.

The "Worst is Better" approach is more pragmatic.  Simplicity of implementation is the most important consideration.  Simplicity of 
interface, correctness, consistency and completeness can all be sacrificed if they threaten it.  Completeness is the least important
consideration and should be abandoned if any of the other values are overly threatened.  

Creating software according to "The Right Thing" approach is a long and difficult process, but once complete results in 
something beautiful and functional.  On the other hand, "Worst is Better" produces software that both Gabriel and the authors 
of the handbook compare to a virus: a basically good version of the software can be created quickly.  It will have just enough
features to be useful and because it is still fairly small it will spread fast (it will be easily portable to different 
environments).  Once it has spread and it has a large user base it will start to improve making it harder and harder to dislodge.
Eventually it will offer most of the functionality of the software produced by the "The Right Thing" approach.  It is interesting
to note the parallels between "Worst is Better" and the Agile movement's notion of a "Minimum Viable Product".  Both emphasize
the importance of quickly producing working software at the expense of feature completeness. 

## Appendix: Unix and Linux

* * *

### References
* [The UNIX Philosophy](https://en.wikipedia.org/wiki/Unix_philosophy)
* Richard P. Gabriel, [Lisp: Good News, Bad News, How to Win Big](https://www.dreamsongs.com/WIB.html)
