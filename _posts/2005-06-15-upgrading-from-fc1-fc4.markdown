--- 
wordpress_id: 69
layout: post
title: Upgrading from FC1->FC4
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=69
---
<p>I&#39;ve just upgraded my trusty laptop from Fedora Core 1 (FC1) to Fedora Core 4 (FC4), and thought I&#39;d use this to just note down a few of the problems I had and the solutions. The upgrade itself was pretty painless -- or would have been if I&#39;d not been lazy and skipped checking my media. <span class="caps">CHECK THE MEDIA.</span></p>

<p>First, yum wasn&#39;t installed. All this required was a trip to a fedora <span class="caps">FTP </span>server and a download of yum and some support staff:<br />
python-elementtree<br />
python-urlgrabber<br />
python-sqlite</p>

<p>Next, evolution came up showing no internal icons whatsoever. (all the spaces where they would have been were jarring &#39;red x&#39; icons like you see when IE can&#39;t load an image.)<br />
The fix, after some judicious strace&#39;ing, was that for some reason there were no &#39;stock&#39; links in the gnome icons tree. I just populated them with the &#39;hicolor&#39; stock links and Evolution is happy happy.</p>


<pre> cd /usr/share/icons/gnome ; for i in *[0-9]x* ; do pushd $i ; ln -s /usr/share/icons/hicolor/$i/stock/ ; popd ; done</pre>



<p>This is all still in progress -- the #1 priority was getting the latop to where I could use it, as it&#39;s my only work machine at the moment.</p>
