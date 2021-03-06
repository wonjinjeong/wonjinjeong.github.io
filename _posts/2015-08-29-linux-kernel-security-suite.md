---
layout: post
section-type: post
title: Linux Kernel Security Suite
category: tech
tags: [ 'linux', 'kernel', 'opensource' ]
published: false
---
Rootkits.
A word that gives whitehats goosebumps and makes sys admins cry in their [shower](https://www.youtube.com/watch?v=QhBr0lPNuuU).
In a nutshell, rootkits are binaries that are executed in the [kernel](https://en.wikipedia.org/wiki/Protection_ring) of the OS, which practically means they are a God and your machine is their pet.
Of course in order to add a rootkit in the kernel you need root permissions, but social engineering and 0-days make this two or three pieces of cake.

Let's see what a typical rootkit will do:

  - Hide files
  - Hide processes
  - Install malicious software (and nobody will notice because of 1 and 2 :smile:)
  - Steal certificates and passwords
  - Log all the activity of your machine

All of the above are realized by altering the system call table.
Scary stuff, right?
In [Mr. Robot](https://www.imdb.com/title/tt4158110/?ref_=nv_sr_1) rootkits are described as

 > A crazy serial rapist with a very big dick.

There are ways to protect yourself from them, but of course it's a mouse and cat game that never ends.

A few years ago my job was to protect linux servers from rootkits and later I kept digging deeper as a hobby, and it's about time to open source this work.

The suite includes the following (whitehat) rootkits:

## The Drip Dry Carbonite

Protects the system call table.
In case of an attempt of modifying it, snapshots of the processes running in the system are logged remotely and the machine gets frozen <small>(that's why it's called Carbonite)</small>.

## Dresden

Blocks all the attempts to insert rootkits in the kernel, dumps their instruction memory and logs a critical message.

## Netlog

Logs all network communication by probing the inet stack of the kernel.

In the future I will post about some interesting snippets of the source code.
The repo lives [here](https://github.com/PanosSakkos/linux-kernel-security-suite) and don't forget to star.

<iframe src="https://ghbtns.com/github-btn.html?user=panossakkos&repo=linux-kernel-security-suite&type=star&count=true&size=large" frameborder="0" scrolling="0" width="160px" height="30px"></iframe>
