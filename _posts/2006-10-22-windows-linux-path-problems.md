---
layout: post
title: Windows -> Linux Path Problems
excerpt: Dealing with path issues developing Rails on Windows, and deploying on Linux.
categories:
- Linux
- Programming
tags:
- Linux
- Ruby on Rails
- Windows
status: publish
type: post
published: true
---

I had one hell of a time when I copied my Ruby on Rails application from my development machine (Windows XP,
Apache 2.2) to my production machine (Linux, Apache 1.3). I floundered through a series of errors and problems, but one
error really stumped me, and unfortunately I couldn't find much help on the net for it:

> Application error (Rails)

It took me forever to get past this problem, but I finally figured it out. The first line of a few files in the
`public/` folder contained a path to the Ruby binaries.

```ruby
#!C:/ruby/bin
```

For obvious reasons, this wasn't going to fly well on a *nix server. So I had to change it in the following files:
`dispatch.cgi`, `dispatch.fcgi`, `dispatch.rb`.

In my instance, it was changed to:

```ruby
#!/usr/local/bin/ruby
```

But obviously your server might be different. Try exploring `/usr/local` and  `/usr/bin` or using `locate ruby` to try
to find the correct path to Ruby.

After I made this modification the application worked fine.
