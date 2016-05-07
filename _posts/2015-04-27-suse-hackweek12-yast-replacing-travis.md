---
layout: post
title: 'SUSE HackWeek12 - YaST: Replacing Travis by Jenkins'
category: Programming
tags: [ yast, CI ]
---

# Replacing Travis by Jenkins for YaST

I decided to look at the possible Travis replacement as my Hackweek 12 project.  

Currently we use both [Travis](https://travis-ci.org/) and
[Jenkins](https://ci.opensuse.org/view/Yast/) for continuous integration in YaST
projects. Unfortunately there are many disadvantages with Travis which require
additional work or limit us what we can run in a Travis job. See [the hackweek
project](https://hackweek.suse.com/projects/888) for the pros and cons summary.  

The biggest Travis disadvantage is that the builds are run in a Ubuntu 12.04
system which is 3 years old and it's very difficult to find a recent compiler,
Ruby interpreter, libraries,... for it.

The Jenkins advantage is that it runs on our server and we can run the
latest openSUSE very easily and avoid the problem with porting YaST packages to
Ubuntu and backporting the development tools.

## Jenkins Plugins

I found out that there are several Jenkins plugins which could be used to
replace Travis with Jenkins:</div>

*   The [GitHub plugin](https://wiki.jenkins-ci.org/display/JENKINS/Github+Plugin) - handlers commit hooks sent by GitHub, reports build status back
*   The [GitHub Pull Request Builder](https://wiki.jenkins-ci.org/display/JENKINS/GitHub+pull+request+builder+plugin) - handles pull request hooks sent by GitHub (similar to the previous plugin, but builds the merged result)
*   The [Embeddable build status plugin](https://wiki.jenkins-ci.org/display/JENKINS/Embeddable+Build+Status+Plugin) - provides a build status badge (similar to Travis one)

You can install the plugins in your Jenkins instance like this:  

*   Login into Jenkins
*   Go to _Manage Jenkins_ -> _Manage Plugins_
*   In the tab _Available_ select plugins _GitHub Plugin_, _GitHub pull request builder plugin_ and _Embeddable Build Status Plugin_ and install them

### Configuring the GitHub Plugin

*   [Generate a new access token at GitHub](https://github.com/settings/tokens/new), select _public_repo_ and _repo:status_. If you want to allow automatic webhook setup select _write:repo_hook_ (you can add/remove the permissions later).
*   Add the token to Jenkins -> _Manage Jenkins_ -> _Configure System_ -> _GitHub Web Hook_ section -> _OAuth token_ field
*   Put the same token to the "GitHub Pull Request Builder" section, "Access Token" filed.

#### Create a new Jenkins job for building commits (pull requests are handled separately):

*   Select _Freestyle project_
*   Put the Github URL (https://github.com/<user>/<repo>) to the _GitHub project_ filed</repo></user>
*   In the _Source Code Management_ section select Git and put the same URL here
*   Make _Branch Specifier_ field empty to build all branches
*   In the _Build Triggers_ section - check _Build when a change is pushed to GitHub_
*   Add Build Step -> select Set build status to "pending" on GitHub commit
*   Add post-build action - Set build status on GitHub commit
*   Configure the other parameters of the build as needed

See more details [here](https://wiki.jenkins-ci.org/display/JENKINS/GitHub+Plugin).  

### Configuring the GitHub Pull Request Builder

*   Add the created GitHub token to Jenkins -> _Manage Jenkins_ -> _Configure System_ -> _GitHub Pull Request Builder_ section

#### Create a new job for building pull requests:

*   Select _Free style project_
*   Put the Github URL (https://github.com/*user*/*repo*) to the _GitHub project_ filed
*   In the _Source Code Management_ section select Git and put the same URL here
*   In _Advanced_ option set _Name_ to **origin** and _Refspec_ to **+refs/pull/*:refs/remotes/origin/pr/***
*   Set _Branch Specifier_ to **${sha1}**
*   In the _Build Triggers_ section - check _GitHub Pull Request Builder_ option
*   Check _Use github hooks for build triggering_ option
* Set _Commit Status Context_ to **continuous-integration/jenkins-ci/pr**
  (or something like that to have a different ID for pull requests and avoid
  clashing with the GitHub plugin configured in the previous step

Set Admins or white list users so you do not have to manually trigger builds for
trusted developers - the plugin avoids running trusted code in your node.

### Using Both Plugins in One Job?

I tried to use both plugins in a single job to have less jobs but it did not
work form me, the pull requests were not processed at all or it did not work at
all.

If you find how to solve it let me know...

### Coveralls Support?

The question was whether [coveralls](https://coveralls.io/) code coverage can
run also outside the Travis environment.  

I found out that it is possible, you just need to set the `COVERALLS_REPO_TOKEN`
environment variable and some other variables containing the Git branch, build
number, etc... See more details
[here](https://coveralls.zendesk.com/hc/en-us/articles/201347419-Coveralls-currently-supports).  

You need to store the token into Jenkins and set it during build.  (You can put
the token directly into the `.coveralls.yml` file, but that's not a good idea
for a public Git repository...) Fortunately there is a Jenkins plugin which
helps with this, it can inject a secret in to a environment variable. The nice
feature is that it can also filter the console output to make sure your
password/token does not leak in the build output.

## The Current State

I have created some experimental jobs to try the plugins, e.g. [yast-registration-github-ci](https://ci.opensuse.org/view/Yast/job/yast-registration-github-ci/), [yast-devtools-github-ci](https://ci.opensuse.org/view/Yast/job/yast-devtools-github-ci/) or [yast-journal-github-ci](https://ci.opensuse.org/view/Yast/job/yast-journal-github-ci/).  

The jobs are not ready to fully replace the Travis integration, but they work and I my plan is to continue with this project later.  

## TODO?

There are still many thing to solve:  

* Coveralls - pass the Git data (branch name, etc.) from Jenkis, set the
  `COVERALLS_REPO_TOKEN` variable, make sure it's not logged in the console to
  avoid compromising the value

* Find a way how to define the scripts started in Jenkins. We need to replace
  the _.travis.yml_ files with something equivalent and share the common parts
  effectively, very likely as a shared Rake task.

* The current Jenkins jobs use active Git polling, we should switch that to use
  the GitHub web hooks 

So stay tuned, I'll post updates on this topic...  

## Bonus Section

During the implementation I found some other interesting possibilities:

### Collecting Code Metrics and Code Coverage

The RubyMetrics Jenkins plugin can be used to collect Flog code metrics and RCov
code coverage statistics:

![](http://3.bp.blogspot.com/-NwAINs2_ZF8/VTUNNIN0VcI/AAAAAAAANkI/xPmeuEtB3pk/s1600/jenkins_rubymetrics1.png)

![](http://3.bp.blogspot.com/-NKmO_acNtPc/VTUNNKkxuzI/AAAAAAAANkE/AbvkiSFNZkE/s1600/jenkins_rubymetrics2.png)

That looks nice, but the practical value is low. The code coverage can be
collected by Coveralls, which can comment the changes in pull requests, and code
quality can be scanned e.g. by CodeClimate which provides more details about the
code and better evaluates the code quality in general. More over it offers some
hint what and how to fix, not just plain numbers without any clue what's wrong
with the code.  

So I decided to not use them for YaST.

### Writing Jenkins Plugins in Ruby

I found an interesting possibility when playing with Jenkins - it's possible to
[write a Jenkins plugins in
Ruby](https://github.com/jenkinsci/jenkins.rb/wiki/Getting-Started-With-Ruby-Plugins)!
There is also an [example](https://github.com/masaki/jenkins-travis-yml-plugin).
Maybe we could simple enhance Jenkins if needed...  

