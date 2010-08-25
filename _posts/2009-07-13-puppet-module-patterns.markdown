--- 
wordpress_id: 273
layout: post
title: Puppet Module Patterns
wordpress_url: http://serialized.net/?p=273
---
I've been playing a lot with Puppet recently, and have been really focusing on getting a core set of modules in place. I found myself using a few module patterns over and over, so I thought I'd write them down and keep an updated list.

<h2><tt>/base</tt> and <tt>/site</tt></h2>

Our puppet modules are stored in 2 directories: <tt>/base</tt> and <tt>/site</tt>. Here are the purposes:

<h4><tt>/base</tt></h4>

The best way to think of what goes in <tt>/base</tt> is:

<blockquote>
I should be able to (and should!) post my <tt>/base</tt> tree to github or my blog, and it would be useful to other people.
</blockquote>

Examples of things that might go into base are:

<ul>
<li>virtuozzo</li>
<li>nginx</li>
<li>lighttpd</li>
<li>perlbal</li>
<li>debrepo</li>
<li>rpmrepo</li>
<li>openvpn</li>
<li>mysql</li>
<li>memcached</li>
</ul>

Essentially, the environments that make daemons or applications useable.

<h4><tt>/site</tt></h4>

Site should be 

<blockquote>This should not be in any way useful (or safe to share) outside my company</blockquote>

Good examples might be:

<ul>
<li>mywebapp</li>
<li>mystatshud</li>
<li>mymonitoringsystem</li>
</ul>

<h2> Module Design </h2>

Any functionality we want to do on a server can more or less be defined as:

<blockquote>I want to set up some applications and an environment in a certain way, so that it does what I want</blockquote>

<h3> Sample application: <tt>mywebapp</tt></h3>

So, for example, if we wanted to build an <tt>mywebapp</tt> module, we'd need certain things to make that work.
Let's assume that we need
<ul>
<li>lighttpd installed and configured in a certain way</li>
<li>apache installed and configured in a certain way</li>
<li>some perl libraries installed</li>
<li>some cron jobs set up</li>
</ul>

and so on.

<h3>The obvious way</h3>

The way I started out solving these sorts of problems turns out to not scale all that well.
As I started a module (to solve a specific problem) I would just start listing out the puppet resources it would need.

<b><tt>mywebapp/manifests/init.pp</tt></b>
[sourcecode language="plain"]
package { &amp;quot;lighttpd&amp;quot;: ensure =&amp;gt; installed }
service { &amp;quot;lighttpd&amp;quot;: ensure =&amp;gt; running, enabled =&amp;gt; true }
package { &amp;quot;lib-catalyst-rest-perl&amp;quot;: ensure =&amp;gt; installed } 
package { &amp;quot;apache2&amp;quot;: ensure =&amp;gt; installed }
service { &amp;quot;apache2&amp;quot;: ....
[/sourcecode]

and so on.

This breaks down in several ways. 

<h4>Things can only be defined once in puppet</h4>

First, puppet only lets us define a resource once. So if we decide we want a new tool that needs the package <tt>lib-catalyst-rest-perl</tt> installed, or needs to use apache to host some stats HUD,
we'd have a problem.

<b><tt>statshud/manifests/init.pp</tt></b>
[sourcecode language="plain"]
...
package { &amp;quot;apache2&amp;quot;: ensure =&amp;gt; installed }
...
[/sourcecode]

Puppet will freak.

<p style="color: red">
Puppet::Parser::AST::Resource failed with error ArgumentError: Duplicate definition: 
Package[apache2] is already defined in file statshub/manifests/init.pp at line 1; 
cannot redefine at statshub/manifests/init.pp:2 on node mondrian
</p>

<h4>Things would get repeated a lot</h4>

As a practical example, I refer you to the our lighttpd config.

It has an SSL config file, in which we find this little gem:

[sourcecode language="plain"]
ssl.cipher-list = &amp;quot;DHE-RSA-AES256-SHA DHE-RSA-AES128-SHA EDH-RSA-DES-CBC3-SHA 
    AES256-SHA AES128-SHA DES-CBC3-SHA DES-CBC3-MD5 RC4-SHA RC4-MD5&amp;quot;
[/sourcecode]

Neat! This implements the current best practices for what ciphers we should accept.

So having this in a <tt>base/</tt> module, which is used by everything that needs to spin up a lighttpd, means that all my stuff will be running in the safest way I know how to run it.

The alternative is not very pleasant: I'd need to copy and paste this block in every one of my modules that used lighttpd. I'd need to keep it updated in each one of those files if and when a cipher turns out to be insecure.

Similarly, I can consolidate a lot of intelligence like this:
<ul>
<li>making sure log rotation is correct</li>
<li>making sure compression cache files are cleaned</li>
<li>making sure monit is configured to restart it if it dies</li>
<li>making sure file permissions are set up right</li>
<li>....</li>
</ul>

and, should we learn something New And Improved about any of these things, viola, all our useage of that module gets improved.

As an added benefit, this style of consolidation maps onto the way Puppet itself is going.
Much like Perl has the CPAN, Puppet Modules will be getting some new features that allow them to be centralized. Soon we will be able to get "the puppet mysql module" rather than "some guy's cloned module from a cloned module on github".

<h2>Module Patterns</h2>

Because of the above threats and virtues, we want to push as much functionality as we can into base modules.

<h3>Basic Design</h3>

<img src="http://serialized.net/wp-content/uploads/2009/07/base_site_modules.png" alt="Puppet Module Designs" title="Puppet Module Designs" width="284" height="194" class="alignnone size-full wp-image-279" />

So how this looks in practice in puppet-speak:

<b><tt>mywebapp/manifests/init.pp</tt></b>
[sourcecode language="plain"]

// configure lighttpd module
include lighttpd

// configure apache module
include apache

// configure mysql module
include mysql

// set up crons
cron {
    command =&amp;gt; &amp;quot;....&amp;quot;
}

[/sourcecode]


<h3>Configuring other Classes</h3>

Ok, great, I've shipped all that intelligence to another module. But how do I make it work the way I want it to?

Well, we want to configure other classes. There are 3 basic methods to configure a class:

<ul>
<li>Create subclasses and only include one or some of them</li>
<li>Create 'defines' that we use from inside classes</li>
<li>Set variables before you include it</li>
</ul>

<h4>Technique #1: Create subclasses and only include one or some of them</h4>

I'll keep this one simple:

A lot of the time there are several distinct modes you might want to use an app in. For example, there's a big difference between

[sourcecode language="plain"]
include mysql
[/sourcecode]

and 

[sourcecode language="plain"]
include mysql::server
[/sourcecode]

<h4>Technique #2: Create 'defines' that we use from inside classes</h4>

This technique is the most commonly used one. Many of the application and daemon classes we want to configure have lots of "parts" that can be enabled. Some examples:
<ul>
<li>monit config chunks</li>
<li>apache/lighttpd/nginx sites</li>
<li>subversion repos</li>
</ul>

These are best handled with defines. A good full-strength version of this is in our monit config:

[sourcecode language="plain"]
define conf ( $source = '', $content = '' ) {
    if $source != '' {
        file { &amp;quot;/etc/monit.d/$name&amp;quot;:
            notify =&amp;gt; Service[&amp;quot;monit&amp;quot;],
            source =&amp;gt; $source,
            mode =&amp;gt; 644,
            owner =&amp;gt; root,
            group =&amp;gt; root,
        }
    } else {
        file { &amp;quot;/etc/monit.d/$name&amp;quot;:
            notify =&amp;gt; Service[&amp;quot;monit&amp;quot;],
            content =&amp;gt; $content,
            mode =&amp;gt; 644,
            owner =&amp;gt; root,
            group =&amp;gt; root,
        }
    }
}
[/sourcecode]

Notable features:
[sourcecode language="plain"] define conf ( $source = '', $content = '' ) [/sourcecode]

This is useful because we can define a config file via either source or content.

I can call this from another module like:

[sourcecode language="plain"]
include monit::common
monit::common::conf { &amp;quot;lighttpd-monit&amp;quot;: source =&amp;gt; &amp;quot;puppet:///lighttpd/lighthttpd-monitrc&amp;quot; }
[/sourcecode]

or, if I wanted to template it:

[sourcecode language="plain"]
include monit::common
monit::common::conf { &amp;quot;apache2&amp;quot;: content =&amp;gt; template(&amp;quot;apache2/apache2-monitrc.erb&amp;quot;) }
[/sourcecode]

There's no reason for us not to actually help people out and write common configs for them. This is easy with extra defines.

[sourcecode language="plain"]
define simple_service ($pidname = $name) {
    file { &amp;quot;/etc/monit.d/$name&amp;quot;:
        notify =&amp;gt; Service[&amp;quot;monit&amp;quot;],
        content =&amp;gt; template(&amp;quot;monit/simple_service.erb&amp;quot;),
        mode =&amp;gt; 644,
        owner =&amp;gt; root,
        group =&amp;gt; root,
    }
}
[/sourcecode]

[sourcecode language="plain"]
include monit::common
monit::common::simple_service { &amp;quot;apache2&amp;quot;: pidname =&amp;gt; &amp;quot;apache2&amp;quot; }
[/sourcecode]

<h4>Technique #3: Set variables before you include the class</h4>

A good example might be for the mywebapp, I only want apache2 to be listening to the <tt>lo</tt> loopback interface. This would be a dumb default behavior if someone just did
[sourcecode language="plain"]
include apache2
[/sourcecode]

I would probably want it listening on all the public interfaces, by default.

So I can specify configuration variables:

In 

<b><tt>site/mywebapp/manifests/init.pp</tt></b>
[sourcecode language="plain"]

// we only want to have apache2 listening on the loopback
$apache2_interface = &amp;quot;lo&amp;quot;

include apache2
[/sourcecode]

Then, in 

<b><tt>base/apache2/manifests/init.pp</tt></b>
[sourcecode language="plain"]
// if someone set this variable before they included our class, use it
if($apache2_interface) {
    $interface = $apache2_interface
} else {
    // by default use '*' which means listen on all interfaces
    $interface = &amp;quot;*&amp;quot;
}
[/sourcecode]


I like the idea of a consistent prefixing of configuration variables -- for example, all config variables for module 'foo' would be '$foo_....'

Sometimes including a class doesn't make sense unless we have some variable defined at all -- there's no reasonable default.
Let's say we're configuring our local syslog -- the server we syslog to. Without defining that, it doesn't make sense to include the module.

We get this pretty much for free, actually. If you specify in your documentation:

<blockquote>
You must specify a value for <tt>$syslog_server</tt> before including this module
</blockquote>

<b><tt>base/syslog-client/manifests/init.pp</tt></b>
[sourcecode language="plain"]
    file { &amp;quot;/etc/syslog.conf&amp;quot;:
        content =&amp;gt; template(syslog-client/syslog.conf.erb),
    }
[/sourcecode]

<b><tt>base/syslog-client/templates/syslog.conf.erb</tt></b>
[sourcecode language="plain"]
#
# Remote Logging
#

destination d_remote {
        tcp(&amp;quot;&amp;lt;%= syslog_server %&amp;gt;&amp;quot;, port(514));
};
[/sourcecode]

So if someone tries to 
[sourcecode language="plain"]
include syslog-client
[/sourcecode]
without defining that variable, puppet will error for you.

<p style="color: red">
Failed to parse template syslog.conf.erb: Could not find value for 'syslog_server'
</p>

<h2>Conclusions</h2>

I'll keep this page updated as more useful patterns emerge.

Thinking about decomposing modules by <tt>/base</tt> and <tt>/site</tt> is a powerful model already, as it helps develop new variants on ideas in a really rapid way with little to no copy and paste.

I've found that my <tt>site</tt> modules are often in the 4-7 lines long range, and I automatically get a lot of my hard-earned "best ways to do things" along for the ride.

And since I'm not the only one making things around here, everyone else saves time (and avoids some n00b mistakes) by being able to develop things more quickly as well.
