---
layout: post
title: Git status in bash prompt - bash-git-prompt
category: Programming
tags: [ git ]
---

## Do you use Git?

Have you ever committed a change into a wrong branch? Have you ever started
working on a feature/bug fix without pulling from remote and thus need to rebase
and resolve conflicts later? Have you ever forgotten your stashed changes?

With bash-git-prompt tool that's gone!

### The tools

Recently I was looking for some tool which would help me to avoid the problems
mentioned above.

At first I tried
[sexy-bash-prompt](https://github.com/twolfson/sexy-bash-prompt), but I didn't
like the default scheme, it was too different to the usual openSUSE bash command
prompt. I tried to customize it, although the code allows some customization I
could not make it look like the openSUSE default.

Then I tried [bash-git-prompt](https://github.com/magicmonty/bash-git-prompt).
This tool is similar to sexy-bash-prompt, but provides more details about the
git status. What I really like:

* It prints more details about the current Git status (number of commits
  behind/ahead of remoter, count of stashed changes,...)

* It periodically fetches the status from remote, if there are new commits on
  the remote you will be noticed (the check is run every 5 minutes, but it's
  configurable and can be completely disabled)

* Displays the exit status of the last command, if a command fails you'll see
  a red X mard and the exit status number. So a failure is more visible on the
  terminal.

I really recommend to give it a try if you use Git from command line, it can
save you same headache...

### Installation

Installation is really easy, just run

```shell
cd ~
git clone https://github.com/magicmonty/bash-git-prompt.git .bash-git-prompt 
```

If you want to update it later just run `git pull` in that directory.

### openSUSE theme

 Again the default style is too different, but it turned out that the color
and the style can easily changed and bash-git-prompt already provides several
styles out of box.

So I decided to create a new style which would mimic the default openSUSE
bash prompt as close as possible so you would not notice that you are using
bash-git-prompt if you are outside a Git repository.

To use bash-git-prompt with the openSUSE theme simply add this to your
`~/.bashrc` file:

```shell
GIT_PROMPT_THEME=Single_line_openSUSE
source ~/.bash-git-prompt/gitprompt.sh
```

The next time you start a new shell session bash-git-prompt will be
automatically loaded.

![](http://2.bp.blogspot.com/-JJ5QXeM1tLo/VNiEn6GfhvI/AAAAAAAAM5Y/EWtT54I6ob4/s1600/bash-git-prompt1.png)

Except the exit status at the beginning of the line it looks like the usual
openSUSE bash prompt.

Let's see how it can help with Git:

![](http://4.bp.blogspot.com/-w3AezCWkDjg/VNiEn9U_rpI/AAAAAAAAM5c/6g_3G7Wpsps/s1600/bash-git-prompt2.png)

**Note:** If you do not like the exit status indicator at the beginning of
the line then use `Single_line_NoExitState_openSUSE` theme instead.

### Help

 You can run `git_prompt_help` to see what the used symbols mean:

![](http://4.bp.blogspot.com/-glFm5LppDkU/VNiGfwp_RdI/AAAAAAAAM5s/ycHGP0VHylA/s1600/bash-git-prompt3.png)

There are also some examples displayed by `git_prompt_examples` command:

![](http://4.bp.blogspot.com/-BY0UwxypFxo/VNiGf_1bmXI/AAAAAAAAM50/92aht7tO-zc/s1600/bash-git-prompt4.png)

And if you want to change the colors used in the style you can use
`git_prompt_color_samples` to see all colors available (note: this depends on
the terminal configuration, the colors might not be the same in a different
terminal):

![](http://1.bp.blogspot.com/-Uk1BbidtLtI/VNiGf_iyaCI/AAAAAAAAM5w/90H4Jcx_YgA/s1600/bash-git-prompt5.png)

### Note for Midnight Commander Users

I noticed that bash-git-prompt does not work correctly when you use Midnight
Commander (`mc`). The problem is that it overwrites the `PROMPT_COMMAND` shell
variable (see more details at [https://www.midnight-commander.org/ticket/2027](https://www.midnight-commander.org/ticket/2027)).

My workaround it to define an alias in `~/.alias` file:

```shell
alias gp="source ~/.bash-git-prompt/gitprompt.sh" 
```

and run `gp` whenever you want to use the bash-git-prompt functionality in
mc subshell.

(The ticket above contains a patch for MC, but you would need to recompile
it from sources and maintain it yourselves, i.e. keep it up to date if a
security fix is released...)