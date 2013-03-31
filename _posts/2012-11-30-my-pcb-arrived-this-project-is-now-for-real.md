---
layout: post
title: My PCB arrived. This project is now for real.
tags:
- ADB
- ohwell
- pcb
status: publish
type: post
published: true
meta:
  _edit_last: '19855582'
  _publicize_pending: '1'
  jabber_published: '1354328930'
---

It's arrived - my first printed board for ADBUSB (and only my second PCB ever). I'm pretty happy with how it turned out, and no complaints at all about the quality - BatchPCB continues to impress me.

![A printed circuit board](/img/adbusb_pcb_v01.jpg)

A few thoughts:

- The vias all have a silkscreen outline. Not sure why pcb decided to put that in. I should have caught that when looking at the gerbers.
- I put the minidin connector backwards on the board. I have no words for how dumb this was. I think this revision is pretty much dead in the water unless I can mount it upside down and have the same traces work, or I'll need to cut the lines and play around with them a bit. Live and learn!
- I had no idea just how tiny 0603 components are. I've seen balls of solder bigger than these.

*Update*: I used the wrong footprint for the Mega32. There's no salvaging this design, so it's back to the drawing board for now. Oh well.
