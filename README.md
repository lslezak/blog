
# My Blog

[![Build Status](https://travis-ci.org/lslezak/blog.svg?branch=master)](
https://travis-ci.org/lslezak/blog)

This is my personal blog, it is published at https://blog.ladslezak.cz/.

## How To Generate the Pages locally

- Install the needed Ruby gems by `bundle install --path .vendor/bundle` command.
- Run `bundle exec jekyll serve --watch` to build the site and start
  the local server.
- Open http://127.0.0.1:4000/ in browser.

## Publishing

I use the [free Firebase hosting service](https://firebase.google.com/) for
publishing the blog.

- Build the pages locally
- Run `firebase deploy`
- See more details in the [deploy documentation](https://firebase.google.com/docs/hosting/deploying)
