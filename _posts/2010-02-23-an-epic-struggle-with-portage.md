---
layout: post
title: An Epic Struggle With Portage
excerpt: Portage is a hell of a drug.
categories:
- Linux
tags:
- Gentoo
- Linux
- Portage
status: publish
type: post
published: true
---

Today I embarked on an epic struggle with the Portage package manager attempting to update the packages on my system.
After I ran into what can only be described as a phantom package.

So I did an `emerge --pretend --update --deep --newuse world` today to see what new packages were available, and got something similar to this:

```text
$ sudo emerge --pretend --update --deep --newuse world

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild  NS   ] app-text/docbook-xml-dtd-4.2-r2 [4.1.2-r6, 4.3-r1, 4.4-r1, 4.5]
[ebuild     U ] app-emulation/virtualbox-modules-3.1.4 [3.1.2]
[ebuild I   U ] app-emulation/virtualbox-bin-3.1.4-r1 [3.1.2] USE="-rdesktop-vrdp%"
[ebuild     U ] dev-db/mysql-5.0.90-r2 [5.0.90-r1] USE="-test%"
[ebuild     U ] media-gfx/graphviz-2.26.0 [2.24.0-r2]
[ebuild     U ] x11-libs/wxGTK-2.8.10.1-r5 [2.8.10.1-r1]
[ebuild   R   ] gnome-extra/deskbar-applet-2.26.2-r1  USE="(-test%)"
```

I didn't want to mess with virtualbox or mysql, so I simply did another `--pretend` just to make sure none of the other packages pulled them as dependencies:

```text
$ sudo emerge --pretend --update --deep --newuse app-text/docbook-xml-dtd media-gfx/graphviz x11-libs/wxGTK gnome-extra/deskbar-applet

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild  NS   ] app-text/docbook-xml-dtd-4.2-r2 [4.1.2-r6, 4.3-r1, 4.4-r1, 4.5]
[ebuild     U ] media-gfx/graphviz-2.26.0 [2.24.0-r2]
[ebuild     U ] x11-libs/wxGTK-2.8.10.1-r5 [2.8.10.1-r1]
[ebuild   R   ] gnome-extra/deskbar-applet-2.26.2-r1  USE="(-test%)"
```

Nope, we're all good. So I went ahead and removed the pretend, then did another `emerge --pretend --update --deep --newuse world`:

```text
sudo emerge --pretend --update --deep world

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild  NS   ] app-text/docbook-xml-dtd-4.2-r2 [4.1.2-r6, 4.3-r1, 4.4-r1, 4.5]
[ebuild     U ] app-emulation/virtualbox-modules-3.1.4 [3.1.2]
[ebuild I   U ] app-emulation/virtualbox-bin-3.1.4-r1 [3.1.2] USE="-rdesktop-vrdp%"
[ebuild     U ] dev-db/mysql-5.0.90-r2 [5.0.90-r1] USE="-test%"
```

Eh? `app-text/docbook-xml-dtd`, didn't I already emerge you? Okay, whatever, I'll just emerge it by itself:

```text
$ sudo emerge --pretend --update --deep --newuse app-text/docbook-xml-dtd

These are the packages that would be merged, in order:

Calculating dependencies... done!
```

Hmm, okay, that's really odd. It is a new package that will not overwrite old packages, so maybe the
`--update` flag is confusing it. I'll just do a straight emerge on it:

```text
$ sudo emerge --pretend app-text/docbook-xml-dtd

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild   R   ] app-text/docbook-xml-dtd-4.5
```

Alright. Now I'm confused. Maybe mysql or virtualbox is pulling a specific version of `app-text/docbook-xml-dtd`. But
wouldn't that package be masked and I'd get a notice about it?

Well, if that's the case, it should show up when I try to update virtualbox and mysql, right?

```text
$ sudo emerge --pretend --update --deep --newuse app-emulation/virtualbox-modules app-emulation/virtualbox-bin dev-db/mysql

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild     U ] app-emulation/virtualbox-modules-3.1.4 [3.1.2]
[ebuild I   U ] app-emulation/virtualbox-bin-3.1.4-r1 [3.1.2] USE="-rdesktop-vrdp%"
[ebuild     U ] dev-db/mysql-5.0.90-r2 [5.0.90-r1] USE="-test%"
```

Wtf? Where is this package coming from? I give up. But just for kicks...

```text
$ emerge --pretend =app-text/docbook-xml-dtd-4.2-r2

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild  NS   ] app-text/docbook-xml-dtd-4.2-r2 [4.1.2-r6, 4.3-r1, 4.4-r1, 4.5
```

Remove the `--pretend` and the package installs fine. Well that was difficult and nonsensical.
