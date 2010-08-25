--- 
wordpress_id: 16
layout: post
title: MonkeyTesting with perl and xte
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=16
---
I was trying to test the snot out of our application today, and whipped up the follwing quick hack:

<a href="http://serialized.net/monkeytest.pl.txt">monkeytest.pl</a>

Basically, it uses from the <a href="http://hoopajoo.net/projects/xautomation.html">xautomation</a> package from <a href="http://hoopajoo.net/">hoopajoo.net/</a> to quickly generate x events -- keys presses and mouse moves and clicks. It uses the xwininfo application to get the x,y coordinates of the frame which contains the application, so it can restrict mouse movements to the appropriate application.

<p>There are several caveats and 'look out for's --
1) THIS IS SERIOUSLY DANGEROUS. It doesn't do any sanity checking to make sure, for example, the application didn't crash or didn't get dragged or window scrolled or... before spewing events at it. You might be spewing events to your root window, or applications below this one, or (....) whatever. You could screw up your system, you could lose data. 'Nuf said.</p>

2) It doesn't handle a lot of generally useful things to test. For example, you either hit, say, CTRL, **or** you hit a character or a number. It would be good to test holding down modifier keys while hitting other keys.

Results -- well, basically the reason it has the above issues is because super early testing suggested it's not very useful. I did prove that the application is relatively difficult to crash. (e.g. I ran for 90 seconds, but spewing into a Gimp window caused a segfault within 3 seconds.) However, it's no doubt crashable -- I just need to stop being lazy and get back in the 'intelligent testing' mindset.
