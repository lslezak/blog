---
layout: post
title: Renaming a Rake task
tags: [ rake ]
---

Rake has a class for creating packaging task which builds a compressed package
from the sources. It's called `Rake::PackageTask` and it's quite easy to use it,
[see the documentation](http://rake.rubyforge.org/classes/Rake/PackageTask.html).

Unfortunately the name of the created task is `package` and it's hardcoded so it
cannot be changed.

Yast uses `make` and the standard targets are `package` and `package-local`.

- `package-local` - builds a tarball source package
- `package` - does some checks (like SVN repository update check, syntax check,
   runs the testsuite...) to ensure that the built package follows some rules so
   it will less likely break something.

We wanted to use the same targets in the WebYaST which uses Ruby on Rails and
Rake as the make replacement.

The problem is how to change the package task name. In Ruby it's possible to
change or extend even the core classes like `String` or `Object`, which might be
good or bad because you can break all Ruby applications very easily... But this
feature allow us to extend Rake as we need.

The following code adds `rename` method to the `Rake::Task` class:

```ruby
# add rename_task method to Rake::Application
# it has an internal hash with name -> Rake::Task mapping
module Rake
  class Application
    def rename_task(task, oldname, newname)
      @tasks = {} if @tasks.nil?
      @tasks[newname.to_s] = task

      @tasks.delete(oldname)  if @tasks.has_key?(oldname)
    end
  end
end

# add new rename method to Rake::Task class
# to rename a task
class Rake::Task
  def rename(new_name)
    if !new_name.nil?
      old_name = @name

      return if old_name == new_name

      @name = new_name.to_s
      application.rename_task(self, old_name, new_name)
    end
  end
end
```

And an example how to use it:

```ruby
# rename 'package' task to 'package-local' task
Rake::Task[:package].rename(:"package-local")
```

It was quite difficult to figure out the rake internals, so I hope it will help somebody else...
