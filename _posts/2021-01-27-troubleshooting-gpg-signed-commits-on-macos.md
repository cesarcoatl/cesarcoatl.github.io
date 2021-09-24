---
layout: post
title: Troubleshooting GPG signed commits on macOS
tags:
- git
- github
- gpg
- quick-take
- tutorial
date: 2021-01-27 11:24 -0800
---
### Introduction

I've been using GitHub for the past couple of years, but it was not until last year that my contribution activity increased. Some PRs [here](https://github.com/justinlettau/sql-source-control/pull/133) and some others [here](https://github.com/Homebrew/homebrew-cask-versions/pulls?q=is%3Apr+author%3Athecesrom) and [there](https://github.com/Homebrew/homebrew-cask/pulls?q=is%3Apr+author%3Athecesrom).

I have created [an organization](https://github.com/coatl-dev), started [sponsoring](https://github.com/thecesrom?tab=sponsoring), and switched from HTTP to SSH following [this useful guide](https://docs.github.com/articles/generating-an-ssh-key/).

But yesterday a user's commit `Verified` label caught my attention, so I decided to grab a cup of coffee and learn about [managing commit signature verification](https://docs.github.com/articles/generating-a-gpg-key/), and after following all the necessary steps I was able to generate my GPG key and upload it to GitHub, but there was an issue, which is the main topic of this post.

### The problem

At the [Signing commits](https://docs.github.com/en/github/authenticating-to-github/signing-commits) step I was about to sign my first commit but then tragedy struck.

```bash
$ git commit -S -m "First signed commit"
error: gpg failed to sign the data
fatal: failed to write commit object
```

I was not sure what was going, so I retraced my steps, and I even found [some](https://github.com/pstadler/keybase-gpg-github#troubleshooting-gpg-failed-to-sign-the-data) [possible](https://stackoverflow.com/a/41054093) solutions, but none of them worked. And I even deleted my keys, and started again but then, similar to this [user's symptoms](https://unix.stackexchange.com/questions/571597/gpg-key-gen-fails-no-such-file-or-directory), I got the following:

```bash
$ gpg --full-generate-key
...
gpg: agent_genkey failed: No such file or directory
Key generation failed: No such file or directory
```

What is this? Let's see what `$GPG_TTY` has to say:

```bash
$ echo $GPG_TTY
not a tty
```

What? That doesn't make sense! – I thought to myself.

But finally, I saw the light.

### The solution

I found [this great guide](https://eriksamuelsson.com/sign-git-commits-on-github-with-gpg-in-macos/) on how to set up GPG, and some useful [Homebrew](https://brew.sh/) formulae, courtesy of [Erik Samuelsson](https://github.com/samuelsson).

But all of the guides that I found did not take into account my current setup; I am using [Powerlevel10k](https://github.com/romkatv/powerlevel10k) by [Roman Perepelitsa](https://github.com/romkatv/), and he had [the answer](https://unix.stackexchange.com/a/608921) to my problem.

>tty command requires that stdin is attached to a terminal. When using Powerlevel10k, stdin is redirected from /dev/null when Instant Prompt is activated and until Zsh is fully initialized. This is explained in more detail in Powerlevel10k FAQ.
>
>To solve this problem you can either move export GPG_TTY=$(tty) to the top of ~/.zshrc so that it executes before Instant Prompt is activated, or (better!) use export GPG_TTY=$TTY. The latter version will work anywhere and it's over 1000 times faster. TTY is a special parameter set by Zsh very early during initialization. It gives you access to the terminal even when stdin might be redirected.
>
>answered Sep 11 '20 at 7:28 by [Roman Perepelitsa](https://unix.stackexchange.com/users/363991/roman-perepelitsa)

So there it was. Instead of `GPG_TTY=$(tty)` somewhere in my `.zshrc`, all I needed was `GPG_TTY=$TTY`.

```bash
$ exec "$SHELL"
$ echo $GPG_TTY
/dev/ttys000
```

And ever since, all my commits are signed by updating my `git` settings:

```bash
git config --global user.signiningkey XXXXXXXXXXXXXXXX
git config --global commit.gpgsign true
```

And after installing `pinentry-mac`, I don't have to enter my passphrase every single time.

```bash
echo 'pinentry-program /usr/local/bin/pinentry-mac' >> ~/.gnupg/gpg-agent.conf
killall gpg-agent
```

And done!

Hope this helps.
