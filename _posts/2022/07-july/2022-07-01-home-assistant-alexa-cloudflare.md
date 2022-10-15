---
layout: post
title: Home Assistant Alexa smart home integration over Cloudflare
date: 2022-07-01
categories: [Home Assistant, Cloudflare] # Can be anything
tags: [hass,homeassistant,cloudflare,firewall] # Must be lowercase
img_path: /media/posts/images/2022-07-01-cloudflare-hass
---

Today I decided it was the day that I was going to manage to get Alexa to talk to my Home Assistant (HA). There are a couple of things that made this slightly difficult for me. The first was that I had limited access to my domain only to the UK. The second was a setting I didn't even know was on but after finding it I didn't want to turn it off. Something that made it a little hard to track down was that the error message was being masked by an error that is commonly encountered during the setup process.

![Cloudflare error returned to AWS Lambda](cfhasserror.jpg){: width="350" height="350" }

## TL;DR
If you are running your Home Assitant instance through Cloudflare and your Alexa cannot connect or successfully get an authentication token then check your Bot Fight setting and Web Application Firewall rules.

## The Back Story
When I first started self-hosting I was getting some random hits on my domains. For some reason, someone in Singapore seemed to think I was running a WordPress site on my domain and kept trying to hit the admin page. So just to be safe I decided to set up a WAF (although it was just called firewall rules back then) rule to only allow traffic in from the United Kingdom. This seemed like a sensible decision as I live in the UK and could always add a new rule if I was going to spend any amount of time in a foreign country.

## Solving the first problem - Wrong country
From the photo above you can see that I was getting an INVALID_AUTHORIZATION_CREDENTIAL error. What I didn't realize for a very long time is that the body of my error was different from the one that was in the video tutorial I was following. After realizing that I had a different error I straight away started to google it. What took me the longest was tracking down what was causing the 1020 error. Eventually, I realized that it was coming from Cloudflare and not from my Home Assistant server. Once I knew which logs I should be looking at it was much easier to track down the exact problem to fix it. It turns out that it was because of the decision I had taken a year ago that I explained above. To test my theory I added the county that I was using to run my AWS Lambda function into another rule and, hey presto, I was able to get a response back from my HA server.

![AWS Lambda function returning a successful result from Home Assistant](lambda_success.png)

## Solving the second problem - Bots
The next problem was not quite so easy to track down. Once I had the Lambda function working I moved on to trying to get the authentication going. Knowing that it was probably my WAF that was going to be causing the problem gave me a head start on tracking the problem down. Watching the logs as I tried to sign in showed that several requests were being issued a JavaScript bot challenge. And as it was a bot trying to sign in it was of course failing to do so. A slightly secondary issue was that the bot seemed to be coming from the USA and not Ireland or the United Kingdom as I had been expecting.
To start with I thought that adding another WAF rule to allow both the USA and Known Bots might be the solution. This didn't work as expected.
I'm not sure why but Cloudflare's bot fight security setting seems to be the ultimate rule. There is no way to override it. The second problem is there are a lot of people and/or servers in the US that I didn't particularly want to access my home automation. This meant I wanted to narrow down the scope of a WAF rule that might let traffic in. I finally settled on a rule that ensured that the country was the USA, the hostname being requested matched that of my public-facing HA subdomain and finally the URI path matched that it needed to get the correct authorization token. The final rule looked something like this:
```
(ip.geoip.country eq "US"
and
http.host eq "<subdomain>.<domain>.<tld>"
and
http.request.uri.path eq "/auth/token")
```
>N.B. This should be one continuous line but I have broken it up in an attempt to make it easier to read.
{: .prompt-warning }

This, after all of my previous WAF fun, surprisingly worked.

## Combining the WAF rules
Unfortunately, I was now using 4 out of my 5 free WAF rules on my Cloudflare account so the next problem became combining the rules all into one. There seems to be a slight bug in the Cloudflare WAF rules that the following very simple block rule:
```
(ip.geoip.country ne "GB") 

or 

(ip.geoip.country ne "IE")
```
>N.B. This should be one continuous line but I have broken it up in an attempt to make it easier to read.
{: .prompt-warning }

Just blocks everything as GB is not IE and IE is not GB. Not the most useful. Luckily if you start building more complex rules this flaw doesn't seem to hold up. That means we can use something like the following:


```
(ip.geoip.country eq "IE"
and
http.host eq "<subdomain>.<domain>.<tld>"
and
http.request.uri.path eq "/api/alexa/smart_home")

or

(ip.geoip.country eq "US"
and
http.host eq "<subdomain>.<domain>.<tld>"
and
http.request.uri.path eq "/auth/token")

or

(ip.geoip.country eq "GB")
```
> N.B. This should be one continuous line but I have broken it up in an attempt to make it easier to read.
{: .prompt-warning }

## Summing up
So now I have one firewall rule that is controlling the access to my domain and whilst I would have liked to keep the Bot Fight setting turned on I think I have reduced the chance of getting unwanted hits. I hope that this may help someone else in the future as it took me a fair chunk of the day to finally work out why the tutorial was failing.
