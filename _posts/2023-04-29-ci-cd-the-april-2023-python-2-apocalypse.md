---
layout: post
title: CI/CD - The April 2023 Python 2 apocalypse
tags:
- azure pipelines
- continuous delivery
- continuous integration
- github
- python
- python2
date: 2023-04-29 15:14 -0700
---
Let's begin by addressing why I still use Python 2.

## Why Python 2?

I've written multiple times about [Python 2]({{ site.url }}/tags#python2) and how I still use it for work and personal projects both locally and on my CI/CD pipelines on GitHub and Azure DevOps.

I am well aware Python 2 reached EOL, but I thought I had more time to stop using Python 2.7, and I based that on Ubuntu's [lifecycle](https://ubuntu.com/about/release-cycle). [Ubuntu 20.04](https://ubuntu.com/about/release-cycle#ubuntu), as an LTS release, it will be supported by Canonical until [2025](https://ubuntu.com/blog/ubuntu-server-20-04), and I was hoping GitHub would take Python 2.7 along for the ride until they deprecate the `ubuntu-20.04` runner.

But specifically, I use Python 2 for testing installation using `tox` and building and publishing my packages to PyPI and Azure Artifacts. Granted, I could live without the former, but the latter is a luxury hard to drop; going back to publishing manually would be the boring solution, and who likes boring?

## The bad news

Since the people behind GitHub's [runner-images](https://github.com/actions/runner-images) switched `ubuntu-latest` from `ubuntu-20.04` to `ubuntu-22.04`, I decided to "watch" their releases so I could be ahead of the game. And it was on April 10, 2023 when I got the news: [python2.7 will be removed from the images on May 15, 2023](https://github.com/actions/runner-images/issues/7401).

While this was to be expected considering Python 2 saw its last release back in April 20, 2020, May 2023 was most definitely unexcepted. But instead of groveling, let's find a solution.

## The options

For my projects at work the obvious option is to switch from [Microsoft-hosted agents](https://learn.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser#microsoft-hosted-agents) to [self-hosted agents](https://learn.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser#install) which can be executed on one or more Servers, but for my public repos on GitHub things get a little bit trickier since it is recommended using [self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners) with [private repositories](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners#self-hosted-runner-security).

### Renting a VM

Here I have explored Linode's [Shared CPU options](https://www.linode.com/products/shared/), Azure's [Bs-series](https://learn.microsoft.com/azure/virtual-machines/sizes-b-series-burstable), and Google's [E2 shared-core machine types](https://cloud.google.com/compute/all-pricing#sharedcore), but I have found that Google Cloud offers a free tier for their [Cloud Build](https://cloud.google.com/build/pricing) service where, at the time of this writing, the first 120 build-minutes per day are free, and free is always good.

So I've already started learning about [Cloud Build](https://cloud.google.com/build/docs) and [creating build triggers](https://cloud.google.com/build/docs/automating-builds/create-manage-triggers) to [build repositories from GitHub](https://cloud.google.com/build/docs/automating-builds/github/build-repos-from-github?generation=1st-gen) and considering whether I should have the [Cloud Build config file](https://cloud.google.com/build/docs/build-config-file-schema) in my repos, inline on my trigger configuration or a Dockerfile.

### Conclusion

Again, it was expected Python 2 would eventually be dropped, and as long as Ignition by Inductive Automation solely depends on Jython 2.7 all of us will be stuck with maintaining and supporting Python 2. But I'm glad we still have options and will report my experience in future posts.

In the meantime let's keep coding.

Thanks for reading.
