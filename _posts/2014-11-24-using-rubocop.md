---
layout: post
title: Using Rubocop
category: Programming
tags: [ Ruby ]
---

## Introduction

[Rubocop](https://github.com/bbatsov/rubocop) is a Ruby static code analyzer
which looks for common code smells and checks the coding style.

## Installation

The installation is quite easy, just run `sudo gem install rubocop` command
(assuming you have Ruby already installed).

## Initial Run and Creating the Config File

If there is no `.rubocop.yml` file in your project root then Rubocop uses [the
default configuration](https://github.com/bbatsov/rubocop/blob/master/config/default.yml).

It is a good idea to let Rubocop generate the project default for you, simply
run `rubocop --auto-gen-config`. This will create `.rubocop_todo.yml` file which
can be used as a template for your initial config file.

The default generated config file disables all checks which fail. That means if
you run Rubocop with this config file (or if you remove the `_todo` suffix) it
will report success.

## Fixing the Issues

Now you can go one by one disabled check in the created template, enable
each check and see where the problem is and whether it's a valid issue according
to your style or preferences.

There are basically these solutions how to fix an error reported by Rubocop:

* Fix the issue according to the suggestions reported by Rubocop

* Let Rubocop to fix it for you (does not work for all issues found, but
  majority of coding style issues, like indentation or white space usage, can
  be fixed automatically), just add `-a` or `--auto-correct` option. You
  should manually check the changes done `git diff`) after auto correction,
  just to be sure the fix was correct and had no side effects.

* Change the expected style (e.g. the default Rubocop style is single quoted
  string literals, if you prefer double quoted strings in your project then
  set the different default in the config), see the possible options in [the
  default
  configuration](https://github.com/bbatsov/rubocop/blob/master/config/default.yml).

* [Disable the check locally](https://github.com/bbatsov/rubocop#disabling-cops-within-source-code)
  in the code (e.g. the rule is valid, but the specific place in the code is an
  exception where breaking the rule is correct, for example you prefer
  `.nil?` over `== nil`, but in a test you want to check your `operator=`
  definition correctly handles `nil` comparison)

*   Disable the check globally

## Using Rubocop in CI

To ensure that the coding style is honored during development it is a good idea
to run Rubocop at CI (Continuous Integration) server like Travis or Jenkins.

## Rubocop in Yast

I tried to start with Rubocop in the Yast [registration
module](https://github.com/yast/yast-registration) which is written from scratch
and should not contain ugly code parts introduced by YCP to Ruby conversion.

Initially it reported almost 3000 (!) offenses, but many of them were false
positives caused by different coding style defaults (e.g. single quote vs.
double quote string literals). After adapting the config style (and relaxing
some metric checks which would require non-trivial refactoring) the number of
issues was decreased to just about 900.

Majority issues were harmless and related to white space, but some of the checks
found really bad code, like this ["private" modifier
issue](https://github.com/yast/yast-registration/commit/4f4819838723e7ddd7598976db29268226838557).

Thanks to the nice auto correction feature the majority of the issues (~830
which is about 92%) could be fixed automatically. So the number of the manual
changes was rather small.

I found only two issues with auto correction - at one place it removed a comment
which was inside a removed block (moved outside the block) and in another case
it added a trailing space at the end of the line (at that time I had the
trailing check disabled so it was not fixed by another check).

You can see all the changes in [this pull
request](https://github.com/yast/yast-registration/pull/177).

## Result

It was a nice experience with Rubocop as it not only complains what is wrong but
also suggests the solution how to fix the found issues. More over it has auto
correct feature which works very well and can fix almost all coding styles
issues automatically.

So let see if we can use it in more Yast modules...
