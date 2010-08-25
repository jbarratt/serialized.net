--- 
wordpress_id: 45
layout: post
title: Xnest remote KDE desktop
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=45
---
<p>New trick for today -- sometimes I need to remotely modify <span class="caps">KDE </span>desktops, and messing around with <span class="caps">CLI </span>config files only goes so far. If I <span class="caps">SSH </span>into the system, then</p>

<p>/usr/X11R6/bin/Xnest :1 &amp; export <span class="caps">DISPLAY</span>=`hostname`:1 ; startkde</p>

<p>then I get a desktop.</p>

<p>This begs a feature -- ssh macros. Openssh already has escape characters. If, in a config file, we could define strings that got dumped to the command line based on a sequence of characters (say, above, ~Mxnest) that&#39;d make the sysadmin&#39;s lot a much happier one. (Alternative is running everything from one host with ssh just reaching out and executing remote commands, or having the same .bashrc on every machine.) I don&#39;t see people out there talking about it, though it&#39;s a feature in some of the <span class="caps">GUI </span>ssh tools.</p>
