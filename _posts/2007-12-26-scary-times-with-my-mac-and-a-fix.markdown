--- 
wordpress_id: 112
layout: post
title: Scary times with my mac (and a fix!)
wordpress_url: http://wp.serialized.net/?p=112
---
<p>My macbook just scared the crap out of me.</p>

<p>Since I couldn't sleep until I fixed, I figured I'd blog it. (It's finishing up fixing itself now.)</p>

<p><strong>Symptom:</strong></p>

<p>Running Leopard, all normal updates. On 12/25/2007 I get a popup window with some macbook upgrades, a quicktime patch, and another innocuous looking update. However, after authorizing it to reboot the computer, I get the message "Reboot failed because application 'Software Update' failed to quit." This, I think, is a bad sign. I quit every other application and tried running software update again. Same thing happens. I then try and shut down. It won't. Power button's not even working to bring up the "shutdown" menu. Only thing I can do is hold down the power button and power it back up.</p>

<p>Here's the thing -- on next boot, I'm looking at the OS X Setup Assistant. Yeah. As if I'd just installed Leopard. Great.</p>

<p><strong>Fix:</strong></p>

<p>Thankfully, the fix was easy, after I got my heart rate back down from 210 <span class="caps">BPM.</span> I searched in the KB a bit and found: <a href="http://docs.info.apple.com/article.html?artnum=306998">Setup Assistant Appears after Every Restart</a></p>

<p>And that worked. Start it in safe mode, (hold shift right after power-on 'Bong!') when it gets to the login screen don't login, but hit back arrow, then restart, and viola -- upgrades continued like they should have, and on next reboot I was normal.</p>

<p>All's well that ends well, but this <span class="caps">DID </span>make me want to get my backups back in shape one way or another; no more waiting around for SuperDuper to get Leopard-ready. (Even though his latest update suggests I could be good to go within a week... it's rsync time, at least.)</p>

<p><strong>Edit</strong>: Turns out the rsync was is easy. <a href="http://jwz.livejournal.com/801607.html">Just listen to jwz.</a></p>
