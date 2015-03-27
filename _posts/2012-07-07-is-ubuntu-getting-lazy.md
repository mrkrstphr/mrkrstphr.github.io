---
layout: post
title: Is Ubuntu Getting Lazy?
excerpt: I've had so many issues with Ubuntu upgrades lately that I'm starting to wonder if they're even trying anymore.
categories:
- Linux
- Technology
tags:
- '12.04'
- precise
- Ubuntu
status: publish
type: post
published: true
---

After spending a year and half in a hell called MacBook, I'm finally back on Ubuntu, and couldn't be happier about
that fact. I installed Ubuntu 12.04. Since this version is a [LTS](https://wiki.ubuntu.com/LTS/), I figured it would
be a pretty stable build, especially since Ubuntu seems to take their time slipping new versions of software into
their repositories anyway. I thought this was because they wanted to make sure everything was thoroughly tested.
But after running into a kludge of issues, I'm wondering if it's just because their slow.

What I've run into so far:
 - **pidgin-sipe** is [busted](https://bugs.launchpad.net/ubuntu/+source/pidgin-sipe/+bug/947920). This is big for
 me, as I use my laptop in a corporate environment and have to be online so that I can interact and support my
 coworkers. This seems to be a regression issue caused with another library. Being a developer myself, I know what a
 pain in the ass it is to make code modifications in one place and have some other app blow up unexpectedly because
 of it. It's just impossible to test everything.
 - **evolution-mapi** apparently
 [can no longer send email](https://bugs.launchpad.net/ubuntu/+source/evolution-mapi/+bug/996381). This is another big
 deal for me as I have to be able to respond to emails. For now I'm stuck using Exchange's web interface in Chrome,
 which is an utter piece of horseshit.
 - Alt+Tab [stopped](https://bugs.launchpad.net/ubuntu/+source/compiz/+bug/975701)
 [working](https://bugs.launchpad.net/ubuntu/+source/unity-2d/+bug/955859). Seriously. Alt+Tab. Htf does this happen
 and not get caught?

Yes. I know. I only listed three issues. But I've only been using Ubuntu 12.04 for a couple days. Who knows what
else I'm going to find? The big kicker here is these bugs are 2-3 months old, and some of them are listed as
importance low. Alt+Tab not working is importance low? Do you understand how utterly fucking difficult it can be to
work without Alt+Tab after relying on it since you quit the pacifier?

This is exactly what people talk about when they bitch that Linux is too raw, too breakable and too error prone to
gain widespread adoption. My mother doesn't want to have to use hacks, scripts or recompile software just to get it
to work. When has something as simple and core as Alt+Fucking+Tab stopped working on Windows? It probably hasn't.
Because the developers would have noticed instantly.

This is sad. And I hope it isn't a sign of things to come with Ubuntu.
