--- 
wordpress_id: 238
layout: post
title: My approach to Inbox Zero with Mail.app
wordpress_url: http://serialized.net/?p=238
---
My setup is pretty straightforward, Mail.app speaking IMAP to our company Zimbra server.

I wanted to do [Inbox Zero](http://www.43folders.com/izero) because I was sick of losing track of things in the backlog, trying to be clever with flags and "mark as unread", etc.

My workflow now is to process my inbox down to zero, pulling the information I need to take actions on out with [this omnifocus technique.](http://www.earth2adam.com/omnifocus-gtd-actions-from-mail-redux/)

However, when I've processed a message, I don't want to trash it. I needed a way to archive them for later searching. So, I applescripted it.

I created this script in the script editor:
{% highlight text %}
    tell application "Mail"
        set theSelectedMessages to selection
        set myAccount to "zimbra"
        set myMailbox to "Archive"
        repeat with theMessage in theSelectedMessages
            move theMessage to mailbox myMailbox of account myAccount
        end repeat
    end tell
{% endhighlight %}

then placed it into the `~/Library/Application Support/Quicksilver/Scripts` directory.

I refreshed the catalog, then headed to the triggers view.

![Quicksilver Triggers](/images/triggers.jpg)

So, after processing a bunch of mail, I can select them all with ?-A, or just pick the ones I want normally (clicking, Shift-clicking, or ?-clicking).

Then I Shift-?-A, and boom, they get moved to my /Archive folder, where they sit, ready for me to search them if I need to. I do still delete messages that I'm really quite sure I won't be needing again.
