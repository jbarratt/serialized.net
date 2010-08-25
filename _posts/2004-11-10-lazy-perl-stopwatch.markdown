--- 
wordpress_id: 27
layout: post
title: Lazy Perl stopwatch
wordpress_url: http://wp.serialized.net/?p=27
---
I needed a quick stopwatch to give me elapsed time on an event. (Making sure a transit took the right amount of time.) Perl to the rescue...

{% highlight perl %}
    #!/usr/bin/perl -w
    # lazy stopwatch. Prints elapsed time each time you hit enter.

    use Time::HiRes qw(gettimeofday tv_interval); #get better than 1 second resolution

    $t0 = [gettimeofday]; # starting time
    while(<STDIN>)
    {
    $t1 = [gettimeofday];
    print tv_interval($t0,$t1); # print elapsed time between enter key hits.
    $t0 = $t1;
    }
{% endhighlight %}


So to use it, run it in a shell, hit `[Enter]` to start timing, `[Enter]` again to stop, and you'll see the elapsed time. Ends up looking something like...

{% highlight text %}
    0.450393
    0.514762
    1.311815
    1.488909
{% endhighlight %}


Wheee!
