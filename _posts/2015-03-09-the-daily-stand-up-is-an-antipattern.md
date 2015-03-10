---
layout: post
title: ! "The Daily Stand-up is an Antipattern"
excerpt: Declaring things an antipattern is the new declaring things dead. Thus, I declare the daily stand-up an antipattern.
categories:
- Development
tags:
- agile
- scrum
- standup
status: publish
type: post
published: true
---

![](/images/dilbert-standup.png)

The Daily Stand-up is a keystone of the [Scrum](http://en.wikipedia.org/wiki/Scrum_(software_development)) framework
that advocates for short, time-boxed (usually 15 minutes) daily meetings where all members of the scrum team answer
three basic questions:

 1. What did you do yesterday?
 2. What are you going to do today?
 3. What impediments have you encountered?

These meetings usually happen in the morning (hence "What did you do yesterday?") in front of the Scrum Board/Sprint
Backlog. They are not intended to be status meetings for management, but instead are a way for the scrum team to sync
up with one another.

If any impediments are announced in this meeting, they should not be solved during at that time, but instead trigger a
discussion after the meeting with the ScrumMaster and any other members who may be able to help remove the impediment.

## What did you do yesterday?

The first question of the daily stand-up is pretty pointless: What did you do yesterday?

Members of the team already know what I did yesterday, because they can see the completed items in the "DONE" column on
the Scrum Board. And since the board is supposed to be visible at all times to all members of the scrum team (lest they
leave to use the restroom), team members should be able to see me get up and physically move my task around the board.

If they don't happen to see me move my task around, they probably noticed my Pull Request being put into 
[GitHub](https://github.com), and various comments and discussions going back and forth on the code and feature. 
Additionally, we use [HipChat](https://hipchat.com), which receives notifications of GitHub activity as well. 

Given that, my team pretty much knows when I finish a task, and actively collaborates on whether that task is 
acceptable or not via the Pull Request.

If you're not using collaborative source control tools, such as GitHub, BitBucket, etc, then you're probably not ready
for this conversation. If you're interested in leaving your wild west, gun slinging ways behind, check them out.

## What are you going to do today?

This question is a little maddening, isn't it? I can tell you what I'm planning on doing today, like maybe finishing the
task that I just told you I was halfway through, and then grabbing the next (whatever that happens to be at that point
in the day).

Or maybe I'm going to be distracted by some big disaster I have to help fix, or maybe finishing that task is going to
take me longer than I thought. I wish I knew!

Regardless, the same things apply to the first question: the Scrum Board and GitHub tells you everything you need to
know about what I did, what I'm doing, and, in the case of the Scrum Board, what I'm hopefully going to be doing when
I move a task into the "DOING" column.

## What impediments have you encountered?

Waiting for the daily stand-up is a terrible way to deal with impediments. Sure, it's best to spend some time trying
to solve problems on your own, but when you've decided you're stuck and need some help overcoming any kind of
obstacle, you need to involve the ScrumMaster and/or the rest of the team as soon as possible. 

You definitely don't want to spend upwards of 8 hours waiting to tell the ScrumMaster of an impediment.

So, generally, by the time of the daily stand-up the next day, the ScrumMaster and/or the rest of the team should
already know of and be working on removing your impediment.

I've even seen some teams that have a blocked or impediment column or sign that goes over a story to identify
impediments visually. This easily let's the whole team know you've got problems.

## Why the big fuss?

Is 15 minutes really a big deal? Isn't it worth it just to sync up with the ScrumMaster and the rest of the team?

I have three general arguments against the daily stand-up:

 1. If the ScrumMaster and rest of the team are doing their jobs and paying attention, they already know the answers
 to the three stand-up questions.
 2. The stand-up usually ends up not being the first thing of the day as team members often don't make it to work at
 the same time due to many life things that happen in the morning or on the way to work: errands, appointments, late
 starts, etc. So they end up starting at 9:00. For those in the office at 8:00 or sooner, the meeting then serves as
 an interruption when they're busy getting stuff done.
 3. The stand-up often becomes abused as a status meeting, especially for ScrumMasters and managers who don't understand
 or respect the process. It becomes a mechanism for former micro-managers to have one opportunity per day to to get 
 people back under their thumbs.
 
The first tenant of the [Agile Manifesto](http://agilemanifesto.org/), the grand daddy document of all agile frameworks,
reads:

> Individuals and interactions over processes and tools

If we hold individuals and interactions to be so important, then we should value frequent interaction throughout the
day between members of the team and the ScrumMaster. Each member is not a silo, but a cog in a highly functioning 
machine (you're much more than that; I'm terrible with analogies) that needs to work cohesively with the rest of the
machine to function successfully. 

The daily stand-up is a bandage for a team that fails to properly communicate and observe throughout the iteration.

## So what, then?

My philosophy with Scrum has always been "do what works." I've never been a die hard scrum zealot; in fact, I strongly
advocate against that. I believe each iteration is a chance to experiment with something new, find what works, and 
abandon what doesn't. 

Frequent experimentation doesn't mean frequently changing the process. Most experiments will likely fail, and the team 
will revert back to whatever was in place before. However, each iteration is a chance to learn how to make the team 
better. 

If you think daily stand-ups are working great for your team -- and, more importantly, the team things that too -- 
stick with them. If you're curious what life would be without them, then abandon them for a couple iterations and 
objectively observe what happens.

The retrospective is a powerful tool for measuring an experiment and deciding whether a tweak to the process was a
good one. Poll each member of them team and reflect on how things went with the change in place. What was better? What
was worse? How did it feel?

Personally, I don't think stand-ups are necessary; maybe your team does. My only suggestion: experiment and find out 
for yourself.
