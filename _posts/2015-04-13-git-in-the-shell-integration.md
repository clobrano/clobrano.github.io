---
layout: article
title: Git in the Shell. Integration
tags: [Git, Shell]
published: false
---
Initially, I wanted to write a long article about [**GIT**](http://git-scm.com/), how useful it is - [at 10 years since its creation](https://www.atlassian.com/git/articles/10-years-of-git/) - and *blah blah blah*, but then I thought that there are alrady [lots of articles out there saying pretty much the same thing](https://www.google.it/search?client=ubuntu&hs=6Sm&channel=fs&q=git+how+to&oq=git+how+to&gs_l=serp.3...9121.9812.0.10076.7.6.0.0.0.0.0.0..0.0.msedr...0...1c.1.64.serp..7.0.0.vaBWCreLahE) and that I was not adding that much. My contribute, instead, will be about how I integrate it in my workflow, hoping that it could help someone.

I work mainly under Linux and so the Shell is my helm. Working in a Shell, I think there are **3 important things** you need to have **clear under your sight**:

1. **Who you are**: that is, whether are you a normal user, or **root**.
2. **Where you are**: the working directory.
3. **What you are working on**.

For the latter, the second point is normally sufficient, but if I am working on a project with revision control, let's say GIT, I need other information:

1. Current branch
2. Most recent Tag
3. Whether there are uncommitted modifications or not
4. Stashed changes (Git only, I guess).

I used to work with [Bash](https://www.gnu.org/software/bash/), as it is the default Shell in all the GNU/Linux distributions I worked with, and the only trick I used at the beginning was a list of **aliases** (in `.basrh` configuration file) to save some time typing the most common GIT commands:

{% highlight bash %}
alias ga='git add -u'       # use it with caution
alias gb='git branch'
alias gd='git diff -w'      # -w: ignore whitespace
alias ghistory='git log --decorate --oneline --graph --all --date=short --pretty=format:"%C(auto)%d%Creset %C(auto)%h%Creset - %C(cyan)%an%Creset %Cgreen(%ad)%Creset : %s" '
alias gs='git stash list'
alias gs='git status'
{% endhighlight %}

**ghistory** (which is actually `gh`, I wrote here `ghistory` to make it clear what it means) is my preferred and looks like this (local branches are in green, remotes one in red):

![ghistory](/images/bash-ghistory.png)


So far nothing special actually, then I heard of [**Zsh**](http://zsh.sourceforge.net/).

As everyone can read in every website that talks about it, Zsh is a Shell as well as Bash, but with an important feature for me: the **double prompt**, one on the left side of the shell -like in Bash- and **one on the right side**. So, what changed with Zsh?

### Who I am

Well, not what "I am" personally of course, but the way the user name is displayed, with a particular regards to **root** user<a rel="nofollow" href="#footnote1" id="ref_footnote1"><sup>1</sup></a>.

![iamroot](/images/iamroot.png)

here is the **definition of the left prompt** (this one goes on `.zshrc` configuration file):

{% highlight bash %}
if [ ${USER} = root ]; then
    PROMPT='%F{red}$(_prompt_line)%f# '
else
    PROMPT='%F{yellow}$(_prompt_line)%f> '
fi
{% endhighlight %}

where `__prompt_line` is the following function

{% highlight bash %}
_prompt_line()
{
    echo -n "Iam%n"
}
{% endhighlight %}

`%n` is the *user name*.

### The working directory path

The only thing I always found annoying **in Bash** was the fact that **the prompt can fill the main part of the command line** when you are working in a folder with a deep path respect home:

![bash-long-path](/images/bash-long-path.png)

This way I do not have a straight line of comands each one above the previous also, which could be easier to read.

As I said, Zsh has got a right prompt (`RPROMPT` variable) that can be used to display all sort of information (e.g. the location path and the current time, as you can see in the next picture) having the beginning of the command line always at the same level, but the real good thing is that the **RPROMPT disappears when you need more space**

![zsh-long-path](/images/zsh-long-path.png)

I still prefer to have even more space, so I changed the right prompt in order to show the current working directory and its parent only(the `%2c` variable, where 2 is the number of folders to show)

{% highlight bash %}
RPROMPT='%2c %F{yellow} %T%f'
{% endhighlight %}

### The Integration with GIT

With Zsh functions I could **integrate some GIT information** from the list above directly into the command line: the **current branch**, the most recent **tag**, uncommitted **changes** and **stashed content**:


![zsh-with-git-info](/images/zsh-with-git.png)

Current branch is shown in cyan, followed by the most recent tag (if any) in green. Changes to be committed are suggested by the symbol *↪* and the number of stashed changes by the pattern *SN* in red (N is the number of stashed changes). And this is the code

{% highlight bash %}
_prompt_git_status()
{
    local __git_branch=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)

    if [ -n "${__git_branch}" ]; then
        echo -n '[%F{cyan}'$__git_branch'%f'


        local __git_tag=$(git describe --tag --abbrev=0 2>/dev/null)
        if [ ! -z ${__git_tag} ]; then
            echo -n "-%F{green}${__git_tag}%f"
        fi
        echo -n "]"


        local __git_modified=$(git status --porcelain --untracked-files=no 2>/dev/null | wc -l )
        if [ -n "${__git_modified}" ]; then
            if [ "0" = "${__git_modified}" ]; then
                echo -n "-"
            else
                echo -n '↪'
            fi
        fi

        local __git_stash=$(git stash list 2>/dev/null | wc -l)
        if [ -n "${__git_stash}" ]; then
            if [ "0" = "${__git_stash}" ]; then
                echo -n "-"
            else
                echo -n " %F{red}S${__git_stash}%f"
            fi
        fi
    fi
}


RPROMPT='$(_prompt_git_status) %2c %F{yellow} %T%f'
{% endhighlight %}



#### Footnotes
1. Yes, I know, I could do that on Bash, too, I guess. <a rel="nofollow" href="#ref_footnote1" id="footnote1">[↩]</a>.




[Ten years ago Git was created](https://www.atlassian.com/git/articles/10-years-of-git/). Personally I only started using Git a couple of years ago, after one more year of experience with SVN, but I was a big change.

* how I worked with GIT (cool, various, low responsability)
    - Git is confortable. There is so much space for try and error without risk of compromising the entire project that the pressure is quite low and I liked that very much, above all at the beginning. The other aspect I appreciate is that the command-line tool is effective enough to avoid using GUI tools (see **aliases** section).
* how much time I spent in Git and shell
    - My Shell is my helm and I usually spent a lot of time both in Shell and GIT, so I need a good way to import information like **branch** and **tag** in my command shell.
* what I don't like in bash, the too long prompt when going down the path (and the fact that I prefer deep paths respect wide paths)
    - Default Shell in Linux is usually bash.
* Solution: Zsh and double prompt.
* Minimal prompt and...
* Fully informative prompt (that can disappear) for all the rest
* Git integration
    - informative prompt and branch
    - aliases
    - ghistory


