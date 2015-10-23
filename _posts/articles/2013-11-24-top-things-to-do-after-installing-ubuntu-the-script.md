---
layout: post
title: "Top Things to Do After Installing Ubuntu. the Script"
modified:
excerpt: "One script to rule them all"
categories: articles
tags: [scripting, ubuntu]
published: true
---

At each Ubuntu release lot of articles appear on the Web about **what to do after installing Ubuntu**. This actually happens for almost all the major Linux distributions, since some tools, libraries or codecs have legal constraints in some countries so their are not shipped with the default GNU Linux distribution.

Said that most of the **best things to do after installing name-of-your-distribution** are usually the same all the time, here the engineer's mind comes to make a **script that sets everything up everytime** for Ubuntu and derivates<a rel="nofollow" href="#footnote1" id="ref_footnote1"><sup>1</sup></a>.

Here is the link to the [GitHub Gist](https://gist.github.com/clobrano/7437551) of the complete script, feel free to leave a comment there.

The easiest way to make this script was the following

    sudo apt-get update && apt-get upgrade
    sudo apt-get install <whatever you want>
    ...

but:

1. It is not even close to a bash scripting exercize (see previous note 1)
2. What if I don't want to install a tool, or what if I change from Ubuntu to Kubuntu, from a i386 system to an X86_64...?

What I want is **configurability** and **control**.

## Configurability
I used a simply *select-case* structure to let the user choose whether execute an `input function` or not

{% highlight bash %}
function prompt
{
    FUNC=$1
    select choice in "yes" "no" "exit"; do
        case $choice in
            "yes")
                $FUNC $choice
                break
                ;;
            "no")
                $FUNC $choice
                break
                ;;
            "exit")
                exit
                ;;
            *)
                log "Bad reply. Please answer yes/no"
                ;;
        esac
    done
}
{% endhighlight %}

Ok, the action for the choice "yes" and "no" is the same (just passing the choice to the input function), but that was necessary in order to *validate* the user reply: if the user replies with "abracadabra" an error message will appear.


## Control

The function passed to the previous function stores the user choice and executes the command to install the tool/program/library.

For example:

{% highlight bash %}
function systemUpdate
{
    log "Update system"
    SYS_UPDATE=${SYS_UPDATE:=$1}

    case $SYS_UPDATE in
        "to-do")
            log "Updating System"
            execute apt-get update && apt-get upgrade
            SYS_UPDATE="done"
            ;;
        "yes")
            SYS_UPDATE="to-do"
            ;;
        "no")
            log "System won't be updated"
            SYS_UPDATE="not-to-do"
            ;;
        "not-to-do")
            log Nothing to do
    esac
}
{% endhighlight %}

the `systemUpdate` function stores the user choice in `SYS_UPDATE` variable. At the first call `SYS_UPDATE` is set with the input argument

{% highlight bash %}
SYS_UPDATE=${SYS_UPDATE:=$1}
{% endhighlight %}

while at all the others calls, the input argument won't change the `SYS_UPDATE` value, that is the function executes what has been chosen the first time.

In this way the flow of execution is:

1. Choose what to do = set function's variable (**configurability**)
2. Recap what you choose = print out the function's variable (**control**)
3. Ask whether continue with this choises or not
4. Execute or exiting according to point 3

For the example above, the `Main` program will be

{% highlight bash %}
echo "Update the system?"
prompt systemUpdate                # this will prompt the list "yes, no, exit"
echo "System update: $SYS_UPDATE"  # This will prompt something like "System Update: to-do". Here you can interrupt the script
systemUpdate                       # This will actually execute the update
{% endhighlight %}

In the bash script there are some other auto-explicative utilities for logging and to determine which is the running system that can be useful to install specific tools (like gnome-tweak-tools or something KDE related)

{% highlight bash %}
function isSystem64
{
    case `uname -m` in
        "x86_64")
            SYSTEM64=1
            ;;
        *)
            SYSTEM64=0
            ;;
    esac
}
{% endhighlight %}


{% highlight bash %}
function detectDE
{
    if [ x"$KDE_FULL_SESSION" = x"true" ]; then DE=kde;
    elif [ x"$GNOME_DESKTOP_SESSION_ID" != x"" ]; then DE=gnome;
    else DE=unknown
    fi
}
{% endhighlight %}


And that's it, so far.



#### Footnotes
1. by the way this was also an exercise of *Bash scripting* and the best way for me to take a note of what I need to install and how<a rel="nofollow" href="#ref_footnote1" id="footnote1">[↩]</a>.

