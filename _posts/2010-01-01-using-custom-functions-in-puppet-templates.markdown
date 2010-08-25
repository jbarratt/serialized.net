--- 
wordpress_id: 353
layout: post
title: Using custom functions in Puppet templates
wordpress_url: http://serialized.net/?p=353
---
This eventually required a support ticket to figure out, so I'm documenting it here in case it's useful to others.

The problem: You have a puppet function you find useful. At this point custom functions are <a href="http://reductivelabs.com/trac/puppet/wiki/PluginsInModules">really easy to create and distribute via modules</a> so there's really no reason not to be using them. However, you want to use the result of a function in your templates.

A real-world example of this is sharing authentication information. (For example, crypted password hashes. Plain text, as always, would be evil no matter where it was living.)
You really have 3 choices:
<ul>
<li>Coding it right into your puppet code and checking that into your version control system</li>
<li>Not distributing that information with puppet</li>
<li>Using puppet to distribute the information, but store it outside of your puppet modules somehow.</li>
</ul>

We chose the third way, and store this kind of information in YAML files outside the puppet modules tree.
Anyway. So we want to access this info inside a template.

Assume you have a function called 'get_extdata'.
You normally call from your '.pp' manifests with something like
[ruby]
$data = get_extdata('mymodule', 'path:to:data')
[/ruby]

You can make a call like that from your template by doing this wonderful fragment of magic-wand-waving to get this function accessible:

In <b>mytemplate.erb</b>:
[code]
&lt;% Puppet::Parser::Functions.autoloader.loadall %&gt;
Now you can use the 'scope' variable to get at your function.
This is the equivalent of the call get_extdata('mymodule',  'path:to:data'):
&lt;%= scope.function_get_extdata('mymodule',  'path:to:data') %&gt;
[/code]

This works like a charm, and means you don't have to create a bunch of variables that you don't really need before you load the template.

So if you were creating an .htpasswd style file with puppet, you could do

In your <b>init.pp</b>
[ruby]
$users = ['steve', 'paul', 'stu']
[/ruby]

<b>htpasswd.erb</b>:
[code]
&lt;% Puppet::Parser::Functions.autoloader.loadall %&gt;
&lt;% users.each do |user| -%&gt; 
&lt;%= user %&gt;:&lt;%= scope.function_get_extdata('authmodule',  user) %&gt;
&lt;% end -%&gt;
[/code]
