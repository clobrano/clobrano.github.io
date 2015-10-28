---
layout: post
title: "Free this memory!"
excerpt: Remove old and unused stuff from your hardisk
tags: [Linux,  ]
category: blog
date: 2015-10-27 18:00
image:
  teaser:
  feature: free-memory.png
  credit:
published: true
---

One of the things I like the most in Linux is the large amount of programs that anybody can try. So I like spending some time installing and trying new applications (most of them with command line interface only), utilities, also themes and the result is that my disk space tends worryingly to be full, therefore I am always looking for other tools to free some memory.

Usually I just use the common tools to check/free disk memory:

{% highlight bash %}
df -h               # check memory occupation for each partition
du -d1 -t50M -h     # list directories with more than 50M under the current working directory
comm -23 <(apt-mark showmanual | sort -u) <(gzip -dc /var/log/installer/initial-status.gz | sed -n '\''s/^Package: //p'\'' | sort -u)
{% endhighlight %}

The last one one is a bit more complicated. It reports only the deb packages that have been installed by me, so that I can easily remove it without breaking the system.

And then:

{% highlight bash %}
apt-get autoremove
apt-get autoclean
apt-get purge <package>
{% endhighlight %}

Some days ago, worried more than usual about my **70% filled HD**, I looked deeply at the problem (namely, I googled deeply) and found out other interesting commands:

{% highlight bash %}
dpkg-query -W --showformat='${Installed-Size} ${Package}' | sort -nr | more # this list installed packages by memory footprint, nice!
apt-get clean   # how did I miss that so far?
dpkg --get-selections | grep linux-images
{% endhighlight %}

I already knew the last one, but also I keep forget it and Linux kernels occupy about 250MB so, better remove the oldest one (I just keep the last 2 ones).

I am also trying [localepurge](http://manpages.ubuntu.com/manpages/precise/man8/localepurge.8.html) which does the following:

> localepurge - reclaim disk space removing unneeded localizations


## Conclusions

I took a deep breath looking at my now only 55% filled HD.

