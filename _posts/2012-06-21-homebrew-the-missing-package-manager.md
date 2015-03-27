---
layout: post
title: ! 'Homebrew, The Missing Package Manager for OS X'
excerpt: ! 'Mac OS X finally has a decent package manager available: homebrew'
categories:
- Mac
tags:
- Mac
- OSX
- Package Manager
- Software
- Utilities
status: publish
type: post
published: true
---
One of my biggest (of many) complaints about the Mac operating system is the lack of a package manager. Package
managers are my favorite feature of most Linux operating systems. They make it easy to find, install, customize and
keep up-to-date most software available for the architecture.

Sure, there's the App Store now, but that's a piece* and doesn't have most of the apps I would want anyway. I'm mostly
talking server/command line stuff: Apache, PHP, MySQL, PostgreSQL, wget, etc. There are a
[multitude](http://www.macports.org/)multitude</a> of [options](http://www.finkproject.org/) for
[third party](http://www.netbsd.org/docs/software/packages.html)
[package managers](http://www.gentoo.org/proj/en/gentoo-alt/bsd/index.xml) for OS X, some of them more glamorous and
some of them more tedious, but I haven't really enjoyed using many of them. None of them seemed as nice, powerful or
reliable as package managers on various Linux flavors.

Then I found [Homebrew](http://mxcl.github.com/homebrew/). And there was much rejoicing.

Homebrew is a package manager for Mac. It is essentially a system for pulling down source code, configuring and making
it, much the same way Gentoo's Portage works. It's not much different from pulling down source and compiling it
yourself, except it does it for you, and if you're like me, it's smarter and probably does it better than I would
myself. And it also provides a mechanism for letting me know when there's new updates and allowing me to upgrade
those packages. It's perfect.

It does depend on having Xcode installed. If you're on OS X Lion, it's available in the App Store.

The first thing you should do is run:

```bash
brew doctor
```

Follow all the advice given here and fix all the issues. Trust me. Two days of headaches and agony may follow if you
don't. Then, to install a package, such as PostgreSQL:

```bash
brew install postgresql
```

Boom. Done. Follow the notes given on the screen (if you miss them, type <code>brew info postgresql</code> to recall them) and you're good to go. Want to see if any packages have updates?

```bash
brew update
```

To update any package listed in the available updates:

```bash
brew upgrade [package-name]
```

It's that simple. And I like simple.

<sub>* Seriously, am I the only one that has issues deleting some applications? Or issues trying to download some, or
perhaps apps that somehow get marked as hidden without you doing the hiding? Or just blank buttons next to apps that
fail to download? Wtf Apple?</sub>
