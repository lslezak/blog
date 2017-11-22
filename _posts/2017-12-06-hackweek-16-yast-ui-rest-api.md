---
layout: post
title: Hackweek 16 - The YaST Integration Tests II.
category: YaST
tags: [ Linux, YaST, Hackweek, testing ]
description: Controlling and inspecting the YaST UI remotely. This time with a
  REST API.

---

# Hackweek 16 (0x10)

I spent the [previous Hackweek with Cucumber and YaST][1]. It worked quite well
but it turned out to be too big step forward and focused on one specific
testing framework.

This time I decided to make a smaller step, but make the solution more generic.
So instead of having direct Cucumber support in the UI I proposed implementing
a generic REST API. The API can be then used by any framework (Cucumber, RSpec...)
or it can be used directly by any script or tool.

## Implementation

The current solution is based on these two key libraries:

- [GNU libmicrohttpd](https://www.gnu.org/software/libmicrohttpd/) - an embedded
  HTTP server
- [jsoncpp](https://github.com/open-source-parsers/jsoncpp) - JSON parser/writer

Both libraries are already included in the openSUSE distributions so it is
easy to use them.

## Installation

After compiling and installing new libyui, libyui-qt and recompiling
yast2-ycp-ui-bindings (because of ABI changes) you can run any YaST module with
`YUI_HTTP_SERVER=14155` environment variable set. Then you can access the REST
API via the port 14115 using `curl` command. (You can use a different port
number.)

## Using the REST API

Run the YaST repository manager (as *root*):

```bash
YUI_HTTP_PORT=14155 yast2 repositories
```
{% include blog_img.md alt="Embedded help"
src="yast_repositories.png" full_img="yast_repositories.png" %}

This will start the YaST module as usually, there is no visible change. You can
normally use the module, the only change is that the UI now listens on the port
14155 for incoming HTTP requests.

You can access the REST API (*root* not required) from command line like this:

```console
> # dump the current dialog as a tree
> curl 'http://localhost:14155/dialog'
{
  "class" : "YDialog",
  "hstretch" : true,
  "type" : "wizard",
  "vstretch" : true,
  "widgets" : 
  [
    {
      "class" : "YWizard",
      "debug_label" : "Configured Software Repositories",
      "hstretch" : true,
      "id" : "wizard",
      "vstretch" : true,
      "widgets" : 
      [
...
> # dump the current dialog as a simple list (easier iteration, search)
> curl 'http://localhost:14155/widgets'
[
  {
    "class" : "YDialog",
    "hstretch" : true,
    "type" : "wizard",
    "vstretch" : true
  },
  {
    "class" : "YWizard",
    "debug_label" : "Configured Software Repositories",
    "hstretch" : true,
    "id" : "wizard",
    "vstretch" : true
  },
...
> # filter widgets by ID
> curl 'http://localhost:14155/widgets?id=enable'
[
  {
    "class" : "YCheckBox",
    "debug_label" : "Enabled",
    "id" : "enable",
    "label" : "E&nabled",
    "notify" : true,
    "value" : true
  }
]
> # filter widgets by label (as displayed, might be translated!)
> curl 'http://localhost:14155/widgets?label=Priority'
[
  {
    "class" : "YIntField",
    "debug_label" : "Priority",
    "hstretch" : true,
    "id" : "priority",
    "label" : "&Priority",
    "max_value" : 200,
    "min_value" : 0,
    "notify" : true,
    "value" : 99
  }
]
> # filter widgets by type (all check boxes in this case)
> curl 'http://localhost:14155/widgets?type=YCheckBox'
[
  {
    "class" : "YCheckBox",
    "debug_label" : "Enabled",
    "id" : "enable",
    "label" : "E&nabled",
    "notify" : true,
    "value" : true
  },
  {
    "class" : "YCheckBox",
    "debug_label" : "Automatically Refresh",
    "id" : "autorefresh",
    "label" : "Automatically &Refresh",
    "notify" : true,
    "value" : true
  },
  {
    "class" : "YCheckBox",
    "debug_label" : "Keep Downloaded Packages",
    "id" : "keeppackages",
    "label" : "&Keep Downloaded Packages",
    "notify" : true,
    "value" : false
  }
]
> # and finally do some action - press the [Abort] button and close the module,
> # you can use the same filters as in the queries above
> curl -X POST 'http://localhost:14155/widgets?id=abort&action=press'
```

To make it cool I have included some inline documentation or help, just open
`http://localhost:14155` URL in your browser:

{% include blog_img.md alt="Embedded help"
src="libyui_http_help.png" full_img="libyui_http_help.png" %}


### Running a Cucumber Test

I have also written a simple set of step definitions for the [Cucumber](
https://cucumber.io/) framework. This allows using the REST API in Cucumber
integration tests.

Let's have this example Cucumber test:

```cucumber
Feature: To install the 3rd party packages I must be able to add a new package
  repository to the package management.

  Background:

    # make sure the tested repository does not already exist
    When I run `zypper repos --uri`
    Then the output should not contain "https://download.opensuse.org/tumbleweed/repo/oss/"

  Scenario: Aborting the repository manager keeps the old settings

    Given I start the "/usr/sbin/yast2 repositories" application
    Then the dialog heading should be "Configured Software Repositories"

    When I click button "Add"
    Then the dialog heading should be "Add On Product"

    When I click button "Next"
    Then the dialog heading should be "Repository URL"

    When I enter "Tumbleweed OSS" into input field "Repository Name"
    And I enter "https://download.opensuse.org/tumbleweed/repo/oss/" into input field "URL"
    And I click button "Next"
    Then the dialog heading should be "Tumbleweed OSS License Agreement" in 60 seconds

    When I click button "Next"
    Then the dialog heading should be "Configured Software Repositories"

    When I click button "Cancel"
    Then a popup should be displayed
    And a label including "Abort the repository configuration?" should be displayed

    Then I click button "Yes"
    And I wait for the application to finish

    # verify that the tested repository was NOT added
    When I run `zypper repos --uri`
    Then the output should not contain "https://download.opensuse.org/tumbleweed/repo/oss/"
``````

If you run it the result would look like this:

{% include blog_img.md alt="Adding a Repository in YaST"
src="cucumber_add_repo_test.gif" full_img="cucumber_add_repo_test.gif"%}

Pretty nice isn't it?

## Generic Usage

Because the REST API is implemented on the UI (libyui) level it means that any
application using the library can be tested the same way, it is not limited only
to YaST. For example it should be very easy to write tests also for the
[Mageia tools](https://madb.mageia.org/package/show/name/manatools) included
in the Megeia distribution.


## TODO

There are lots of missing features, mainly:

- Return more details in the response, add missing attributes
- Support more actions besides clicking a button or filling an `InputField`
- Support more UI calls (e.g. `UI.PollInput`)
- Support the other UIs (ncurses, maybe GTK)
- Support for the package manager widget (implemented in the `libyui-*-pkg` subpackages)
- Packaging - make the feature optional (if you do not want to include the web
server for security reasons)
- Add more step definitions in the Cucumber wrapper

### Optional Features

- User authentication (unauthenticated access is a security hole, but it might be
  OK when running tests in a sandboxed environment in a trusted network)
- SSL support (securely transfer the current values and set the new values, e.g.
  the root password)
- IPv6 support (it's the future, isn't it?)
- Unix socket support (another kind of user authorization)

## Conclusion

The feature is far from being production ready, it is still rather a proof
of concept. But it shows that it works well and we can continue in this
direction in the future.

[1]: {{ site.baseurl }}{% post_url 2017-03-01-hackweek-15-yast-cucumber %}