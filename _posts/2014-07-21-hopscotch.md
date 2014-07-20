---
layout: post
title: Introducing hopscotch, a tiny image proxy
excerpt: The crazy things we do to keep the web safe
tags: hopscotch https ssl perl image img http https
image:
  feature: autumnleaf.jpg
---

I spent my weekend on a new project I'm calling [hopscotch](https://github.com/robn/hopscotch). Its a tiny, high-performance HTTP image proxy.

When you include unsecured (HTTP) content (usually images) inside a secured (HTTPS) page, you effectively make the whole page "unsecure". The browser will usually tell you when this happens in some way. Older versions of IE at least used to present an obnoxious dialog box. Chrome will change the state of the padlock icon and remove the green EV badge on an EV site. Its a pretty horrible experience if you've tried to train people to look for the padlock and other visual cues before performing certain actions (entering passwords or credit card details).

There's really only two options available for resolving this. If you're a regular website you just shouldn't include images from other sources. Serve images from your own secured server. But if you're directly including user-provided content (eg some kind of web forum, or in our case user email), you have to re-serve every image from your own servers. To do that, you need some software to take care of this for you.

And that's what hopscotch is. You request a URL from it, it goes and fetches that URL from the internet proper and sends the content back.

Naturally, things are never quite this simple. You don't want to allow just anyone request whatever URL they like, otherwise you'll find people bouncing all manner of random garbage through you. You need to filter some headers; you don't want the remote site to be able to be able to set cookies or things like that. You probably only want to allow certain content types; there's no reason to proxy files that aren't typically embedded. You don't want to allow a file of arbitrary size because you really don't want an attacker doing something to consume all your bandwidth. And you want some [SSRF protection](http://robn.io/ssrf).

hopscotch takes care of all this for you. It uses a digest generated from a shared key in the URL, so only your application can generate URLs that will work. It has a small whitelist of headers that it will let through, mostly own those necessary for content identification and cache control. It has an explicit list of content types that it will let through. It has a configurable size limit and will close down the connection before fetching anything if the reported content size is too large. And it has a "paranoid" mode that won't allow connections to internal networks.

I've got to give a massive tip of my hat to the good folks at Github for their [Camo](https://github.com/atmos/camo) proxy. This is the exact same concept that they use for images embedded in their comments and issue tracking applications. I didn't use it mostly because its written in Node, which I don't have on my servers and didn't want to take as a dependency for this project. Its a fantastic piece of software though and might be more suitable for your needs. For fun, the URL formats are the same, so you can try both without any changes to your client code.

The code for hopscotch is [over at Github](https://github.com/robn/hopscotch). The easiest way to get it running is to use [Docker](https://www.docker.com/). See the [README](https://github.com/robn/hopscotch/blob/master/README.md) for more details. And do send feedback, I'm curious to know how if anyone uses it and if it works for you!
