--- 
wordpress_id: 30
layout: post
title: Serious rsync timesaver
wordpress_url: http://wp.serialized.net/?p=30
---
I needed a copy of fedora core 3 for a machine I'm building, so I broke out the handy bittorrent link and zinged it to my colocated server. That was nice -- I got about 800k/sec sustained during the download. What was also nice is that the MD5 sums matched -- good files to start with.

Then I downloaded them to our local fileserver (in case we need them again, plus I've managed to stuff my laptop full to the brim with data... again.) Problem: MD5sums no longer match. D'oh. (I'm not sure what causes them to barf, but interestingly, this happened last time I did basically the exact same scenario with fedora core 1!)

So, instead of downloading them again:

{% highlight console %}
$ rsync -c --existing --stats -e ssh server:download/heidelberg-binary-i386/*.iso .
{% endhighlight %}

Let rsync figure out exactly what portions of the file are screwed up, and then let it correct them for you! The trick turned out to be the '-c' flag, which forces it to do a checksum on the files. (Otherwise, since all the other metadata matches, it won't bother diffing.)

It ended up taking about 5 minutes to correct both .iso's -- trivial, compared to the time it would have taken to download them again (possibly with more errors, meaning another download, etc.)
