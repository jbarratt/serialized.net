--- 
wordpress_id: 223
layout: post
title: Using Yammer with Quicksilver
wordpress_url: http://serialized.net/?p=223
---
We use (and really like) [Yammer](http://yammer.com) at [work](http://mediatemple.net).

I couldn't find a Quicksilver plugin for it, and I noticed that [Yammer's API](http://www.yammer.com/company/api) was pretty similar to [Twitter's API](http://apiwiki.twitter.com/), so I checked to see if there was Quicksilver->Twitter integration. There was one (which I installed anyway, since I [use twitter myself](http://twitter.com/jbarratt)) over at [codahale.com](http://blog.codahale.com/2007/01/15/tweet-twitter-quicksilver/).

So here it is, modified to work with Yammer's (different) API:

[Yammer.scpt](http://serialized.net/software/Yammer.scpt)

<ol>
<li>Grab the file and put it in your ~/Library/Application Support/Quicksilver/Actions/ folder.</li>
<li>Rescan Quicksilver's catalog. (Easy way, click it's dock icon, then CMD-R.) If this does not work, try restarting Quicksilver.</li>
<li> Create the login name and password:
	<ol>
	<li>Start the Application Keychain Access (use quicksilver!)</li>
	<li>Create a new entry:
		**Item name:** http://yammer.com
		**Account name:** yourname@yourdomain.com
		**Password:** If I have to tell you what goes here, stop reading now
	</li>
	</ol></li>
<li> Now start Quicksilver, type the period (**.**) to get a text entry box, type your text, **Tab**, and type **Yammer** (or as much of it as you need to.) The first time you'll probably be asked for confirmation to let the tool access the password. Say 'always allow' and enjoy.</li>
</ol>

