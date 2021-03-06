--- 
wordpress_id: 121
layout: post
title: Encoding video for the iPhone with ffmpeg
wordpress_url: http://wp.serialized.net/?p=121
---
I searched for this more than I should have, so I'm contributing it back out to the universe.

I wanted to convert pretty much any kind of video to iphone format.  This was for 2 reasons:
* it seems to pretty much only want to play .mp4 videos
* at < 8G available for video, why would you want to have all that extra resolution of video around when you're only ever going to play it on the dinky little 480�272 screen?

So I found a neat little package, [vs4p](http://code.google.com/p/vs4p/). It works only on linux out of the box, and uses a style of packaging I wasn't really interested in, so I tweaked it a bit. The good guts (with some shell expansion variables) are:

Pass 1

{% highlight console %}
$ ffmpeg -threads $THREADS -y -i "$1" -s 480x272 -vcodec libx264 -b $BITRATE -flags +loop -cmp +chroma -me_range 16 -g 300 -keyint_min 25 -sc_threshold 40 -i_qfactor 0.71 -rc_eq "blurCplx^(1-qComp)" -qcomp 0.6 -qmin 10 -qmax 51 -qdiff 4 -coder 0 -refs 1 -bt $BITRATE -maxrate 4M -bufsize 4M -level 21  -r 30000/1001 -partitions +parti4x4+partp8x8+partb8x8 -me hex -subq 5 -f mp4 -aspect 480:272 -title "$TITLE" -acodec libfaac -ac 2 -ar 48000 -ab 128 -pass 1 "$TITLE.mp4"
{% endhighlight %}

Pass 2

{% highlight console %}
$ ffmpeg -threads $THREADS -y -i "$1" -s 480x272 -vcodec libx264 -b $BITRATE -flags +loop -cmp +chroma -me_range 16 -g 300 -keyint_min 25 -sc_threshold 40 -i_qfactor 0.71 -rc_eq "blurCplx^(1-qComp)" -qcomp 0.6 -qmin 10 -qmax 51 -qdiff 4 -coder 0 -refs 1 -bt $BITRATE -maxrate 4M -bufsize 4M -level 21  -r 30000/1001 -partitions +parti4x4+partp8x8+partb8x8 -me hex -subq 5 -f mp4 -aspect 480:272 -title "$TITLE" -pass 2 -acodec libfaac -ac 2 -ar 48000 -ab 128 -vol 320 "$TITLE.mp4"
{% endhighlight %}

Since I wanted to run this on a mac, I also needed ffmpeg, which should be easy, as it's part of [MacPorts](http://www.macports.org/)

{% highlight console %}
$ sudo port install ffmpeg +gpl +lame +x264 +xvid +faac +faad +a52
{% endhighlight %}

I found a problem with this, where it errored out saying something like:

{% highlight console %}
$ make -C libpostproc install-headers
make              -C libswscale  install-headers
Makefile:20: ../subdir.mak: No such file or directory
make<sup class="footnote">[1](#fn1)</sup>: *** No rule to make target `../subdir.mak'.  Stop.
make: *** [install-headers] Error 2
{% endhighlight %}

This is a bug that's being fixed in MacPorts, apparently, but in the meantime you can fix it by editing one of the Makefiles, changing `subdir.mak -> common.mak`, and building again. It's listed as [ticket #14964](http://trac.macosforge.org/projects/macports/ticket/14964) in their Trac.

In any case, hope that helps if you're looking to transcode, or convert, video for the iPhone or iPod Touch.
