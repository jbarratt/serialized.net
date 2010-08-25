--- 
wordpress_id: 14
layout: post
title: Outbound SMTP blocks
excerpt: |
  

wordpress_url: http://wp.serialized.net/?p=14
---
<p>Stupid <span class="caps">ISP </span>tried to lock down outbound <span class="caps">SMTP, </span>ostensibly to shut down traffic from spammers and trojaned windows machines.</p>

<p>Thank goodness for <span class="caps">SSH.</span></p>



<pre>su -c &quot;ssh -L 25:localhost:25 user@mail.server.com&quot;</pre>



<p>Just repoint my mail client to use &#39;localhost&#39; as a server, and viola.</p>
