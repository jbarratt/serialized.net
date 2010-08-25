--- 
wordpress_id: 64
layout: post
title: perl one-liner; 'randline'
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=64
---
<p>I have a directory on my hard drive which has a fair quantity of multimedia content. Occasionally, my listening choices (ok, I gave it away) get somewhat stale. Scrolling through a list of -- well, quite a few choices can be somewhat daunting. I thought "hey! What if I chose entries at random for consideration! I&#39;m sure there&#39;s a what I can do that with some shell mumbo-jumbo."</p>

<p>Turns out to be possible, but you need some sed fu, and why not use perl.</p>

<p>So now I can type</p>

<pre>ls | randline</pre>


<p>and get a random selection from the list. If I want to go all crazy and not even screen it, I can go with </p>

<pre>xmms -e `ls | randline`.</pre>


<p>Here&#39;s the .bashrc entry I used:</p>

<pre>alias randline=&quot;perl -we &#39;srand ; rand(\$.) &lt; 1 &amp;&amp; (\$line = \$_) while &lt;STDIN&gt;; chomp(\$line); print \&quot;\$line\\n\&quot;;&#39;&quot;</pre>


<p>The &#39;guts&#39; came from an entry in the <a href="http://www.unix.org.ua/orelly/perl/cookbook/ch08_07.htm">Perl Cookbook</a>, worth checking out due to their description of how it actually works. It&#39;s a neat idea. Basically, you look at each line one by one. Each time you do you have a 1/(line number) chance of choosing that line as the one you&#39;ll output. So if you just have one line, you&#39;ve got a 1/1 chance of choosing that line. If you have 2, you start out with a taking the first line, and then having a 1/2 chance of picking the second -- giving you 50/50 odds you&#39;ll have either the first or the second. It extends nicely.</p>

<p>Anyway, thanks to this, I&#39;m currently listening to a cd I forgot I bought years ago which I&#39;m really digging, fit my mood perfectly. <a href="http://www.amazon.com/exec/obidos/ASIN/B000003BKZ">The Blue Moods of Spain</a></p>
