---
layout: post
title: Jekyll::Compose - A must-have plugin
subtitle: Streamline your writing in Jekyll with these commands
tags: [github-pages, jekyll, quick-take]
date: 2020-09-04 11:48 -0700
---
As you may have noticed, I just recently started this Jekyll blog and still getting the hang of it.

In my [previous post]({% post_url 2020-08-31-welcome %}), I suggested a couple of extensions you could use in Visual Studio Code for markdown without too much tinkering. And while these extensions are very useful, I just found `jekyll-compose`, and boy is it handy!

As suggested [here](https://github.com/jekyll/jekyll-compose/blob/master/README.md#installation), just add that line to your `Gemfile`, so it looks like this:

```ruby
gem 'jekyll-compose', group: [:jekyll_plugins]
```

Or if you plan to "grow", do this instead:

```ruby
group :jekyll_plugins do
    gem 'jekyll-compose'
end
```

After successfully installing it, just by typing the following command I get a new drat under the `_drafts` folder:

```bash
bundle exec jekyll draft "New Draft"
```

And `jekyll-compose` takes care of properly naming the file, so you can take care of the important stuff.

### Summary

1. Add `jekyll-compose` to your `Gemfile`
1. Execute `bundle` command
1. `bundle exec jekyll draft "{title}"`
1. Write your post
1. `bundle exec jekyll publish _drafts/{title}.md`
1. Commit and push
1. Success!

Don't forget to read the documentation!
