--- 
wordpress_id: 371
layout: post
title: "Quickie: Keeping my OSX downloads folder clean"
wordpress_url: http://serialized.net/?p=371
---
My Downloads folder consistently gets large and cluttered. I considered an app like <a href="http://www.noodlesoft.com/hazel">Hazel</a> but it seemed like overkill.

I added the following alias to my .bashrc file:
{% highlight text %}
alias clean_downloads="find ~/Downloads -mtime +30 -maxdepth 1 -print -exec rmtrash '{}' ';'"
{% endhighlight %}
which depends on the free tool '<a href="http://www.nightproductions.net/cli.htm">rmtrash</a>' being installed -- a command line way to elegantly move files to the OSX-standard trash can.

The nice thing is when I think 'that looks messy', I just run clean_downloads, and get a nice list of all the things that it just "trashed" -- so, worst case, I can go and fish it out right then and there. Actually a bit nicer than things magically getting trashed while I'm not looking.

