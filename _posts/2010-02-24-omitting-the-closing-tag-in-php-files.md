---
layout: post
title: Omitting the Closing tag in PHP Files
excerpt: Omit closing tags in PHP except when in template (HTML) files!
categories:
- Programming
tags:
- PHP
- Zend Framework
status: publish
type: post
published: true
---

**Note from the future**: Please omit closing PHP tags. It only makes sense, and is part of the PSR-2 standard. Past
me who wrote this blog article was an idiot. Closing tags only make sense when templating.

At first when I saw the Zend Framework recommendation of omitting the closing tag in PHP files I thought it bizarre
and stupid. Afterall, you close every other syntactic character in programming: parenthesis, brackets, HTML tags, ifs,
loops, etc. So why wouldn't you close the PHP tag at the end of the document as well?

The Zend Framework reason is, of course, to white space data from being sent to the browser prematurely, which messes
up sending header information to the browser. My first thought was "this is lazy and encourages bad coding."

But the more I think about it, the more it makes sense. It isn't laziness, but a fool proof way to prevent a common
mistake from happening. And apparently the closing tag isn't required at all, so why not?

I don't think I'll be quick to adopt this practice, but maybe eventually, and maybe as I write new code. We'll see.
