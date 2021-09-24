---
layout: post
title: How to verify your GitHub Organization's domain on Google Domains
tags:
- github
- google
- quick-take
- tutorial
date: 2020-10-14 18:07 -0700
---
Recently I purchased the thecesrom.dev domain at Google Domains, as it was cheaper on Google than on Namecheap. And inspired by that I decided that I would also create an organization, so [coatl.dev](https://github.com/coatl-dev){:target="_blank"} was born.

I am not yet sure what I will do with it, but one thing is for certain, it will be related to Python. Hence, [Coatl](https://en.wikipedia.org/wiki/Coatl){:target="_blank"}.

And once that orgranization was created, I wanted to verify the domain controlled by that organization. So I followed GitHub's guide on [verifying an organization's domain](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/verifying-your-organizations-domain){:target="_blank"}, but something was not properly working.

DuckDuckGoing I stumbled upon an article titled ["Verify GitHub Organization’s Domain on Namecheap"](https://medium.com/@derekfong/verify-github-organizations-domain-on-namecheap-9b2af148679a){:target="_blank"} by a certain [Derek Fong](https://medium.com/@derekfong){:target="_blank"} that did solve the issue where GitHub was unable to verify my organization's domain.

So here are the steps I followed for Google Domains:

1. Go to your Github's organization's profile page
1. Navigate to **Settings** > **Verified domains** and click on **Add a domain**
1. Enter your domain, and click on **Add domain**, e.g.`example.com`
1. GitHub will require you to add a `DNS TXT` record, e.g. `_github-challenge-<ORGANIZATION_NAME>.example.com.`, and it will also provide a value for that record
1. Take note of both, and head over to **Google Domains**
1. Click on **Manage** on the domain you're attempting to verify
1. Select **DNS** from the left pane and scroll all the way down to **Custom resource records**
1. On the **Name** field enter `_github-challenge-<ORGANIZATION_NAME>`. Note that we're not adding `.example.com.` as GitHub suggests
1. Select `TXT` as **Type** from the dropdown
1. Leave the `1H` setting for **TTL**
1. On the **Data** field paste the code from GitHub, and click on **Add**. The following notification will appear: "Changes to example.com saved. They'll take effect within the next 48 hours."
1. Go back to the verification process on GitHub, and click on **Verify domain**, which will tell you that the verification process might take up to 72 hours

And as easy as that, you're done.
