--- 
wordpress_id: 110
layout: post
title: "Quick LAME tip: concatenate mp3's"
wordpress_url: http://wp.serialized.net/?p=110
---
<p>If you want to join, append, or concatenate multiple <span class="caps">MP3</span>&#39;s without packing them into a container (like &#39;mp3join&#39; does), you can do so with &#39;find&#39; and &#39;lame.&#39; This example also transcodes down to a mono file (-a) and drops the bitrate to 96 (-b 96) -- I was converting spoken word from iTunes default rips.</p>

<p><code>find . -name &#39;*.mp3&#39; -exec lame --decode &#39;{}&#39; - &#39;;&#39; | lame -a --tt &quot;Title&quot; --tl &quot;Album&quot; --ta &quot;Artist&quot; -b 96 - concatenat.mp3</code></p>

<p>I was annoyed enough by trying to solve this via google that I figured I&#39;d cache it here for myself and the rest of humanity.</p>
