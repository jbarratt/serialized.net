--- 
wordpress_id: 389
layout: post
title: Capturing short-lived programs on Linux
wordpress_url: http://serialized.net/?p=389
---
One of the things I love most about DTrace is your <a href="http://www.brendangregg.com/DTrace/shortlived.d">ability to do things like this.</a> Because you can ask the kernel to let you start tracking when something cool happens (like forking a new program), you can instrument it.

[code light="true"]
// Measure parent fork time
syscall::*fork*:entry { /* save start of fork */
	self-&gt;fork = vtimestamp;
}
[/code]

Sadly, those tools aren't available on a standard issue Linux machine. (You can do it with <a href="http://sourceware.org/systemtap/wiki/systemtapstarters">SystemTap</a>, but it's not on every server I end up looking at.) Today I was trying to track down some processes that were making very odd DNS lookups. I isolated the user ID making these calls via iptables logging:

[code light="true"]
iptables -I OUTPUT 1 -m string --string &quot;BADZONE&quot; -d 127.0.0.1 -p udp --destination-port 53 --algo bm -j LOG --log-uid --log-prefix &quot;BADZONE: &quot;
[/code]

But this user's PHP scripts were getting launched and dying again quickly. How to find out what was going on inside them? This hacky little trick actually worked really well. You can download it from <a href="http://axis.serialized.net/gitweb/?p=utilities.git;a=blob_plain;f=auto_stracer;hb=HEAD">my git utilities directory.</a>

Basically, we check the process list as fast as we can for any owned by that user; If you find one, stick an strace on it. The script will 'hang around' until all the strace processes have finished. You end up with a directory full of trace files which you can then post-grep through to get what you need.

The short version is I was able to catch one of that user's programs doing the odd behaviour within about 30 seconds of running this tool, which was great!

[perl light="true"]
#!/usr/bin/perl

use warnings;
use strict;
use Getopt::Long;

my $opt = {};
GetOptions($opt, &quot;uid=i&quot;, &quot;number=i&quot;, &quot;help&quot;, &quot;man&quot;);

if($opt-&gt;{help} || $opt-&gt;{man} || !defined($opt-&gt;{uid})) {
    print &quot;Usage: auto_stracer --number &lt;processes to capture&gt; --uid &lt;numeric uid&gt;\n&quot;;
    exit; 
}
$opt-&gt;{number} ||= 5;

my $traced = 0;
my %seen = ();
my @pids = ();
my %ourpid = ();
$ourpid{$$}++;

$SIG{INT} = sub { kill 9, @pids; print &quot;Interrupt: Killed all running strace processes and quitting.\n&quot;; exit;};

while ($traced &lt; $opt-&gt;{number}) {
    for my $line (split(/\n/, `ps -Ao 'uid,pid'`)) {
        $line =~ s/^\s+//g; # eat leading spaces
        my($uid, $pid) = split(/\s+/, $line);
        next if($ourpid{$pid}); # don't run on something we are tracking
        if($pid &amp;&amp; $uid eq $opt-&gt;{uid} &amp;&amp; !$seen{$pid}) {
            $seen{$pid}++;
            my $st_pid = do_strace($pid);
            $ourpid{$st_pid}++;
            push(@pids, $st_pid);
            $traced++;
            last if ($traced &gt;= $opt-&gt;{number});
        }
    
    }
}

print &quot;Traced $opt-&gt;{number}, waiting for all to finish\n&quot;;
my $kid = 0;
do {
      $kid = waitpid(-1, 0);
} while($kid&gt;0);
print &quot;All processes completed, exiting.\n&quot;;
exit;

sub do_strace {
    my($pid) = @_;
    my $ourpid = fork();
    return $ourpid if($ourpid != 0);
    my $cmd = &quot;strace -p $pid -f -s 65535 -o trace.$pid.$$ -v&quot;;
    print &quot;\tcmd: $cmd\n&quot;;
    system($cmd);
    exit;
}
[/perl]

So, very brute force compared to the elegance of systemtap or DTrace, but when you need it, it's still handy.
