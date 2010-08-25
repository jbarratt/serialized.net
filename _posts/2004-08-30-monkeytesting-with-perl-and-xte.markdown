--- 
wordpress_id: 16
layout: post
title: MonkeyTesting with perl and xte
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=16
---
<p>I was trying to test the snot out of our application today, and whipped up the follwing quick hack:</p>

<p><a href="http://serialized.net/monkeytest.pl.txt">monkeytest.pl</a></p>

<p>Basically, it uses from the <a href="http://hoopajoo.net/projects/xautomation.html">xautomation</a> package from <a href="http://hoopajoo.net/">hoopajoo.net/</a> to quickly generate x events -- keys presses and mouse moves and clicks. It uses the xwininfo application to get the x,y coordinates of the frame which contains the application, so it can restrict mouse movements to the appropriate application.</p>

<p>There are several caveats and &#39;look out for&#39;s --<br />
1) <span class="caps">THIS</span> IS <span class="caps">SERIOUSLY DANGEROUS.</span> It doesn&#39;t do any sanity checking to make sure, for example, the application didn&#39;t crash or didn&#39;t get dragged or window scrolled or... before spewing events at it. You might be spewing events to your root window, or applications below this one, or (....) whatever. You could screw up your system, you could lose data. &#39;Nuf said.</p>

<p>2) It doesn&#39;t handle a lot of generally useful things to test. For example, you either hit, say, <span class="caps">CTRL, </span><b>or</b> you hit a character or a number. It would be good to test holding down modifier keys while hitting other keys.</p>

<p>Results -- well, basically the reason it has the above issues is because super early testing suggested it&#39;s not very useful. I did prove that the application is relatively difficult to crash. (e.g. I ran for 90 seconds, but spewing into a Gimp window caused a segfault within 3 seconds.) However, it&#39;s no doubt crashable -- I just need to stop being lazy and get back in the &#39;intelligent testing&#39; mindset.</p>
