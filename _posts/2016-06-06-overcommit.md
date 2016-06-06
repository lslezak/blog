---
layout: post
title:  "Overcommit? Overcommit!"
date:   2016-06-06
category: Programming
tags: [ git ]
description: How to easily run checks from git commit hooks?
---

Git itself provides support for running [hook scripts](
https://git-scm.com/docs/githooks) at many events. It would be nice to run
some checks automatically, for example when creating a commit.

The problem is not how to run the checks but how to integrate them,
make them fast and safe, provide a nice output, etc...

# Overcommit

By chance I came across [Overcommit](https://github.com/brigade/overcommit),
a nice framework for running Git hooks.

It provides a wide variety of predefined checks which are easy to use. I found
some of them really useful:

- Forbid commits to blacklisted branches - e.g. you can forbid direct commits to
`master` because you want to use Pull Requests. This can also avoid mistakes,
e.g. once I did a typo in `git checkout -b` and overlooked that.
The following `commit` and `push` went by mistake directly to `master`...

- Run [Rubocop](https://github.com/bbatsov/rubocop) automatically to avoid
  fix up commits later. I see *"make Rubocop happy"* commits quite often in
  YaST.

- Run the tests locally before pushing the changes, that makes sure the tests
  still pass. Sometimes you forget to run the tests or simply you are too
  lazy to run them.

- And last but not least it can check spelling of the commit messages to avoid
  typos.

For more details see the [Overcommit documentation](
https://github.com/brigade/overcommit).

## Speed?

A very nice Overcommit feature is that it wants to be quick, your usual git
workflow should not be slowed down because of the hooks.

For example it tries running all hooks in parallel and the Rubocop hook checks
only the changed files, which speeds it up for large repositories a lot.
Running Rubocop over all source files in the YaST registration module takes
about 7 seconds on my machine, but when a single file is checked it takes
just a fraction of a second.

## Installation

I have prepared RPM packages with Overcommit, simply add the [YaST:Head
repository](http://download.opensuse.org/repositories/YaST:/Head/) and run

```
zypper in "rubygem(overcommit)"
```

## Initializing the Hooks

Now go to your Git repository and set up the hooks:

- Use `overcommit --install` to install the hooks.
- Add the `.overcommit.yml` configuration file.
- Run `overcommit --sign` to sign the current configuration. You will need to
  run it again whenever the config files is changed.
  See the  [Security section](https://github.com/brigade/overcommit#security)
  in the documentation for more details.

You need to install Overcommit in each Git repository separately, but it is
possible to [automate the setup](
https://github.com/brigade/overcommit#automatically-install-overcommit-hooks).

## Using the Hooks

Then you use the `git` commands as usually, you do not need to change anything
to run the hooks. You will just see the results of the Overcommit hooks
in the output.

If a hook fails the whole `git` command fails. So if for example the Rubocop
hook fails when creating a commit then the commit is not created and you can fix
the problem early.

If for some reason you want to temporarily disable the Overcommit checks run
the `git` command with `OVERCOMMIT_DISABLE=1` environment setting.

## Example Configuration

Here is an example `.overcommit.yml` file proposed for YaST:

```yaml
PreCommit:
  # do not commit directly to these branches, use Pull Requests!
  ForbiddenBranches:
    enabled: true
    branch_patterns:
      - master
      - openSUSE-*
      - SLE-10-*
      - Code-11*
      - SLE-12-*

  RuboCop:
    enabled: true
    # treat all warnings as failures
    on_warn: fail

CommitMsg:
  SpellCheck:
    enabled: true
    # force using the English dictionary
    env:
      LC_ALL: en_US.UTF-8

PrePush:
  RSpec:
    enabled: true
    command: [ "rake", "test:unit" ]
    # do not fail because of translations
    env:
      LC_ALL: en_US.UTF-8

```

## Example Session

I recorded an example session to show the mentioned Overcommit features in
action in the registration repository:

![Overcommit screencast](/images/overcommit_screencast.gif)

It looks nice, isn't it?

## Conclusion

It looks very promising and it could catch some obvious mistakes early, I will
give it a try. I will try using it for some time and then we will see if it
could used in YaST more widely.
