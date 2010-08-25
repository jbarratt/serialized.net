--- 
wordpress_id: 259
layout: post
title: Managing volatile files with puppet
wordpress_url: http://serialized.net/?p=259
---
I'm managing <a href="linux-ha.org">Linux HA</a> (heartbeat2) from <a href="http://reductivelabs.com">Puppet</a> and I had a problem.
There is a file called 'cib.xml' used by heartbeat which I needed to manage. For a variety of boring reasons, when you make changes to this file, you must have a number in it which is the highest the
app has ever seen.

To make things easy, I use the current timestamp for this, as I've never seen that number go backwards.

Ok, not the normal kind of thing you do with Puppet, but it can be done. We just need to know what time it is in the template.

Thankfully Puppet lets us define custom facts, so here we go...
in the module's plugins/facter directory, I created 'epoch_time.rb'.

[sourcecode language="ruby"]
Facter.add(&quot;epoch_time&quot;) do
        setcode do
                Time.now.to_i
        end
end
[/sourcecode]

Then I could use that in templates/heartbeat/cib.xml I said

[sourcecode language="xml"]
 &lt;cib admin_epoch=&quot;&lt;%= epoch_time %&gt;&quot;&gt;
[/sourcecode]

Well, that works fine, except for one little issue.

When puppet manages a file it does this:
<ol>
<li>Renders out the templates and takes an md5sum of that</li>
<li>Sends that to the client, which checks it's own md5sum</li>
<li>If the sums are different, the client overwrites the file with the old one</li>
<li>The client then updates anything 'subscribed' to that file (for example, restarting a service.)</li>
</ol>

So since puppet runs about every 30 minutes, that's not good. I don't want to be reloading the CIB database every 30 minutes. That's bad.

This is what we get:
<pre>
debug: File[/etc/ha.d/ha.cf]/checksum: Initializing checksum hash
debug: File[/etc/ha.d/ha.cf]: Creating checksum {md5}18530322762561ce59f1d414340b4c43
debug: File[/etc/ha.d/cib.xml]/checksum: Initializing checksum hash
debug: File[/etc/ha.d/cib.xml]: Creating checksum {md5}a02f0ca8a3cce64d7913faa3268e530b
debug: File[/etc/ha.d/cib.xml]/content: Executing 'diff /etc/ha.d/cib.xml /tmp/puppet-diffing20090415-3486-ska653-0'
1c1
< <cib admin_epoch="1239318580">
---
>  <cib admin_epoch="1239815009">
debug: File[/etc/ha.d/cib.xml]: Changing content
debug: File[/etc/ha.d/cib.xml]: 1 change(s)
</cib></pre>


So here was my solution, and as inelegant as it is, it pretty much works.

<ol>
<li>Manage a file called 'cib.xml-puppet' instead</li>
<li>In this file, set admin_epoch="0"</li>
<li>When THAT file changes, do a chained action which sets the epoch time correctly, and writes out the actual cib.xml file</li>
<li>Run the command to reload the CIB database in that and only that case.</li>
</ol>

So the puppet code to make this happen:
[sourcecode language="ruby"]
        exec { &quot;add-epoch-cib-xml&quot;:
                command =&gt; &quot;sed 's/admin_epoch=\&quot;0\&quot;/admin_epoch=\&quot;${epoch_time}\&quot;/' /etc/ha.d/cib.xml-puppet &gt; /etc/ha.d/cib.xml &amp;&amp; cibadmin -R -x /etc/ha.d/cib.xml&quot;,
                path =&gt; [&quot;/bin&quot;, &quot;/usr/sbin/&quot;],
                subscribe =&gt; File[&quot;/etc/ha.d/cib.xml-puppet&quot;],
                refreshonly =&gt; true,
        }
        file { &quot;/etc/ha.d/cib.xml-puppet&quot; :
                type =&gt; 'file',
                ensure =&gt; 'present',
                content =&gt; template(&quot;ha_nfsroot/heartbeat/cib.xml&quot;);
        }
[/sourcecode]

So I still get to use my very first custom fact (it gets interpolated into the 'sed' command) but I also don't run that terrifying update every time.

If any puppetmasters or Linux HA gurus want to tell me exactly how I'm doing it wrong I would love to hear that. This certianly doesn't feel "elegant."
However, since it feels "functional", functionality trumps elegance for today.
