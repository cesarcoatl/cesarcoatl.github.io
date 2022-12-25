---
layout: post
title: Welcome Again
subtitle: Or getting started with Jekyll and GitHub Pages (December 2022 Edition)
tags:
- github
- github-pages
- guide
- jekyll
- macos
- quick-take
- vscode
date: 2022-12-24 17:14 -0800
---
After a little over 10 years, my trustful [MacBook Air (13-inch, Mid 2012)](https://support.apple.com/kb/SP670?locale=en_US) has recently died. And as it was time to upgrade, I went for the [MacBook Air (M1, 2020)](https://support.apple.com/kb/SP825?locale=en_US).

And as I was getting myself ready to start blogging again, and testing my blog locally, I ran into an issue when I ran `bundle install` on my blog's root directory.

I installed `rbenv` with `brew`, Ruby version 2.7.4, and then this happened:

```sh
$ bundle install
# Cut for brevity
An error occurred while installing eventmachine (1.2.7), and Bundler cannot continue.
Make sure that `gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/'` succeeds before bundling.

In Gemfile:
  github-pages was resolved to 227, which depends on
    jekyll-avatar was resolved to 0.7.0, which depends on
      jekyll was resolved to 3.9.2, which depends on
        em-websocket was resolved to 0.5.3, which depends on
          eventmachine
```

So, as suggested I tried the `gem install` command:

```sh
$ gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/'
Building native extensions. This could take a while...
ERROR:  Error installing eventmachine:
	ERROR: Failed to build gem native extension.

    current directory: /Users/thecesrom/.rbenv/versions/2.7.4/lib/ruby/gems/2.7.0/gems/eventmachine-1.2.7/ext
/Users/thecesrom/.rbenv/versions/2.7.4/bin/ruby -I /Users/thecesrom/.rbenv/versions/2.7.4/lib/ruby/2.7.0 -r ./siteconf20221220-4681-wmlash.rb extconf.rb
checking for -lcrypto... no
checking for openssl/ssl.h... yes
checking for openssl/err.h... yes
checking for rb_trap_immediate in ruby.h,rubysig.h... no
checking for rb_thread_blocking_region()... no
checking for rb_thread_call_without_gvl() in ruby/thread.h... yes
checking for rb_thread_fd_select()... yes
checking for rb_fdset_t in ruby/intern.h... yes
checking for rb_wait_for_single_fd()... yes
checking for rb_enable_interrupt()... no
checking for rb_time_new()... yes
checking for inotify_init() in sys/inotify.h... no
checking for __NR_inotify_init in sys/syscall.h... no
checking for writev() in sys/uio.h... yes
checking for pipe2() in unistd.h... no
checking for accept4() in sys/socket.h... no
checking for SOCK_CLOEXEC in sys/socket.h... no
checking for sys/event.h... yes
checking for sys/queue.h... yes
checking for clock_gettime()... yes
checking for CLOCK_MONOTONIC_RAW in time.h... yes
checking for CLOCK_MONOTONIC in time.h... yes
CXXFLAGS=-g -O2 -Wall -Wextra -Wno-deprecated-declarations -Wno-ignored-qualifiers -Wno-unused-result -Wno-address
creating Makefile
# Cut for brevity
ld: symbol(s) not found for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [rubyeventmachine.bundle] Error 1

make failed, exit code 2

Gem files will remain installed in /Users/thecesrom/.rbenv/versions/2.7.4/lib/ruby/gems/2.7.0/gems/eventmachine-1.2.7 for inspection.
Results logged to /Users/thecesrom/.rbenv/versions/2.7.4/lib/ruby/gems/2.7.0/extensions/arm64-darwin-22/2.7.0/eventmachine-1.2.7/gem_make.out
```

Before my old MacBook Air died, I did manage to install Ubuntu 22.10 and didn't run into any issues running my blog. But since this was my first time trying M1, and having read `not found for architecture arm64`, it did look like an issue with M1's ARM architecture.

But without feeling defeated I went to `eventmachine`'s page at [RubyGems](https://rubygems.org/gems/eventmachine), and found its GitHub repo, and after a quick search I found the following this [issue comment](https://github.com/eventmachine/eventmachine/issues/960#issuecomment-1332076385).

```sh
$ gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/' -- --with-ldflags='-Wl,-undefined,dynamic_lookup'
Building native extensions with: '--with-ldflags=-Wl,-undefined,dynamic_lookup'
This could take a while...
Successfully installed eventmachine-1.2.7
Parsing documentation for eventmachine-1.2.7
Installing ri documentation for eventmachine-1.2.7
Done installing documentation for eventmachine after 1 seconds
1 gem installed
```

And only then I was able to run `bundle install` with no errors.

```sh
$ bundle install
Fetching gem metadata from https://rubygems.org/...........
Resolving dependencies...
Using concurrent-ruby 1.1.10
Using minitest 5.16.3
Using thread_safe 0.3.6
Using zeitwerk 2.6.6
Using public_suffix 4.0.7
Using bundler 2.3.26
Using execjs 2.8.1
Using colorator 1.1.0
Using coffee-script-source 1.11.1
Using commonmarker 0.23.6
Using unf_ext 0.0.8.2
Using http_parser.rb 0.8.0
Using faraday-net_http 3.0.2
Using eventmachine 1.2.7
Using ruby2_keywords 0.0.5
Using rb-fsevent 0.11.2
Using gemoji 3.0.1
Using forwardable-extended 2.6.0
Using rouge 3.26.0
Using safe_yaml 1.0.5
Using liquid 4.0.3
Using unicode-display_width 1.8.0
Using mercenary 0.3.6
Using unf 0.1.4
Using ffi 1.15.5
Using i18n 0.9.5
Fetching em-websocket 0.5.3
Using racc 1.6.1
Using tzinfo 1.2.10
Using faraday 2.7.2
Using pathutil 0.16.2
Using coffee-script 2.4.1
Using simpleidn 0.2.1
Using rexml 3.2.5
Using jekyll-commonmark 1.4.0
Using jekyll-paginate 1.1.0
Using addressable 2.8.1
Using rubyzip 2.3.2
Using jekyll-swiss 1.0.0
Using activesupport 6.0.6
Using ethon 0.16.0
Using sawyer 0.9.2
Using rb-inotify 0.10.1
Using kramdown 2.3.2
Using octokit 4.25.1
Using dnsruby 1.61.9
Using listen 3.7.1
Using terminal-table 1.8.0
Using jekyll-gist 1.5.0
Using jekyll-coffeescript 1.1.1
Using jekyll-watch 2.2.1
Using sass-listen 4.0.0
Using kramdown-parser-gfm 1.1.0
Using typhoeus 1.4.0
Using nokogiri 1.13.10 (arm64-darwin)
Using sass 3.7.4
Using html-pipeline 2.14.3
Using github-pages-health-check 1.17.9
Using jekyll-sass-converter 1.5.2
Installing em-websocket 0.5.3
Fetching jekyll 3.9.2
Installing jekyll 3.9.2
Fetching jekyll-feed 0.15.1
Fetching jekyll-avatar 0.7.0
Fetching jekyll-github-metadata 2.13.0
Fetching jekyll-optional-front-matter 0.3.2
Fetching jekyll-commonmark-ghpages 0.2.0
Fetching jekyll-include-cache 0.2.1
Fetching jekyll-mentions 1.6.0
Fetching jekyll-default-layout 0.1.4
Installing jekyll-feed 0.15.1
Installing jekyll-github-metadata 2.13.0
Installing jekyll-commonmark-ghpages 0.2.0
Installing jekyll-optional-front-matter 0.3.2
Installing jekyll-avatar 0.7.0
Installing jekyll-mentions 1.6.0
Installing jekyll-include-cache 0.2.1
Installing jekyll-default-layout 0.1.4
Fetching jekyll-readme-index 0.3.0
Fetching jekyll-redirect-from 0.16.0
Fetching jekyll-relative-links 0.6.1
Fetching jekyll-remote-theme 0.4.3
Fetching jekyll-seo-tag 2.8.0
Fetching jekyll-sitemap 1.4.0
Fetching jekyll-titles-from-headings 0.5.3
Fetching jemoji 0.12.0
Installing jekyll-readme-index 0.3.0
Fetching jekyll-compose 0.12.0
Installing jekyll-relative-links 0.6.1
Installing jekyll-redirect-from 0.16.0
Installing jekyll-remote-theme 0.4.3
Installing jekyll-titles-from-headings 0.5.3
Installing jemoji 0.12.0
Installing jekyll-sitemap 1.4.0
Installing jekyll-compose 0.12.0
Installing jekyll-seo-tag 2.8.0
Fetching jekyll-theme-merlot 0.2.0
Fetching jekyll-theme-dinky 0.2.0
Fetching jekyll-theme-hacker 0.2.0
Fetching jekyll-theme-architect 0.2.0
Fetching jekyll-theme-leap-day 0.2.0
Fetching jekyll-theme-midnight 0.2.0
Fetching jekyll-theme-cayman 0.2.0
Fetching jekyll-theme-minimal 0.2.0
Installing jekyll-theme-dinky 0.2.0
Fetching jekyll-theme-modernist 0.2.0
Installing jekyll-theme-hacker 0.2.0
Fetching jekyll-theme-primer 0.6.0
Installing jekyll-theme-cayman 0.2.0
Fetching jekyll-theme-slate 0.2.0
Installing jekyll-theme-architect 0.2.0
Installing jekyll-theme-minimal 0.2.0
Fetching jekyll-theme-tactile 0.2.0
Installing jekyll-theme-modernist 0.2.0
Installing jekyll-theme-merlot 0.2.0
Fetching jekyll-theme-time-machine 0.2.0
Installing jekyll-theme-slate 0.2.0
Installing jekyll-theme-primer 0.6.0
Fetching minima 2.5.1
Installing jekyll-theme-leap-day 0.2.0
Installing jekyll-theme-tactile 0.2.0
Installing minima 2.5.1
Installing jekyll-theme-midnight 0.2.0
Installing jekyll-theme-time-machine 0.2.0
Fetching github-pages 227
Installing github-pages 227
Bundle complete! 2 Gemfile dependencies, 92 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

Happy blogging!
