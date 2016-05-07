---
layout: post
title: Switching from Gettext to FastGettext in a Rails3 app
category: Programming
tags: [ Ruby ]
---

# From Gettext to FastGettext

In
[SLMS](http://www.suse.com/products/susestudio/features/lifecycle-management.html)
we use Gettext for i18n support. Unfortunately it doesn't work with new Rails
3\. But we found out that there is
[FastGettext](https://github.com/grosser/fast_gettext) Ruby gem which does work
with Rails 3 and we decided to switch to this different implementation.  

In this blogpost I'll describe the needed steps when switching from Gettext to
FastGettext. And here also are solutions for some problems we found during the
transition.  

# Using the new Ruby gems

You will need these new Ruby gems:

*   fast_gettext: [https://github.com/grosser/fast_gettext](https://github.com/grosser/fast_gettext)
*   gettext_i18n_rails: [https://github.com/grosser/gettext_i18n_rails](https://github.com/grosser/gettext_i18n_rails) _(FastGettext Rails integration)_
*   rails-i18n: [https://github.com/svenfuchs/rails-i18n](https://github.com/svenfuchs/rails-i18n) _(Default Rails translations)_

The first step is to remove the old Gettext gems and replace them by FastGettext
gems. So replace these gems in your Gemfile:  

```ruby
gem 'locale'
gem 'locale_rails'
gem 'gettext'
gem 'gettext_activerecord'
gem 'gettext_rails'
```

by

```ruby
gem 'fast_gettext'

# 0.4.3 contains fixes in
#'rake gettext:store_model_attributes' task
gem 'gettext_i18n_rails', '>= 0.4.3'

# rails-i18n provides translations for ActiveRecord
# validation error messages
gem 'rails-i18n'

# needed to collect translatable strings
# not needed at production
group :development do
  # needed for HAML support (optional)
  gem 'ruby_parser'

  # no need to load the gem via require
  # we only need the rake tasks
  gem 'gettext', '>= 1.9.3', :require => false
end
```

Then you need to initialize FastGettext, create
`config/initializers/fast_gettext.rb` file: 

```ruby
# define your text domain
FastGettext.add_text_domain 'foo', :path => File.join(File.dirname(__FILE__), '..', '..', 'locale')


# set the default textdomain
FastGettext.default_text_domain = 'foo'

# set available locales
# (note: the first one is used as a fallback if you try to set an unavailable locale)
FastGettext.default_available_locales = ["en_US","ar","cs","de","es",...]
```


Replace `foo` with your textdomain. Now you need to add *FastGettext*
initialization in your application controller:  

```ruby
class ApplicationController < ActionController::Base
  # replace these old Gettext calls:
  #   init_gettext "your_domain"
  #   GetText.textdomain("your_domain")
  # by this:
  include FastGettext::Translation

  before_filter :set_users_locale 

  def set_users_locale
    I18n.locale = FastGettext.set_locale(params[:locale] || cookies[:locale] ||
      request.env['HTTP_ACCEPT_LANGUAGE'] || 'en_US')
    cookies[:locale] = I18n.locale if cookies[:locale] != I18n.locale.to_s
  end
end 
```

The set_users_locale before filter handles setting the correct locale for every
request. The locale is set via a cookie and can be changed using
`?locale=_locale_` URL option. It is possible to use different solution for
switching the locale, e.g. as a path prefix or a domain name - see [the Rails
guide](http://guides.rubyonrails.org/i18n.html)  

**Note:** The application needs to be restarted after any change in the
translations.  

# Solved Problems

## Automatic detection of available locales

Using fixed list in the available locales list might not be nice, especially if
you want to dynamically add new translations later. In this case you need to
find the available locales dynamically at start. The solution is to put this
code to `config/initializers/fast_gettext.rb` file:

```ruby
# put 'en_US' as first, the first item is used as a fallback
# when requested locale (via ?locale= URL parameter) is not found
FastGettext.default_available_locales = ["en_US"]

# get available locales automatically
Dir[File.join(File.dirname(__FILE__), '..', '..', 'locale', "/*/LC_MESSAGES/*.mo")].each do |l|
  if l.match(/\/([^\/]+)\/LC_MESSAGES\/.*\.mo$/) && !FastGettext.default_available_locales.include?($1)
    FastGettext.default_available_locales << $1
  end
end
```

## Language and Country Separator in locale name

Rails native localization support uses I18n module for translation support. The
problem is that it uses dash (-) separator between langugage and country code in
locale names.  

This makes a problem when using with standard gettext locale schema which uses
underscore (\_) as the separator. For example translations from `rails-i18n` gem
will not be found when the current locale in en_US, it expects en-US locale.  

The problem can be solved by defining locale fallbacks like this (put this to
<tt>config/initializers/fast_gettext.rb</tt> file):  

```ruby
# enable fallback handling
I18n::Backend::Simple.include(I18n::Backend::Fallbacks)

# set some locale fallbacks needed for ActiveRecord translations
# located in rails_i18n gem (e.g. there is en-US.yml translation)
I18n.fallbacks[:"en_US"] = [:"en-US", :en]
I18n.fallbacks[:"en_GB"] = [:"en-GB", :en]
I18n.fallbacks[:"pt_BR"] = [:"pt-BR", :pt]
I18n.fallbacks[:"zh_CN"] = [:"zh-CN"]
I18n.fallbacks[:"zh_TW"] = [:"zh-TW"]
I18n.fallbacks[:"sv"] = [:"sv-SE"]
```

This means that if for example a translation for _en_US_ locale is not found
then _en-US_ will be tried and then _en_ locale.  

## Including source file name and line number is the final POT file

By default when you run `rake gettext:find` task to collect the translatable
string the output will not contain the source file name and the line number.
It's very useful if you get a feedback from translator (like a typo in the
original message) then you don't have to scan all file but you immediately know
where to fix the problem.  

If you want to change this behavior and include the line numbers add this
configuration to `config/initializers/fast_gettext.rb` file:  

```ruby
# configure default msgmerge parameters (the default contains "--no-location" option
# which removes code lines from the final POT file)
Rails.application.config.gettext_i18n_rails.msgmerge = ["--sort-output", "--no-wrap"]
```

## Sorting messages in the final POT file

The `rake gettext:find` task sorts the messages in the final POT file
alphabetically. The advantage is that if you add a new string and regenerate the
file then the files will be similar and the diff will be small.  

The problem is that the sorting is done at the merge step, when merging the new
found translation wit the old ones. At the very first run (when the final POT
file does not exist yet) the merge step is skipped and thus the messages are not
sorted. This can be fixed by starting the task once more (the second run will
find existing messages and do the merge with sorting).  

But the problem is that you can easily forget to run the task for the second
run. The workaround is to create an empty target POT file when the it doesn't
exist yet. Unfortunately simple `touch` command is not sufficient (msgmerge
failed for me with some strange UTF-8 error), we have to create valid POT but
without any messages.  

The workaround it to put this code to `lib/tasks/gettext.rake` file:

```ruby
# 'gettext:find' sorts the messages alphabetically only when it is merging existing messages
# copying empty pot file from the template forces sorting even at the first run
namespace :gettext do
  task :create_pot_template do
    FileUtils.cp("locale/template.pot", "locale/textdomain.pot") unless File.exists?("locale/textdomain.pot")
  end
end

# add task dependency
task :'gettext:find' => :'gettext:create_pot_template'
```

The `locale/textdomain.pot` template should look like this:  

```
# SOME DESCRIPTIVE TITLE.
# Copyright (C) YEAR THE PACKAGE'S COPYRIGHT HOLDER
# This file is distributed under the same license as the PACKAGE package.
# FIRST AUTHOR <email@address>, YEAR.
#
#, fuzzy
msgid ""
msgstr ""
"Project-Id-Version: version 0.0.1\n"
"POT-Creation-Date: 2012-01-16 17:56+0100\n"
"PO-Revision-Date: 2012-01-16 17:56+0100\n"
"Last-Translator: FULL NAME <email@address>\n"
"Language-Team: LANGUAGE <ll@li.org>\n"
"MIME-Version: 1.0\n"
"Content-Type: text/plain; charset=UTF-8\n"
"Content-Transfer-Encoding: 8bit\n"
"Plural-Forms: nplurals=INTEGER; plural=EXPRESSION;\n"
```

## Automatic translation in HAML files

It is possible to extend HAML parser to automatically translate all plain text
strings. The advantage is than you don't have to explicitly use `_()` function and
you cannot forget to mark a text to translation.  

This can be done using [this code snippet](http://pastie.org/445295). Save it to
a file, remove the require calls at the beginning (they are obsoleted and do not
work with new gettext) and require it in your `ApplicationController`.  

Then you need to add support to `rake gettext:find` task. Save [this code
snippet](http://pastie.org/445297) to `lib/haml_parser.rb` file. You need to
replace `require 'gettext/parser/ruby` by `require
'gettext/tools/parser/ruby'` so it works with newer gettext gem.

Then put this to `lib/tasks/gettext.rake` file:

```ruby
# extend the HAML parser to extract plain text messages
# to support automatic translations (without need to mark the text with _())
namespace :gettext do
  task :haml_parser do
    require 'haml_parser'
  end
end

# extend the HAML parser before collecting the translatable texts
task :'gettext:find' => :'gettext:haml_parser'
```