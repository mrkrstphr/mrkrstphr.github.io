---
layout: post
title: Using Ditz to Track Issues
excerpt: Ditz allows you to manage issues via the command line. Now I never have to leave my terminal again!
categories:
- Linux
- Reviews
- Tools
tags:
- command line
- Linux
- management
- Reviews
- Tools
status: publish
type: post
published: true
---
[Ditz](http://ditz.rubyforge.org/) is an issue tracking system for command line junkies. For those of us who live
on the command line already, using vim, psql or mysql, svn, bzr or git, this makes us feel right at home tracking
our issues. And I love it.

Ditz creates a directory within your project (or anywhere else you want it) and a config file in the project
directory. Each issue then becomes a file in this directory. A powerful command line interface allows you to setup
releases and components, and assign those to issues, as well as edit, complete and comment on issues. It even
provides the ability to generate HTML files of issues.

The only thing Ditz doesn't allow you to do is have your information stored in a remote location, which pretty much
kills effective project collaboration as one would have to commit their Ditz directory ASAP, and other developers
would have to update their working copy before managing issues.

But for solo projects, it's perfect for command line junkies to track bugs, tasks and features for their project. 
