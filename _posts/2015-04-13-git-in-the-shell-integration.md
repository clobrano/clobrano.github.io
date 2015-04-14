---
layout: article
title: Git in the Shell. Integration
tags: [Git, Shell]
published: false
---

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

Initially I wanted to write a long article about Git, how useful it is, at 10 years since its creation, but then I thought that in the end there are lots of articles out there that say the same thing and that I was not adding that much. My contribute, instead, will be about how I integrate it in my workflow, hoping that it could help someone.

I work mainly under Linux and so the Shell is my helm. I was used to work with [Bash]() as it is the default Shell in all the GNU/Linux distributions I worked with, and the only modding I used was a list of **aliases** like the following:

    alias gs='git status'
    alias gdif='git diff -w'

and above all my customized **git history**

    alias gh='git log --decorate --oneline --graph --all --date=short --pretty=format:"%C(auto)%d%Creset %C(auto)%h%Creset - %C(cyan)%an%Creset %Cgreen(%ad)%Creset : %s" '

which I prefere respect a `git log` and looks like this

    [insert image here]
