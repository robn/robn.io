---
layout: post
title: Adding XMPP support to nginx
excerpt: We don't need no XML parser
tags: xmpp jabber nginx ssl tls terminator auth sasl
image:
  feature: sand.jpg
---

So today I want to tell you about a little side project that I worked on over the Christmas break in between eating, watching cricket and napping. I added XMPP termination and proxy support to nginx.

This is a big post because I want to cover everything: background, rationale, implementation detail and so on. As I wrote the outline it occurred to me that this would make a really interesting talk. Maybe one day if I find an appropriate venue that could happen!

If you just want to use the thing, go and get it from GitHub: [https://github.com/robn/nginx-xmpp](https://github.com/robn/nginx-xmpp)

Lets get started.

## FastMail's XMPP server: djabberd

FastMail's XMPP server is a relic. It's called [djabberd](https://github.com/djabberd/DJabberd). It was originally developed at [LiveJournal](http://www.livejournal.com/) for their [LJ Talk](http://www.livejournal.com/chat/) service way back in 2006. Apart from being written in Perl (which we love because it can use our internal libraries), it's designed not be to be a standalone server but rather to be heavily extended to integrate into your existing environment.

It has some really nice properties besides being easy to hook up to other things. The main thing I like is that internally it's fully asynchronous. Every other server I've seen (admittedly not many) is willing to block the entire server for storage operations. djabberd is happy for you to initiate a storage operation and go and do something else, and it will tell you when it's done. It actually feels a little like coroutines inside. Granted, we don't currently have the load where this is strictly necessary, but that's no reason not to like it.

Unfortunately, djabberd hasn't seen any serious development for a few years now. We maintain a [small patch set](https://github.com/fastmail/DJabberd/commits/fastmail) to keep it alive and working sanely with other servers, but as far as I'm aware there's no one else using it. If they are they're either not doing federation or they're quietly writing their own code to keep it running as well, because I had to implement [STARTTLS for server-to-server streams](https://github.com/fastmail/DJabberd/commit/144c4431f36e5ea371aeeaa4692f6d81fae196ff) to support the mandatory encryption push that the XMPP community went through last year and as far as I'm aware not one person has taken my patch.

The plan at some point is to move to [Prosody](https://prosody.im/). It needs a fair bit of development work to hook it up to the rest of our systems and add support for a few things we need, like dynamic virtual hosting. None of it is especially difficult to do, and in reality if I put my head down it would probably only take a few weeks. There's quite a bit of more important (non-XMPP) work I need to do though, and if we're going to do serious engineering work on our XMPP server we first want to think carefully about how it fits into our product in the long term and how we can make better use of internal storage and messaging infrastructure that we didn't have back when we deployed djabberd. I've got a plan, and I'm hoping to do some work on it this year, but right now it's not a priority.

There is however one important part of djabberd that does have to be actively maintained, and that's its TLS support.

## TLS in 2014

2014 was a busy year for the TLS world. We saw some serious bugs and protocol flaws ([Heartbleed](http://en.wikipedia.org/wiki/Heartbleed), [POODLE](http://en.wikipedia.org/wiki/POODLE), [RC4 weaknesses](https://community.qualys.com/blogs/securitylabs/2013/03/19/rc4-in-tls-is-broken-now-what), etc) that forced us all into fast upgrades and removal of obsolete features from our TLS-aware servers. We saw the general availablilty of secure new ciphers and concepts, like [PFS](http://en.wikipedia.org/wiki/Forward_secrecy). Most importantly, we saw a big increase in the quality and pace of development in TLS implementations and just a lot more awareness of security issues among the broader tech world.

In 2014 I did a lot of TLS-related upgrades. I bought new certificates, I recompiled things, I disabled SSLv3, I disabled RC4, and so on. For the most part disabling things is easy to do. Most of our TLS processing is done by [nginx](http://nginx.org/en/), a popular web server/proxy that [at last count](http://news.netcraft.com/archives/2014/12/18/december-2014-web-server-survey.html) serves some 15% of the worlds websites and 35% of the top 1000 sites. It's a big deal and as a result there's a lot of investment and interest in having it support the latest features and best practices that the web security world comes up with. Just one example of this is the company [CloudFlare](https://www.cloudflare.com/), who are huge nginx users and [write a lot about TLS](https://blog.cloudflare.com/staying-on-top-of-tls-attacks/) issues as well. Enough people care about nginx's TLS support to make sure that it's highly configurable and give a reasonable guarantee that it's going to do the right thing.

Other less popular software is not so lucky. We provide an LDAP service so that our customers can do address book searches directly from their mail client. It runs on [OpenLDAP](http://www.openldap.org/) which is a very competent LDAP server. Its TLS support is not as configurable as I would have liked though. It took me quite a while to figure out how to disable SSLv3 to protect against the POODLE attack. Eventually after reading the code I found an undocumented config option that let me specify a particular flag to OpenSSL to disable it. If I hadn't found that, I would have had to write my own patch for it.

djabberd is in a similar situation. Right now there appears to be deficiencies in Perl's TLS libraries that make it impossible for us to support TLS 1.1 and 1.2 and ECDHE ciphers for PFS. I've investigated these to the best of my ability but haven't been able to get things working. As a result, right now, FastMail's XMPP server is only accessible with TLS 1.0 with a very small set of ciphers. It's secure against known attacks, but given the events of 2014 the odds seem quite good that something will soon happen that will render TLS 1.0 and/or the ciphers I have available unusable. At that point I'm going to be left with three options: continue with a known-insecure service, migrate to another server, or somehow find and fix whatever is stopping Perl using more modern TLS. None of these are particularly appealing. It's going to be a lot of pain when the time comes.

So in November I started looking for options that might allow us to keep djabberd running on the latest TLS tech indefinitely. nginx is the solution, but first lets talk about something that isn't well known - nginx's mail support.

## The nginx mail module

The vast majority of nginx deployments are as a web reverse proxy. That is to say, it doesn't serve any content itself, but takes incoming requests, figures out who should handle them, then makes a request to some backend server. Once that connection is established, it gets out of the way and just passes traffic back and forth between the client and the backend.

[Way back in 2005](http://www.emaildiscussions.com/showthread.php?s=&threadid=37897) we were shopping for a new IMAP proxy to replace [Perdition](http://horms.net/projects/perdition/), which was struggling to cope with the load. We realised that nginx's model of setting up the connection then getting out of the way was exactly what we wanted. Igor Sysoev, the author of nginx, [agreed](http://blog.fastmail.com/2007/01/04/webimappop-frontend-proxies-changed-to-nginx/) and a couple of months later nginx entered service as our IMAP and POP3 proxy. Over the years it gained features, not least of which is an authenticated SMTP proxy. It's been almost ten years and nginx is still going strong at FastMail, serving hundreds of thousands of connections every day without breaking a sweat.

Its operation is fairly simple. It speaks just enough of the IMAP, POP3 and SMTP protocols to get through STARTTLS and SASL AUTH. Once it's received the username and password, it calls out to an [authentication server](http://nginx.org/en/docs/mail/ngx_mail_auth_http_module.html). This performs whatever username/password checking is necessary and then returns an IP address and port of the "real" IMAP/POP3/SMTP server for the user, and a username and password to login as.

(The authentication server enables some very interesting functions, like transparent failover. You can read more about that in [this post](http://blog.fastmail.com/2014/12/06/user-authentication/) I wrote for the FastMail blog last month).

Armed with that knowledge, nginx then connects and authenticates to that backend server. As soon as it issues the authentication command, it switches to a bi-directional proxy mode. nginx no longer looks at what's being received; it just forwards it on to the other end. The very first thing to travel over the proxy is the backend server's successful authentication response. The client receives this and then starts normal protocol operations, whatever they may be.

From searching around, it looks like not many sites use the mail proxy. I suppose this makes sense since doing email at the sort of scale that makes you need a proxy is actually fairly uncommon, unlike websites. Whatever the reason, we're happy that it exists and works as well as it does.

## The plan

So, back to XMPP proxying. We've got nginx with its excellent support for "modern" TLS. It knows how to set up and proxy connections for a few different protocols. Given these facts, I started to wonder if it could be made to proxy XMPP in the same way.

I initially dismissed this idea as ridiculous. I couldn't possibly embed an XML parser in nginx and still maintain the features that make it what it is: tiny, micro-optimised and secure. Event if I did, XMPP is asynchronous, so there's no way that it could comfortably fit into either the HTTP or the mail proxy models. The idea stayed in my head though and after a couple of days it started to become clear that it could be done.

The first thing I realised is that during stream establishment (that is, while the client and server are negotating encryption, authentication and other things) the protocol is effectively synchronous. The server does not do anything without instruction from the client. This effectively means we can consider each XML chunk sent by the client to be a "command" inside nginx, not unlike IMAP commands.

Then came the big "aha!" moment: *we don't need a full XML parser*. There's actually not a lot an XMPP client can do during stream establishment, and clients won't typically use any unusual XML features this early because there's some very strong conventions in the XMPP world about things like the choice of namespace prefixes, use of entities, etc that go right back to the first Jabber servers which didn't understand any "advanced" XML. Clients generally don't deviate from these conventions. Further, we can largely assume that clients will be compliant - if they weren't, they wouldn't work in lots of places.

With this, we know that we will only receive a very limited set of "commands" from the client and we know their format. To make this work we only need to be able to parse just enough XML to understand this limited set.

I ran the whole lot by my colleague Rob Mueller, who understands pretty much everything about nginx, and he though it didn't sound totally ridiculous so I took that as a kind of green light.

## The parser

nginx's protocol parsers are simple character-at-a-time state machines. The IMAP proxy, for example, doesn't really understand IMAP. It just looks for a string that matches one of the commands it knows, and then moves the state along. When the state reaches particular points, like the end of the AUTH command and arguments, actions are taken. A "real" server would more likely read a line, split to commands and arguments, look up command handler, and so on. nginx instead has the logic embedded in the parser itself. You can do this when you only have a very small subset of a command set.

For XMPP, we need to do the same thing. (well, we don't strictly need to, but one goal here is to get this code accepted upstream, so it makes sense to keep it looking like the surrounding code as much as possible). So first, we need to think about the minimum amount of XML we need to support. Because we're tracking the stream state as we go, and we're assuming that the client is always going to do the right thing, we can simplify a long way:

* After connection, only a `<stream:stream>` element is valid. And we can actually ignore the stream prefix and just say anything that looks like `<stream>` is valid.
* After `<stream>`, only `<starttls>` and `<auth>` are valid.
* After `<auth>`, assuming we don't have enough credentials to actually do the authentication (SASL mechanism dependent), only `<response>` is valid.
* Also after `<auth>`, we need to collect CDATA, which is just everything up to the next `<`.
* At any time after `<stream>`, a closing `</stream:stream>` can close the connection. If we ignore the leading `/` and the prefix, then we're just looking for `<stream>` again, simplifying our list of possible commands.
* Any other element gets ignored.

We luckily get an easy ride on attributes. There are only two elements that have an attribute we care about: `<stream to='user-domain'>` and `<auth mechanism='...'>`. nginx allows us to store an arbitrary number of positional parameters with each state. Positional parameters would be a problem for a character-at-a-time XML parser because attributes can appear in any order but because there's only one we care about, we just look for it and store its value at as the first parameter.

Now obviously it's entirely possible to send perfectly valid XML (and XMPP) that this parser would have no idea what to do with. In practice though I'm pretty sure clients won't. I think there's an outside chance that XML entities could appear, but they're trivially dealt with. The other thing that this probably breaks is any form of non-ASCII UTF-8 codepoints, though I think this should only appear in the `to` attribute, if anywhere. If it comes to it I'll just start converting the UTF-8 character stream to [UTF-32](http://en.wikipedia.org/wiki/UTF-32) internally and then process an array of `uint32_t` instead of `char`.

## Other implementation notes

There's a handful of other interesting implementation details:

* Response XML is generated from templated strings embedded in the source code. There's no logic involved.
* Unlike IMAP, POP3 and SMTP, in XMPP the client sends first. A little bit of nginx code had to be reshuffled to support this.
* The `Auth-User` parameter passed to the authentication server needs to be a full `user@domain` pair. To do this, we have to combine the username received during the auth handshake with the domain received in the stream `to` attribute.
* Similarly, the authentication server can return a different `Auth-User` parameter to be used to login to the backend, so we have to split this for use in the right places in the protocol.

## Results

So far, it looks promising. I've tested with a small number of clients and libraries and so far all have been able to establish a stream and a full IM session without issue. My testing hasn't been particularly methodical though. As soon as I get time I'll be filling one of our test machines with every client I can find and seeing what happens. I'm not expecting any major issues.

More importantly, it performs very well on TLS tests. The [IM observatory](https://xmpp.net/result.php?domain=betachat.messagingengine.com&type=client) awards it an A, with no serious warnings. This isn't surprising since we've already heavily tested our regular nginx configuration, but it's good to have it confirmed by tools the XMPP community are more likely to use.

## To do

There's two main items I want to look into. The more important one is support for server federation (server-to-server). It should actually be pretty easy since it's really just a matter of treating `<db:result>` and `<db:verify>` elements as proxy triggers. Once we see one of them, we need to buffer the rest of the element and once the closing `>` is reached, we initiate the proxy and send the buffer to the backend. Alternatively, we can switch to the proxy immediately after `<starttls>`.

The other part that will be important for us is to be able to support multiple certificates. This is basically [SNI](http://en.wikipedia.org/wiki/Server_Name_Indication) but using the domain received in the stream `to` attribute as the key. This is significantly less urgent, and also kind of tricky because even though nginx does support SNI, it uses the certificate name to select a particular configuration block, which isn't something we actually want here (or for IMAP etc). Also I think I'd actually like to be able to call out to a service daemon to fetch the certificate and key for the requested domain, so that I can allow the user to upload their certificate and key to their file storage area. This isn't something I intend to tackle for a while, and it's not quite XMPP-related either.

I'm curious about other stream features that we could support. [XEP-0198 session resumption](http://xmpp.org/extensions/xep-0198.html#resumption) might be interesting, especially in concert with TLS session resumption, though I've not thought about it in depth. Other features like compression can be negotiated once the proxy is established so there shouldn't be many where nginx really has to get involved.

## And not to do

A curious thing about the XMPP community is that they've always been extremely interested in security technologies. If you want to find out more about the latest security protocols you can pretty much guarantee that there's an XMPP server or client that has an implementation in place. So when I mentioned this project to a few XMPP people, I got some raised eyebrows over the fact that I'd tie XMPP into a system that can only do plaintext authentication. Not negative responses, I must stress - these are smart people that understand better than most the difficulty of gaining widespread adoption - but it was at least mentioned as a minor issue.

So I'll say it right here. The XMPP support I'm adding to nginx is not intended to be at the leading edge of anything. Its almost the opposite, by making the status quo easier to maintain. TLS+PLAIN is good enough for every other service FastMail offers, and so its good enough for our XMPP support as well (though of course it supports all the [FastMail auth magic](https://www.fastmail.com/help/account/2fa.html), but that's because our authentication server handles all that). Indeed, nginx doesn't even have support for anything that isn't plaintext authentication so I couldn't (easily) support [SCRAM](https://tools.ietf.org/html/rfc5802) even if I wanted to.

This whole thing is about applying whatever nginx does to XMPP, not the other way around. If, in the future, nginx gains support for more advanced authentication mechanisms, then the XMPP support will make sure to use it. If nginx doesn't, then neither will XMPP. If XMPP moves in directions that make that untenable, then I'll throw the whole lot out and try something else. I'm ok with that.

So the assumption here is that you'll only use this if you already have an investment in nginx. If you want a standalone XMPP server, then [grab one off the shelf](http://xmpp.org/xmpp-software/servers/).

## Getting it

The code is on GitHub: [https://github.com/robn/nginx-xmpp](https://github.com/robn/nginx-xmpp)

For now at least, I'll be managing this as a patch series on top of nginx releases. Each time there's a new release, I'll create a new version branch and rebase on top. Obviously, if this gets crazy popular then I'll try and rope others into helping out with this too. Patches gratefully accepted!
