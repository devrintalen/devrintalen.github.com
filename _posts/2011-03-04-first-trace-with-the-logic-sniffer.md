---
layout: post
title: First trace with the Logic Sniffer
tags:
- ADB
- crosstalk
- logic sniffer
- something worked
status: publish
type: post
published: true
meta:
  _edit_last: '19855582'
  jabber_published: '1299293831'
  _wpas_skip_twitter: '1'
---

![Logic Analyzer trace](/img/firsttrace.png)

Just got my first protocol trace using my new [Open Bench Logic
Sniffer][1]! Channel 0 is the ADB data wire, and you can see two
separate "talk" commands being sent by the Mega32. There's no mouse
connected, so there's no response. There's also a fair amount of junk
on channels 2-5 that looks like crosstalk on the probe wires (they're
not connected to anything and should be grounded).

[1]: http://dangerousprototypes.com/2010/02/25/prototype-open-logic-sniffer-logic-analyzer-2/
