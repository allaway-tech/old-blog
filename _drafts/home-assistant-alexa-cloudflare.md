---
layout: post
title: Home Assistant Alexa smart home integration over Cloudflare
date: 2022-06-29
categories: [Home Assistant, Cloudflare] # Can be anything
tags: [hass,homeassistant,cloudflare,firewall] # Must be lowercase
img_path: /media/posts/images/cloudflare-hass
---

Today I decided it was the day that I was going to manage to get Alexa to talk to my Home Assistant. There are a couple of things that made this slightly difficult for me. The first was that I had limited access to my domain only to the UK. The second was a setting I didn't even know was on but after finding it I didn't want to turn it off. Something that made it a little hard to track down was that the error message was being masked by an error that is commonly encountered during the setup process.

![Cloudflare error returned to AWS Lambda](cfhasserror.jpg){: width="350" height="350" }

## The Back Story
When I first started self-hosting I was getting some random hits on my domains. For some reason, someone in Singapore seemed to think I was running a WordPress site on my domain and kept trying to hit the admin page. So just to be safe I decided to set up a WAF (although it was just called firewall rules back then) rule to only allow traffic in from the United Kingdom. This seemed like a sensible decision as I live in the UK and could always add a new rule if I was going to spend any amount of time in a foreign country.

## Solving the first problem - Wrong country
From the photo above you can see that I was getting an INVALID_AUTHORIZATION_CREDENTIAL error. What I didn't realize for a very long time is that the body of my error was different from the one that was in the video tutorial I was following. After realizing that I had a different error I straight away started to google it.