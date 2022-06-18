---
layout: post
title: Getting more analytics for a free Cloudflare account
date: 2021-08-13
redirect_from:
  - /2021/08/13/getting-more-analytic-data-from-a-free-cloudflare-account/
categories: [Cloudflare, Analytics]
tags: [cloudflare,analytics,free]
---
Ever wished that you could get more Cloudflare analytics for free? But don’t want to upgrade your Cloudflare account? So here’s a hacky way to get a little more data out of the free features that you can access.

![Cloudflare domain overview dashboard](/media/posts/images/2021-08-13-cloudflare/Screenshot-from-2021-08-13-01-19-50.png)

## The preamble
As you can see here I have my Cloudflare domain overview dashboard. Now I understand why there are limits to what the free account can access. Storing and crunching through a lot of data can be expensive. And yes there is an analytics page but for my site, it’s extremely sparse in both data and views.

![Cloudflare analytics dash](/media/posts/images/2021-08-13-cloudflare/Screenshot-from-2021-08-13-01-26-05.png)

Now. There is an obvious solution. We could pay for the Pro plan (which at $20 might just be a bargain). Or we could upgrade a WordPress plugin (once again this involves money). But as you can see from my overview, I don’t get a lot of traffic. This now makes me think it might not be worth it.

## The gathering of facts from Cloudflare
On a side note, I noticed that my “routeMeHome” domain had had a few firewall events pop up. So being slightly bored and curious as to why these event logs were I opened one up. And this is what I found:

![Cloudflare firewall event log](/media/posts/images/2021-08-13-cloudflare/Screenshot-from-2021-08-13-01-36-35-1.png)

The interesting parts of this log to me were mainly the Host and Path fields. The firewall allows me to view much more details on the request that had been blocked than the analytics page allows. So an idea pops into my head. The firewall has both a deny and an allow mode. So what happens if I allow everyone access? And the second. How do I allow everyone?

The first idea was could I just allow IP addresses *.*.*.*, however, Cloudflare were ahead of me here, and could tell it was not a valid IP address. Next though. What happens if I allow 255.255.255.255, the network broadcast address, or 0.0.0.0, the magic placeholder that sometimes works. But no neither of these worked either.

## The Eureka moment
I went back to the original firewall event log and checked why it was there. It turns out that earlier that day I decided that as I have no plans to leave the country any time soon, damn you Covid-19, I had blocked all requests that originate from outside the UK. From this, I decided to create a firewall rule to allow all countries other than one. And then include that one back in again as a second part to the rule. The only problem is which country do I pick? I finally settled on Antarctica a) because I’m lazy and this meant I didn’t have to scroll too far. And b) I doubt there are many people in Antarctica trying to read my blog so if this rule breaks the access then it’s probably not the end of the world.

(If you are in Antarctica and are having problems accessing my blog let me know and I’ll pick a different country).

![A Cloudflare firewall rule allowing all countries to access my blog](/media/posts/images/2021-08-13-cloudflare/Screenshot-from-2021-08-13-02-00-37-1.png)

## The testing our analytics
Now that I have a rule in place that seems to be doing what I want, let us see what sort of data it is giving me.

![A Cloudflare allow firewall event log showing us Host and Path information](/media/posts/images/2021-08-13-cloudflare/Screenshot-from-2021-08-13-02-09-23.png)

And low and behold I have access to that valuable Host and Path information for every request that hits my domain. This is now starting to look like the Cloudflare analytics for free that I promised back at the top of the page. (And yes this one was added because the Yoast algorithm told me I need to use my key phrase one more time).

## The summing up
Now, I’m not saying this is a good idea. Or that even it replaces the Cloudflare analytics section. And in the few hours that I have had this running, I have generated around 18750 firewall event logs. Most of these are from me just editing this post with the way WordPress has been calling page content. At this moment I almost have the complete opposite problem with now having far too much data to process. This is just a way I found to get some extra Cloudflare analytics for free. But I think what and how I can use this data may fall under a different post. For now, I can pull the firewall logs and comb through them trying to work out what on my blog works and what doesn’t.

## The Cloudflare firewall order
It appears that the firewall operates in a single match top-down fashion. This means that as soon as it finds a rule that matches it stops checking for any others. This means that the allow all rule needs to be at the bottom. By doing this any other rules you have to block access will match first and then as a last resort the firewall will log the request.

## The disclaimer
I am unsure whether this is a good or a bad idea. Or even if it is something we should be allowed to do. However, it does currently work but if for some reason Cloudflare does change their system to block this I will add an update to the top of this post declaring it broken.
