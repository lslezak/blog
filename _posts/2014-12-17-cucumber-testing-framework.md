---
layout: post
title: Cucumber Testing Framework
category: Programming
tags: [ testing ]
---

[Cucumber](http://cukes.info/) is BDD ([behaviour driven
development](https://en.wikipedia.org/wiki/Behavior-driven_development))
framework. In contrast to other BDD frameworks (like
[RSpec](http://rspec.info/)) the specification is written in a natural human
readable language.

### Rubocop-yast

I wanted to write tests for the new Rubocop-yast plugin in some nice way. I
started with RSpec, but the tests looked ugly (writing multiline indented Ruby
code in a string literal requires extra escaping which makes it quite hard to
read...).

Then I looked at the original [Zombie Killer
tests](https://github.com/yast/zombie-killer/blob/master/spec/zombie_killer_spec.md).
They are written in Markdown so they are better readable, you can write some
additional comments to the test etc... And how to run the Markdown tests? There
is a custom Markdown renderer which converts the Markdown specification into a
RSpec test.

Looks nice, but having a custom renderer makes it difficult, we have to
maintain it and the Markdown format in specific, there is nothing else similar
to it...

Then Cucumber come to my mind! It exactly fits our need! The specification
allows to write extra comments and notes, it's readable almost like our Markdown
and is a standard tool. The killer feature is [multiline docstring
parameter](http://cukes.info/step-definitions.html#doc-strings). It allows to
write indented Ruby code directly without any extra escaping.

You can check how the tests looks
[here](https://github.com/yast/rubocop-yast/tree/master/features), check the
`*.feature` files.
[Here](https://github.com/yast/rubocop-yast/blob/master/features/step_definitions/cop_steps.rb)
is the code which converts the specification into the testing code.

### Pros &amp; Cons

Here is the summary of pros &amp; cons I found when starting with Cucumber:

**Advantages**

* Specification in natural language, readable tests and user stories
* Allows to use other testing framework for running the real tests (like RSpec)

**Disadvantages**

* Extra code for converting the textual specification to Ruby code
* One more layer between test description and the code (you need to make sure
  the code really matches the description)
* Test descriptions should describe the high level features (usually the user
  interaction), they should not describe the low-level implementation details


Ideally the features should be written by managers, designers or other
non-technical people. They can describe the required features without any
programming skills. That's probably the most important Cucumber feature.

### Suitable for Yast?

In my opinion not. Why? We are usually focused on low-level features and we
would probably need too much code for converting the specifications to tests.
And the tests are usually too different, we would need to write extra conversion
for each test and maintain it. The overall benefit would be small in my opinion
and would not be worth of doing it.

Moreover the feature descriptions we usually get are hard to convert to a
testing code, they are usually too generic or cannot be tested in unit tests
(e.g. the installer features).

### Links

* <http://cukes.info/>
* <http://cukes.info/step-definitions.html>
* <https://blog.engineyard.com/2009/15-expert-tips-for-using-cucumber/>
* <http://antonymarcano.com/blog/2010/08/youre-almost-cuking-it/>
* <http://www.elabs.se/blog/15-you-re-cuking-it-wrong>