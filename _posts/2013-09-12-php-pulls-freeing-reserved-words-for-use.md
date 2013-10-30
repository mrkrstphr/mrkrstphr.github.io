---
layout: post
title: ! 'PHP Pulls: Freeing Reserved Words for Use?'
excerpt: An interesting RFC has appeared for PHP that could potentially free up reserved words for use in userspace.
categories:
- Development
- Technology
tags:
- Ideas
- PHP
- pull requests
- upcoming
status: publish
type: post
published: true
---
A developer named [bwoebi](https://github.com/bwoebi) has submitted
[a pull request](https://github.com/php/php-src/pull/438) to PHP that would allow - in certain circumstances -
previously reserved words to be used as class and method names.

His example shows a class named <code>List</code> with a method of <code>default()</code>.

This would be pretty sweet and has been, at times, something that has frustrated me when I've wanted a method
called something like `list`, but had to go with `getList()` instead due to `list` being a reserved word.

The only problem with this implementation is that it leads to a lot of ambiguity and unintended consequences, such
as writing a function named `list` and then not knowing if the one should call the built-in `list` or the user
function.

I'm very interested to see where this goes. I'd definitely support it in the case of Class and Method names.
