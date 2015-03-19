---
layout: article
title: "Switch-to-application Shortcut on Linux"
modified:
tags: [scripting]
image:
  feature:
  teaser:
  thumb:
---

There is always one software that we use a lot at work. As a programmer, most of my daily work is spent on a Linux terminal or on the IDE.

Now, when an application is so frequently used it wouldn't be great if we could run it with a shortcut or to give it the focus if it is already running? Thanks to this [article](http://vickychijwani.github.io/2012/04/15/blazing-fast-application-switching-in-linux/), the answer is **[wmctrl](http://linux.die.net/man/1/wmctrl)**:

> $apt-cache show wmctrl

> Wmctrl is a command line tool to interact with an EWMH/NetWM compatible X Window Manager (examples include Enlightenment, icewm, kwin, metacity, and sawfish).

> Wmctrl provides command line access to almost all the features defined in the EWMH specification. For example it can maximize windows, make them sticky, set them to be always on top. It can  switch and resize desktops and perform many other useful operations.

Basically, what we need is the following script that checks for the application already running and *activate it* or run a new instance of it

{% highlight bash %}
cat /usr/local/bin/run-or-raise.sh
#!/bin/sh
wmctrl -x -a "$1" || "$1"
{% endhighlight %}

For example, I really like **Konsole**, the KDE terminal emulator, so that I installed it also in my new OS of choice [ElementaryOS](http://elementaryos.org/), and I binded a keyword shortcut (**F12** as in [Yakuake](http://yakuake.kde.org/)) to the command `run-or-raise.sh konsole`

On ElementaryOS, you need to install [Elementary Tweaks](http://www.elementaryupdate.com/2013/06/finally-elementary-tweaks.html) to add new keybord shortcuts, but there are lot of other way to do it: [google-search](https://www.google.com/search?client=ubuntu&channel=fs&q=add+custom+keyword+shortcuts+linux&ie=utf-8&oe=utf-8&gfe_rd=ctrl&ei=tTzqUpSMLqqO8QeRroCAAg&gws_rd=cr).

Since I often change notebook or just install a new OS, I put the generation of most of this stuff in a script like the following

    sudo apt-get install -y konsole
    echo "#!/bin/bash" > ~/run-or-raise.sh
    echo "wmctrl -x -a \"\$1\" || \"\$1\"" >> ~/run-or-raise.sh
    sudo mv ~/run-or-raise.sh /usr/local/bin
    sudo chmod a+x /usr/local/bin/run-or-raise.sh

Of course the `raise-or-run` script can be used on whatever other application, I use it also with Firefox and VIM.
