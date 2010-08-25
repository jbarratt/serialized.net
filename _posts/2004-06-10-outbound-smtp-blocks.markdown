--- 
wordpress_id: 14
layout: post
title: Outbound SMTP blocks
wordpress_url: http://wp.serialized.net/?p=14
---
Stupid ISP tried to lock down outbound SMTP, ostensibly to shut down traffic from spammers and trojaned windows machines.

Thank goodness for SSH.

`su -c "ssh -L 25:localhost:25 user@mail.server.com"`

Just repoint my mail client to use 'localhost' as a server, and viola.
