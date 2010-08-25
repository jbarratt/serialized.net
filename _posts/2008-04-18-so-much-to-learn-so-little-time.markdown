--- 
wordpress_id: 119
layout: post
title: So much to learn, so little time.
wordpress_url: http://wp.serialized.net/?p=119
---
<p>I am looking at my lists of <a href="http://www.goodreads.com/review/list/702622?shelf=to-read">books to read</a> and <a href="http://www.goodreads.com/review/list/702622?shelf=currently-reading">books I'm actually reading</a> and feeling a little overwhelmed by how much good stuff there is to do right now.</p>

<p>I'm learning <a href="http://www.linux-ha.org/">linux HA</a>, which is going well. I needed to do something that probably could have been done in a r1-style config, but I went with a <span class="caps">CRM </span>r2 style config because having owned that, I can do way more interesting things. One very useful model is the loadbalancerless cluster. For 'N' servers, assign 'N' IP's with heartbeat ensuring that each IP will be hosted <span class="caps">SOMEWHERE, </span>preferring a server that either (a) doesn't have one, or (b) has lower load. Set up all these IP's in Round Robin <span class="caps">DNS.</span> That way if you lose a server, the most lightly loaded server with grab it's additional <span class="caps">IP.</span> Availability and simple load balancing without additional hardware. (Obviously suitable to only certain kinds of workloads, but interesting.)</p>

<p>We're moving away from home-rolled configuration management, and <a href="http://reductivelabs.com/projects/puppet/">puppet</a> looks like the best next thing, which means it's finally time to learn Ruby so we can get more of our magic working.</p>

<p>And, now that we have more Solaris around,  I'm spending more time with <a href="http://en.wikipedia.org/wiki/DTrace">Dtrace</a> which means learning more about Solaris internals so I can learn true ninja status.</p>

<p>I'm pushing more and more into automated testing and deployment and making things repeatable, so there's always things to learn about packaging, testing, and deployment -- we have a test <a href="http://code.google.com/p/ganeti/">ganeti</a> cluster running which is helping with some of that.</p>

<p>So there's tons of data flying around and I want to be grabbing (and crunching), so we might as well be using the funding over at yahoo -- <a href="http://hadoop.apache.org/core/">hadoop</a> and <a href="http://wiki.apache.org/hadoop/Hbase">hbase</a> are on the list for sure.</p>

<p>Then of course visualization is a big deal when you're trying to understand data, so you'll see some books on <a href="http://processing.org/">processing</a> and I'm also really looking at <a href="http://nodebox.net/code/index.php/Home">nodebox</a>.</p>

<p>And the web is changing -- apache is looking more and more like your dad's big lumbering caddy compared to <a href="http://www.lighttpd.net/">lighttpd</a> and <a href="http://nginx.net/">nginx</a>. Toss <a href="http://en.wikipedia.org/wiki/Varnish_cache">varnish</a> and some horizontal filesystem magic in the form of <a href="http://www.danga.com/mogilefs/">mogilefs</a>.</p>

<p>And then there are things that are just cool -- I already wanted to learn <a href="http://www.haskell.org/">haskell</a> due to it being functional without parentheses eye-bleeding, and it being what parrot is based on -- but now I found out I <a href="http://www.flickr.com/photos/harmoniousmanic/2419342543/in/set-72157604581860717/">know one of the creators</a>.</p>

<p><span class="caps">WHY, </span>world, <span class="caps">WHY</span>? <span class="caps">WHY MUST YOU</span> BE SO <span class="caps">FILLED WITH AWESOME AND</span> SO <span class="caps">DEVIOD</span> OF <span class="caps">TIME</span>?</p>

<p>That is all.</p>
