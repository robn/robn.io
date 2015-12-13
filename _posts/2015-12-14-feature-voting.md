---
layout: post
title: Voting for features is bad for all involved
excerpt: Feature voting systems just push decisions you should be making onto your users. Don't do it.
tags: feature voting service support user
image:
  feature: rain.jpg
---

This is a short post based on an email I wrote in reply to someone who asked me about my "reservations" about feature voting systems. Unfortunately for them, I'd had it on my mind to write something about this for a while so I took the opportunity to to get my thoughts into order.

For the uninitiated, when I talk about a "feature voting system" I'm referring to systems that many service providers have on their support pages these days where users can submit a feature request, and other users can vote on them. There usually some ability to add comments to the feature, and most service providers claim that they use these lists as a way to let users choose what thing gets worked on next.

I'm coming at this largely from the perspective of a service provider accepting feature requests from users, though I have been on the customer side too and found it unsatisfying. I'm willing to accept however that there are times when a feature voting system can work that I just haven't seen yet. Do point me to examples if you know of some.

## Users don't know what they want

The main objection I have to asking users to directly describe what they want is that they don't actually know what they want, or at the very least, are unable to articulate it. This isn't new knowledge; Neilsen & Levy wrote about it in 1994, which they summarise in part as "[Don't Listen To Users](http://www.nngroup.com/articles/first-rule-of-usability-dont-listen-to-users/)". A more recent take is the quote usually misattributed to Henry Ford where he says that if he had "asked [his] customers what they wanted, they'd have asked for faster horses". I've seen it everywhere throughout my career - someone asks for something, but once you start talking to them about what they're actually trying to achieve; what their desired outcome is, they often want something quite different, which can often be realised via a mechanism that already exists!

## Users don't know how to prioritise

The idea of prioritisation is also broken. Users have no idea of how hard something is to actually produce, and what the makeup of skills across our team is. We see this quite often; any time we announce a new feature, it invariably gets a few responses along the lines of "why work on X, when Y is so much more important?". Most of the time, X and Y are entirely unrelated, require different staffing and technical resources and have different timescales. Users don't know enough to compare two feature requests in isolation.

## Users don't know your vision for your product

The other objection I have is related to product vision. We deeply understand what our product is and what our goals for it are. Our plans for the next year are almost set in stone, and we have a good idea of what's coming in the next 2-3 years. We know the direction we want to move and grow the product and the company in. Users that have been customers for many years might also have some understanding of this, but the vast majority do not, so there's know way for them to know if their request even fits in with where we want to take the product.

## Voting for features can only produce bad data

So with all that in mind, we can look at what a feature voting system actually gives us. It's simply a list of unrelated tasks, described and prioritised by people unable to ask for what they want, prioritise items against each other and understand how its fits into our product vision. Its not a particularly useful.

In fact, I'd argue that its worse than that. If we were to implement such a service, and the top-voted feature was something fundamentally impossible, then what? All we can do at that point is say no, with the result that people feel like their voice hasn't be heard. That would be entirely reasonable criticism: if you give a person a voice, you _must_ be prepared to listen to it. If you tell your users that the top-voted item will be worked on next and you don't deliver, you break trust.

## How to listen to users

Of course, none of this means that we don't listen to users - we absolutely do! We do it by listening to the stories they tell us, mostly through support tickets, but also by reading comment threads and personal emails. Our customers regularly tell us about things they like and don't like about our service, or point us to other services that are doing useful things for them. If we start to hear the same problem or request over and over again, then we start talking about it, experimenting, and working out if its something we want to do and how it fits into our broader plans.

## The user experience

On the other side of it, being a user of a service that uses a feature voting systems can be extraordinarily unsatisfying. One personal experience I've had is with a third-party service that I pay for. I contacted their support asking if a particular feature I needed could be added to their API. The response I got was "great idea! Please go and vote for it!". That's about the least useful answer possible. If I'd been told "no, never", then I can find another way to do what I want. If I get told "yes, but not until next year" or "maybe, we'll discuss it" or pretty much anything else, at least I know where I stand and can make a decision based on that. Instead, I'm left with a company that I pay for a service passing the buck, delegating a decision to some undefined mass of other customers that I have no relationship with or interest in. Its far worse than a simple "no".

## Can it ever work?

I really don't see how feature voting systems as traditionally deployed can ever work.

I think they could be useful internally in some situations. Basically, if the users of the system can describe what they want, know how to prioritise and understand the product vision, then it seems that it could all work very nicely, and those are things that people actually working on the product should be able to do. I have no idea if it would be better or worse than other methods of selecting tasks to work on, and I don't know if I'm keen to try.

There are certainly aspects of a feature voting system that are nice for users. Users like to be able to talk to other users about their experiences, and there's always solace in knowing that you're not the only one having a particular problem. I suspect that many people like feature lists as a substitute for a roadmap. Those aspects seem to me to be something you can do with other tools, say, a discussion forum and a release calendar.

Mostly though, I don't really see that it can. You own your service, and your users are expecting (and maybe even paying) you to use your experience and skill to make the best decisions on their behalf. It isn't fair to them to expect them to make your decisions for you.
