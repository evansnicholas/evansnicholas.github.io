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
I feel like I have memorized rather than constructed out of basic building blocks.  This feeling has always been a source of worry 
because one of the tenets of UNIX is that its fundamental constructs (such as pipes) allow programmers to build
complex programs by easily composing together small modular ones.  I was convinced that something essential was missing from
my understanding of UNIX and that this was preventing me from grasping it as a coherent whole.  The UNIX-HATERS handbook 
soothed this worry.  Of course, the book has a sarcastic tone, but as Dennis Ritchie (one of the creators of UNIX) notes in the 
"anti-foreword", the sentiment that lurks under the sarcasm may actually be one of fondness.  The editors of this book (which is 
actually a narrated compilation of posts to the UNIX-HATERS mailing list) have spent a lot of time with UNIX, 
and have certainly got to know it well. 

The handbook guides the reader through all the fundamental features of UNIX and exposes in each one serious design 
and implementation flaws.  The opening chapter provides an explanation for the lack of coherency that I experience while using
UNIX.  UNIX lacks homogeneity precisely because it favours the composition of small modular programs into complex ones.  
Many of the UNIX tools are written by different authors, following different coding conventions. This leads to UNIX commands with
cryptic names and inconsistent command line parameters.

This duality is a recurrent theme of the book: the authors find fault with the very aspects of UNIX that are supposed to be 
its main strengths. The chapter on "CSH, pipes and find" for instance criticizes the popular idea that UNIX's structure 
promotes the composition of simple programs into complex systems. The authors are also critical of the UNIX programming 
environment.  For them c and c++ are reminders of the progress that has been made with garbage collected languages 
and modern build tools. This is a good antidote for anyone suffering from nostalgia for the good old times when real 
programmers managed their own memory. 

But this book offers more than a critical tour of UNIX's fundamentals. Beyond the jokes and sarcasm lies a 
reflection on the different philosophies that guide the design of software. Throughout the book the authors 
contrast two school of thought, which are formulated most elegantly by Richard P Gabriel in appendix c. On the other 
hand we have UNIX, and on the other lisp based systems.  In a well known paper, Gabriel formulates this clash of philosphies as
"Worse is better" versus "The Right Thing".

Appendix: Unix and Linux

