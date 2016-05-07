---
layout: post
title: The Atom Editor - part II. - The Atom Packages
category: Programming
tags: [ Atom, IDE ]
---

# A Collection of Must Have Atom Packages

The Atom editor calls the extension plugins *Packages*. The packages can add a
new functionality, extend the existing functions or completely replace them.
Even the core functions can replaced by an external package.

## How to Install Packages

### Installing directly from the Atom Editor

It is really easy, in the Setting panel (`Ctrl + ,`) go to the Install section,
search for a package and press the Install button.

### Installing from Command Line

Alternatively you can install packages from command line using the `apm`
command. Run `apm install <package>` to install the package. You can also pass
multiple packages for mass installation.

### Installing Starred Packages

You can mark a package with star - unfortunately only at the Atom web
pages, not from the editor itself. Then you can install the starred packages
with command `apm stars --install` command.

You can also install the starred packages from a different user. So if you want
to install [all my starred](https://atom.io/users/lslezak/stars) packages simply
run `apm stars --user lslezak`.

Note: It will install _all_ starred packages, you cannot select individual
packages or change the list before installation. So really check what the bundle
contains!

### Possible Problems

There are thousands of Atom packages available so it means there is a wide
variety of packages and you almost sure find a package for a feature you miss.

#### Too Many Packages?

But that also means there might be several packages providing the same
functionality, there might be forks of the same packages, there might be low
quality packages... This is a similar situation like with Rubygems, you have to
spend some time searching for the best option.

You can use the download number as a very roughly indicator, a high download
number usually means the package is good. (Obviously this metrics does not work
for the newly published packages or for very specific packages...)

#### Conflicts

Another drawback of too many packages is that there might be conflicts between
packages by chance. The keyboard shortcuts can easily get in conflict because
there are not many possibilities for unique and simple shortcuts.

If a shortcut does not work for you then look whether it is actually defined
properly in *Settings* -> *Keybindings section*. Or you can press `Ctrl+ .`
(dot) to display the keybinding resolver which displays all actions for the
pressed keys.

There are two possibilities how to resolve a conflict:

* Simply ignore the conflict and use the command panel (`Ctrl+Shift+p`) for
  selecting and running the action.
* If you need to run the action often and you really want to have a shortcut
  then you can disable the keyboard shortcuts for the conflicting package. If
  you need the shortcuts for the conflicting package you can remap them in your
  config file (there is a link at the top of the Keybinding settings page).

If the packages conflict at the UI level (using the same UI element, that can
happen if you install another fork of a package) or at the program level then
you should probably report an issue at GitHub and ask the maintainers to solve
the conflict.

## Generic Packages

_[Note: Some screenshots are linked from the project pages.]_

* [open-recent](https://atom.io/packages/open-recent) - Adds a new submenu with
  the recent files and projects, helps when you often switch between multiple projects.

![](https://i.github-camo.com/9d1f9ea5daf862fd0797b23d9b2df4bc1116f03f/687474703a2f2f692e696d6775722e636f6d2f643979346941692e706e67)

* [local-history](https://atom.io/packages/local-history) - Keeps a copy of each
  file when saved, can help you if you are experimenting and you want to get
  some change from the history. When you are testing something you usually do
  not commit every change to Git (you would need to clean them up later) so if
  you need to have a fine grained history without Git commits this tool can help
  you. The changes are displayed in a diff tool ([meld](http://meldmerge.org/)
  by default) so you can easily choose which specific parts should be reverted
  back.

![](https://i.github-camo.com/cbb781955878f40a90adfd6baf8791a0ac48efea/687474703a2f2f692e696d6775722e636f6d2f783134716d356e2e706e67)

* [sync-settings](https://atom.io/packages/sync-settings) - Backs up the Atom
  settings to cloud (GitHub's Gist) which can be later restored. You can easily
  share the configuration, packages, snippets, etc across several machines (e.g.
  a desktop and a laptop).

* [term3](https://atom.io/packages/term3) - Run terminal directly in an Atom
  tab, run commands without switching to a different window.

## Editor Packages

* [revert-buffer](https://atom.io/packages/revert-buffer) - Drops all changes and reloads the current file from disk. You can close the file without saving and then reopen it but you would need to file the file in the tree and it would loose the current state (tab position, cursor position).
* [change-case](https://atom.io/packages/change-case) - Easily change the case (camel, snake, upper, lower...)
* [toggle-quotes](https://atom.io/packages/toggle-quotes) - Easily switch between single quote (') and double quotes (")
* [highlight-selected](https://atom.io/packages/highlight-selected) - Highlights the selected word in the file, helps with locating functions and variables in the file.
* [minimap](https://atom.io/packages/minimap) - Displays a miniature of the file for easy navigation.

### Code Completion

* [atom-ctags](https://atom.io/packages/atom-ctags) - Improved code completion
* [autocomplete-emojis](https://atom.io/packages/autocomplete-emojis) - Auto completes [emoji](http://www.emoji-cheat-sheet.com/) tags
* [autocomplete-paths](https://atom.io/packages/autocomplete-paths) - Auto completes file names

### Git Packages

* [atomatigit](https://atom.io/packages/atomatigit) - Adds an optional side bar with Git status and provides some shortcuts for Git commands (stage, commit, push...):

![](http://2.bp.blogspot.com/-Dg5NrI0gawQ/Vm8q38WzHPI/AAAAAAAAPoc/3Hvn_c9KLzk/s320/atom-atomatigit.png)

* [git-plus](https://atom.io/packages/git-plus) - Access to the most used Git commands

![](https://i.github-camo.com/78e2bafa5f9b3afdf47d7e02e3f949fea4801fc0/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f616b6f6e77692f6769742d706c75732f6d61737465722f636f6d6d69742e676966)

I currently combine both, git-plus has direct shortcuts while atomatigit is more interactive (e.g. you can select which changed files to stage and commit).

* [git-blame](https://atom.io/packages/git-blame) - Displays the "git blame" data next to the text:

![](https://i.github-camo.com/48e49f0c648a225aa35bebaaff21d437b5b0461b/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f616c6578636f7272652f6769742d626c616d652f6d61737465722f696d616765732f73637265656e2d73686f742e706e67)

* [git-history](https://atom.io/packages/git-history) - Displays a commit (diff) from the history of the current file (or a full version)

![](https://i.github-camo.com/8ec7f1a9bf4668eccf5ad7834318876e209145cf/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f6a616b6573616e6b65792f6769742d686973746f72792f6d61737465722f7265736f75726365732f73637265656e73686f742e676966)

* [git-log](https://atom.io/packages/git-log) - Displays a graph with the Git commits

![](https://i.github-camo.com/fe46952e7b204bcc30575e592b23bd791e40b25d/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f4e696b68696c4b616c6967652f6769742d6c6f672f6d61737465722f7265736f75726365732f6769742d6c6f672e676966)

* [line-diff-details](https://atom.io/packages/line-diff-details) - Show and easily revert the changes.

![](https://i.github-camo.com/7d4d524f318eb902e51a8b9c9418545a641973e4/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f6a616b6573616e6b65792f6c696e652d646966662d64657461696c732f6d61737465722f7265736f75726365732f64656d6f2e676966)

* [merge-conflicts](https://atom.io/packages/merge-conflicts) - Helps with merging the conflicts.

![](https://i.github-camo.com/44ff44f156f274b8799022e44bcacb804fadc08a/68747470733a2f2f7261772e6769746875622e636f6d2f736d61736877696c736f6e2f6d657267652d636f6e666c696374732f6d61737465722f646f63732f636f6e666c6963742d7265736f6c7574696f6e2e676966)

### Additional Language Packages

These packages provide syntax highlighting and support for other file formats
and programming languages which are not included in the default set:

* [cmake](https://atom.io/packages/language-cmake)
* [cpp14](https://atom.io/packages/language-cpp14) (extends the standard C++ package)
* [diff](https://atom.io/packages/language-diff)
* [gettext](https://atom.io/packages/language-gettext)
* [haml](https://atom.io/packages/language-haml)
* [rnc](https://atom.io/packages/language-rnc)
* [rpm-spec](https://atom.io/packages/language-rpm-spec)

Of course, you can find support for many more languages, just check the
[available packages](https://atom.io/packages).

### Ruby Packages

These packages can help you with Ruby development.

* [ruby-block](https://atom.io/packages/ruby-block) - Highlights the matching block at the cursor. When you place the cursor at `end` keyword it highlights the matching `do` or `if` line.

![](https://i.github-camo.com/689e832c5a8907d5dfb54ee210a1c13df1483c73/687474703a2f2f636c2e6c792f696d6167652f3138316e306631523155304f2f727562792d626c6f636b2e676966)

* [ruby-block-converter](https://atom.io/packages/ruby-block-converter) - Easily convert between single line `{}` and multine `do...end` Ruby blocks.
  ![](https://i.github-camo.com/a2074b2732f9bf9988a8a415901e1dfd83b756fb/68747470733a2f2f6769746875622e636f6d2f6473616e647374726f6d2f61746f6d2d727562792d626c6f636b2d636f6e7665727465722f7261772f6d61737465722f73637265656e2d312e676966)

* [ruby-quick-test](https://atom.io/packages/ruby-quick-test) - Runs the test at the cursor, avoid running whole possibly long test suite when fixing just a single test.

![](https://i.github-camo.com/64fc02739a6ecf558baac8716f76e8dc6d89006b/68747470733a2f2f7261772e6769746875622e636f6d2f7068696c6e6173682f727562792d717569636b2d746573742f6d61737465722f73637265656e73686f74732f727562792d717569636b2d746573742d64656d6f2e706e67)

### Linters

Linters provide live code style checks and/or syntax checks.

* [linter](https://atom.io/packages/linter) - is the base package providing the
  framework for the specific linters.
* [linter-coffeelint](https://atom.io/packages/linter-coffeelint) - a linter for
  coffeescript
* [linter-gcc](https://atom.io/packages/linter-gcc) - C and C++ linter using gcc
* [linter-ruby](https://atom.io/packages/linter-ruby) - Ruby

![](http://3.bp.blogspot.com/-ZleMpkALyZw/Vm8q4kvyxCI/AAAAAAAAPok/2jo0uY0tAAI/s320/atom-ruby-lint.png)

* [linter-rubocop](https://atom.io/packages/linter-rubocop) - There is also
  [Rubocop](https://github.com/bbatsov/rubocop) linter, but unfortunately it
  requires a recent version of Rubocop (the version used by YaST is too old).

## Building tools

For building the sources I basically found two systems:

* [build-tools](https://atom.io/packages/build-tools) (with
  [build-tools-make](https://atom.io/packages/build-tools-make) extension)
* [build](https://atom.io/packages/build) (with
  [build-make](https://atom.io/packages/build-make) and
  [build-rake](https://atom.io/packages/build-rake) extensions, see the
  [complete list of extensions](https://atombuild.github.io/) extensions)

![](https://i.github-camo.com/d30e92a5f43c7a85ba4f9726acd15ba1ea6e7a6c/68747470733a2f2f636c6f75642e67697468756275736572636f6e74656e742e636f6d2f6173736574732f3930373939382f31313734373131352f62376361313336322d613032312d313165352d393865642d3631313164396661313431302e676966)

I tried both and it seems that the `build` package is a bit better from the
usability point of view. `build-tools` use more complicated keyboard shortcuts
and require manual configuration while `build` tries to autodetect the tools
used (scans for `"Makefile`, `Rakefile`, etc...).

### Web Development

* [color-picker](https://atom.io/packages/color-picker) - Change CSS colors via
  visual color picker

![](https://i.github-camo.com/467c72e686f00893c3d36bf46499e76c10f31787/68747470733a2f2f6769746875622e636f6d2f74686f6d61736c696e647374726f6d2f636f6c6f722d7069636b65722f7261772f6d61737465722f707265766965772e676966)

* [pigments](https://atom.io/packages/pigments) - Another color selection and
  visualization tool. (Note: I suggest to disable it and enable it only when
  editing a style sheet. The package treats numbers in a text like `bnc#123456`
  as a color specification and sets it as the background which looks weird...)

![](https://i.github-camo.com/8451130b08ce539d2f19ff985b9ac65d5354708f/68747470733a2f2f6769746875622e636f6d2f61626533332f61746f6d2d7069676d656e74732f626c6f622f6d61737465722f7265736f75726365732f7365617263682e6769663f7261773d74727565)

* [emmet](https://atom.io/packages/emmet) - expands text like
  `ul.list>li.item*3` into the appropriate HTML structure
* [rest-client](https://atom.io/packages/rest-client) - A simple REST API client
  for testing server responses, can be useful for testing the server response.

![](https://i.github-camo.com/7d1497b2c8c30f4c062bc88f3e774483a95cd634/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f6464617669736f6e2f726573742d636c69656e742f6d61737465722f696d616765732f6578616d706c652e676966)

### TODO and FIXME Helpers

* [imdone-atom](https://atom.io/packages/imdone-atom) - Scans the project for
  `FIXME`, `TODO` and similar comments. Displays a Trello-like dashboard where
  you can track the status. The drawback is that it adds some additional tags to
  each comment (to track the state) so it is not good for project where you do
  not want to change the code unnecessarily.

![](http://4.bp.blogspot.com/-xUjntav7E7E/Vm8q3vSHuvI/AAAAAAAAPoU/DPcQJ21q8hU/s320/atom-imdone.png)

* [todo-show](https://atom.io/packages/todo-show) - Similar to the previous
  package, but only scans the project. On the other hand it does not touch the code.

![](https://i.github-camo.com/f15ae70a5e4f1078889fba95bcfdfdec75306a28/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f6d726f64616c67616172642f61746f6d2d746f646f2d73686f772f6d61737465722f73637265656e73686f74732f707265766965772e706e67)

## Miscellaneous

I found some quite interesting plugins which nicely show the flexibility of Atom.

* [lcov-info](https://atom.io/packages/lcov-info) - Displays code coverage
  overview and highlights covered and not covered lines. For Ruby you need LCOV
  coverage generator like
  [simplecov-lcov](https://github.com/fortissimo1997/simplecov-lcov) Ruby gem.
  You need to enable single file output via
  `"SimpleCov::Formatter::LcovFormatter.report_with_single_file = true` option
  and add the formatter. Unfortunately the generated coverage is saved to
  `./coverage/lcov/_directoryname_<dirname>.lcov` while the lcov-info expects
  `./coverage/lcov.info` file. You have to move the file manually or enhance the
  task which runs the test.

![](http://4.bp.blogspot.com/-fRKmDSFZSq0/Vm8q3-mcUGI/AAAAAAAAPoY/LeOoUJitbc8/s320/atom-lcov-info-coverage1.png)

* [markdown-scroll-sync](https://atom.io/packages/markdown-scroll-sync) - A nice
  enhancement for the builtin Markdown preview package. It keeps the editor
  position in sync with the preview when scrolling and moving the text.

![](https://i.github-camo.com/a0f171c5c0f3ca4403ae288ca45ee2035906c184/68747470733a2f2f636c6f75642e67697468756275736572636f6e74656e742e636f6d2f6173736574732f3831313435352f31313331373235392f63356239623063322d386664632d313165352d386638352d6237646565666235323563352e676966)

* [regex-railroad-diagram](https://atom.io/packages/regex-railroad-diagram) -
  visualizes regular expressions.

![](https://i.github-camo.com/8466434b7e61870e51dbee19b0bc1173846e7a89/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f6b6c6f72656e7a2f61746f6d2d72656765782d7261696c726f61642d6469616772616d732f333535323636373232386331393265383161306432653538343365383234633036346238653462392f72656765782d7261696c726f61642d6469616772616d732e706e67)

* [docblockr](https://atom.io/packages/docblockr) - help with writing the
  documentation comments, scans the function methods and generates a template.
  Unfortunately the support for Ruby is missing... :worried:

![](https://i.github-camo.com/39f257a03de803c96a04d2dbf9d694da1aa9ed35/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f4e696b68696c4b616c6967652f646f63626c6f636b722f6d61737465722f7265736f75726365732f747970652d68696e74696e672e676966)

* [travis-ci-status](https://atom.io/packages/travis-ci-status) - Displays a
  Travis build status icon in the panel. On click it displays details with a
  direct link to the Travis build.

![](http://1.bp.blogspot.com/-54m_vWm3UBA/Vm8q4qv8ucI/AAAAAAAAPos/kHfGPKs3Ykk/s320/atom-travis-status.png)

## To be Continued...

The next time we will look at the "hackability" of the Atom editor...
