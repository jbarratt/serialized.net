--- 
wordpress_id: 66
layout: post
title: vim and CVS todo lists
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=66
---
<p>We keep our todo lists in <span class="caps">CVS </span>-- it&#39;s not as classy as using a web app, I guess, but it is nice to be able to edit what turns out to be a fairly rich space using the wonderfully powerful text-flingy-ness of vim.</p>

<p>The thing is, I used to always launch my todo list from a terminal, then keep that terminal around so I could commit and update. I suddenly realized that a simple :map would make things a lot easier -- since when I launch vim, it keeps the same working directory that it starts with.</p>

<p>So sticking this in my .vimrc:</p>

<pre>map &amp;lt;F8&amp;gt; :!cvs commit -m &quot;&quot;&amp;lt;CR&amp;gt;&amp;lt;CR&amp;gt;</pre>


<p>means by hitting F8 (arbitrary choice) I can commit the current document. Nice. If it gets to it I could add in the other features, like update, but for now I digs it.</p>
