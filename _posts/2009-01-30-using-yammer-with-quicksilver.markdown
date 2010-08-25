--- 
wordpress_id: 223
layout: post
title: Using Yammer with Quicksilver
wordpress_url: http://serialized.net/?p=223
---
We use (and really like) <a href="http://yammer.com">Yammer</a> at <a href="http://mediatemple.net">work</a>.

I couldn't find a Quicksilver plugin for it, and I noticed that <a href="http://www.yammer.com/company/api">Yammer's API</a> was pretty similar to <a href="http://apiwiki.twitter.com/">Twitter's API</a>, so I checked to see if there was Quicksilver->Twitter integration. There was one (which I installed anyway, since I <a href="http://twitter.com/jbarratt">use twitter myself</a>) over at <a href="http://blog.codahale.com/2007/01/15/tweet-twitter-quicksilver/">codahale.com</a>.

So here it is, modified to work with Yammer's (different) API:

<a href="http://serialized.net/software/Yammer.scpt">Yammer.scpt</a>

<ol>
<li>Grab the file and put it in your ~/Library/Application Support/Quicksilver/Actions/ folder.</li>
<li>Rescan Quicksilver's catalog. (Easy way, click it's dock icon, then CMD-R.) If this does not work, try restarting Quicksilver.</li>
<li> Create the login name and password:
	<ol>
	<li>Start the Application Keychain Access (use quicksilver!)</li>
	<li>Create a new entry:<br />
		<b>Item name:</b> http://yammer.com<br />
		<b>Account name:</b> yourname@yourdomain.com<br />
		<b>Password:</b> If I have to tell you what goes here, stop reading now
	</li>
	</ol></li>
<li> Now start Quicksilver, type the period (<b>.</b>) to get a text entry box, type your text, <b>Tab</b>, and type <b>Yammer</b> (or as much of it as you need to.) The first time you'll probably be asked for confirmation to let the tool access the password. Say 'always allow' and enjoy.</li>
</ol>

