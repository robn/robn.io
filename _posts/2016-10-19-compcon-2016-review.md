---
layout: post
title: CompCon 2016 in review
excerpt: In which a most excellent time was had by all.
tags: compcon compconau compcon2016 student conference review
image:
  feature: brisbane.jpg
---

From 1-3 October 2016 I was at CompCon Australia 2016, a technical conference aimed at tertiary computing students. I had a great time, in ways I didn't expect. I'm currently on the flight home and thought I should write down a few thoughts while its still fresh in my mind.

## Setup

Its not a conference that I'd ever heard of before, and even if I had I probably wouldn't have figured it was the right place for me to attend. But my friend [Ducky](https://twitter.com/Ducky_Tape) brought it to my attention and very enthusiastically suggested that I should submit a talk because students have so little real-world experience, and I've been around a while and should be able to offer a useful perspective on something interesting.

I've been thinking about doing more conference speaking for a while now, and after a little checking I decided this could be a good place to get started on that, so I submitted a talk. I've been doing a lot of stuff around authentication at FastMail, and I decided that [a talk on passwords](/passwords-compcon-2016/) might be interesting. There's a massive amount that can be said about the generation, storage and use of passwords, and much of the detail is hard to appreciate until you've actually done it for a while. A talk about some of the things you need to think about seemed like it could be made interesting and fun.

Not long after submitting the talk, I recieved an email from the conference organisers asking if I would like to present it as a keynote. This felt like a massive step to me, considering that at this point I had only done one talk in my life longer than five minutes, and that was way back in 2010. But I knew I had to, and was confirmed when I asked my wife what she thought and she said "you have to; you can't ignore an opportunity like this". In reality I only asked her because I knew she'd say that and I needed to hear it. Thus resolved, I replied back and told them I'd be delighted to do a keynote.

## Preparation

And thus began the writing process. I started writing the talk in early September, because I knew that I would need plenty of time to research, write, practice, rewrite, etc and I didn't want to leave it until the last minute. That's not fair to the audience, who would be makng the effort to come and see me speak and wil expect (or hope for) some solid information and entertainment in return. I've seen lots of talk which have nothing to say and/or put my to sleep. I absolutely did not want this to be another one of those.

My wife is actually a sought-after speaker in her circles and has been doing this sort of thing right back to high school where she was winning awards for debating and public speaking. This is both awesome because I've got a wealth of experience and advice to draw on and terrifying because I've been in awe of her since long before we were married and suddenly I'm playing in her world. She was very gracious with her time and advice though, as well as creating space in our very busy schedule (juggling three kids and two jobs) to make sure I had time to focus. I remain in awe of her, and I'm ok with that.

I set myself a deadline of one week to write the whole talk and do the first presentation (to my wife, in our home). I knew full well that I'd be tweaking things right up until the talk itself, but I figured it would be better to spend my time iterating on it, creating multiple versions of the talk each one improving on the last, rather than spending the whole time working on a single talk which might not be right in the end. It works for software I write, and turned out to also work for writing a talk.

The final talk looked very different to the first version that I gave to my wife in early September. Only the "history" section remains from that early version. The rest has been discarded and rewritten entirely four times by my count, and it wasn't until the day before my flight to Brisbane that I finally found a clear narrative connecting the all the sections of the talk.

I did make a few minor style tweaks in the two days in between, and re-read it over and over to myself, but I was really happy with it at that point. So much so, in fact, that I didn't feel at all nervous in the hour before I had to deliver it, which surprised me greatly, since I've always suffered from a mild anxiety.

I haven't actually seen the final product; I'm told there's a video on the way soon. I'm really curious to see how I look. It felt good delivering the talk, and I got some positive (and no negative) feedback from the audience. I specifically asked a couple of people that I trust afterwards what they thought, both in content and in delivery and everyone said it was great. So I'm feeling good about it and I'm quietly excited about the next talk I will give.

## Loot

A few months ago when we were implementing [U2F for FastMail](https://www.fastmail.com/help/account/2fa.html), I was disappointed at the general lack of interest in it. I've made no secret of my enthusiasm for U2F; its the first two-factor technology that I've thought stood a chance of being accepted by non-technical users while actually being secure.

U2F is starting to see a lot more adoption since then, and I'm sensing that a critical mass is probably not far away. I'd already decided that I wanted to do more to promote it, which started with me doing a [last-minute lightning talk at YAPC:NA 2016](/u2f-yapcna-2016/). Something I heard after that was that while people found it interesting, most wouldn't do much about it because they'd have to buy a U2F device just to play around with it.

So I decided try to lower the bar by giving the damn things away. I found the cheapest devices I could, the [Nitrokey U2F](https://shop.nitrokey.com/shop/product/nitrokey-u2f-5), and ordered a pile of them. Granted, they're not particularly nice devices in construction or user experience (insert-to-activate, ugh) but that's not the point - the point is to give people no excuse to give the idea a try.

Because I hoped to avoid the situation where people would just take one and chuck it in a drawer and never see it again, I placed a single condition on it - recipients had to commit to saying something in public about what they did with it. I tried to make that clear that it didn't need to mention me or FastMail, and didn't need to be much - even just a tweet would be fine. The main point of this would be to make U2F more visible, but the other reason was just to attach a tiny amount of mental obligation to recieving a device.

I don't know if it will work. I'm not hugely concerned, to be honest. I would like to hear a couple of good stories though. I was happy that a couple of people working on security-related projects or research did ask if they could take a few for further experimentation.

## Attendees

CompCon is aimed at tertiary computing students. Its been over 15 years since I was a tertiary computing student, so I assumed that there'd be some sort of gap between me and the conference attendees. I didn't know what exactly, but I was expecting to have to make a particular effort to relate in some way.

Happily, that never happened. I think the worst that happened was when I didn't know a couple of newer songs in the impromptu karaoke session on Sunday night. Apart from that, everything was just normal - just people with all kinds of different experience and plenty of stuff for me to learn.

A great example of this was Jaimyn Mayer and Kendrick Tan's [autonomous car](http://jabelone.com.au/blog/make-autonomous-car-code-included/) presentation. One of them is good at hardware, the other good at software, so they got together to build their own self-driving car and gave a great presentation abut the whole thing, incuding how the neural network that controls it works and how they trained it. I've spent some time trying to understand this stuff myself and mostly haven't, and I don't really after this talk either. I was however rather awestruck that first-year students make these things for fun.

But the definite best-in-show for mine was [Ash Guy](https://twitter.com/theashguy)'s [Beautiful & Useful](http://www.slideshare.net/AshGuy/beautiful-useful-66694019/1), where he presents "some rules of thumb for building software that looks and feels great without designers". As engineers, too often we'll be building things for real people without any understanding of how those people actually use them. Particularly in open-source, many of us will be working without the benefit of a proper designer nearby so having a few simple rules to help us is unbelievably useful. I found myself wanting to applaud through most of this talk (but managed to restrict myself to nodding and tweeting) just because this is stuff I've mostly managed to learn the hard way and I wish I'd heard it 20 years ago before I made a mess of this stuff.

## Next time

I'm now of the opinion that CompCon or a conference like it is hugely important. Australia has very few technical conferences open to general topics ([linux.conf.au](https://linux.conf.au/) is the obvious example of this type), and perhaps nothing generally-accessible to students apart from CompCon.

Cost appears to be the main issue. The thing that was impressed on me at every turn was that students generally have very few resources at their disposal. Despite the conference itself only costing $25, it was still attended predominantly by Brisbane-based students. The reason for that is obvious - flights and accomodation are out of reach for most. While I didn't take the absolutely cheapest options available, I still spent $600 on those. Even if you could do it on half that; its a decent chunk of change if you don't have someone supporting you.

There was a bunch of talk content that was mostly only relevant to students. There was a good presence from recruiters and local companies offering internships, and a panel talking about career options. These sort of topics aren't typically going to be discussed at a conference with a broader attendance.

There's also (somewhat obviously) a lot more opportunites at a student-specific conference for students to actually get on stage and talk about what they're doing. This is massive for experience and confidence; an audience of your peers is a much less intimidating place to start your speaking career than a major professional conference like LCA.

I don't mean any disrespect to the organisers and attendees of other conferences; on the contrary, most are doing a great job of understanding and catering to their audiences and many are trying to find ways to give newcomers opportunities to contribute and connect with industry. But, having seen it in action, I'm firmly of the opinion that CompCon is trying to fill a gap that nothing else is really looking at or focusing on.

Its never going to be what it could be if it can't reach more students. I have a few ideas for how that could happen, and I've been talking on-and-off with some of the organisers about it. I don't know if anything will come of it, and if nothing changes and CompCon remains a small Brisbane-based conference that won't be a bad thing, but I'm hopeful that in the future we as an industry can find more ways to support students.

## Corporate overlord

I must mention my own employer/patron, [FastMail](https://www.fastmail.com/). They've always been entirely supportive of me going to conferences, meetups and other events and never ask any questions when I want to do crazy things like buy a bunch of gadgets to give away. And then there's all the time and distraction and practice runs that goes into putting the talk together, which everyone was quite happy to accomdate and get involved with.

We also do stuff with email. You should try it.
