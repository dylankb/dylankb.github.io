---
layout: post
title: My tip for more successful pair programming
---

Alternative title: _Pair Programming - How to Have "the Conversation"_

You didn't ask for my advice, but I'm going to give you some anyway: Talk to your pair
about how each of you prefers to pair program before starting.

If I had to make some rules about pair programming they might be...

- The first rule of pair programming. Talk about pair programming.
- The second rule of pair programming. Talk about pair programming.

Ok, I admit that I kind of did that terrible "first rule of Fight Club" joke - BUT, I
made the call that the situation merited drastic action as this tidbit has really helped
me make my pair programming sessions more successful.

**Why should I talk about pair programming early in the pairing?**

I can only relate my experiences, but for me even if I have a good relationship with the
the person I'm pairing with, I have a much more difficult time talking about something
that's not working about the pairing after we've already started.

I could talk about all the ways pairing can go poorly by starting off on the wrong foot,
but instead, I'll try and on touch the ways that this tip can help your pairing go right
later on.

**Isn't this kind of conversation a bit awkward in itself?**

If you're talented (like me), then potentially _any_ conversation can be awkward. With
that being said, it's surprisingly not that bad.

If you feel like you're being a process nerd by going over all the details before
there's a concrete problem, that's ok - you already are one. Code is just obsessing over
processes that run on computers.

<iframe src="https://giphy.com/embed/IXTkvYCHkgSKA" width="480" height="310" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/obsessed-IXTkvYCHkgSKA">via GIPHY</a></p>

In all seriousness though, as developers, we do spend lots of time planning how to
execute tasks and design our programs beforehand, so I try and think of it as a similar
process. Something akin to to whiteboarding a problem or laying out requirements before
starting the actual work of programming. I feel like you've probably noticed how this
type of pre-planning helps your code. Another way I frame it in mind is I'm trying to
communicate the contents of my "code with me user manual".

Maybe I've convinced you to talk about your pair programming preferences with your
soon-to-be-pair, but...

**What's there to talk about?**

Glad you asked.

I of course immediately pop "the question" - what's your preferred pairing style? There
are other good topics to ask about, though, and here's a few.

1) Pairing style  
2) Driver navigator schedule  
3) Break schedule  
4) Responding to pair errors  

There are probably more areas that could be good to touch base on, but these are some of the more helpful ones that come to mind. To get you started, I'll go over some of the the how and why these topics were helpful for me to bring up in conversation. But first ...

**Why am I doing this again?**

If you say, "I like to do x, y, and z when I pair. What do you think about that?" You
can then:

a) figure out where your styles may differ (that's ok)  
b) have a shared understanding, or consensus, between you and your pair on how you both
want things to go.

For me, it's much easier to talk about problems in relation to the "consensus" we've
come to, rather than bringing up issues ad hoc. However untrue it might be, I sometimes
feel like I'm critiquing the other person or the relationship if something in the
pairing isn't going well. "Oh, I think 20 minutes is up. Mind if I take a turn?" feels
more rules-based and less hostile than, "Hm, mind if I have a turn now". The later can
feel like I'm judging my pair for being a keyboard hog.

**1) Decide on what type of pair programming you want to do**

There are purists who have strong ideas about what pair programming is and is not. I
like this [blog post](http://articles.coreyhaines.com/posts/thoughts-on-pair-programming) that covers two styles of pair programming. Whatever style you chose
or other preferences you talk about, I always try to keep the following in mind.

> Pair programming is not two people sitting at a computer.
It is two people collaboration on a problem, who happen to use a computer.

[https://twitter.com/NativeWired/status/914479928837591040](https://twitter.com/NativeWired/status/914479928837591040)

**2) Driver navigator schedule**

In other words, if you're following a driver-navigator schedule (see blog post above)
what will be your scheduling for switching roles. Deciding on this early makes it so I
don't have to think of tactful ways of asking to take a turn. Conversely, I also don't
have to worry about possibly making my pair thinking I'm a keyboard hog. Since we talked
about what the schedule should be, we're now stress-free!

**3) Make a break schedule**

Besides figuring out when to switch driving/navigating, syncing up breaks has been
surprisingly helpful for longer pairing sessions. When I don't sync up with my pair
about this before hand, I've found that we'll often run into a situation where either:

a) the navigator uses that time as a break, or b) one person taking a break and the
other person not, which leaves a context gap to fill later when the two come back
together.

Neither of these outcomes are going to destroy your software project, but I've found
them to be less than ideal for long term, happy, and productive pairing.

**4) Responding to pair errors**

Here I'm talking about the approach you each take when you notice that the person typing
on the keyboard has made some sort of error has made some sort of error. Each of you can
discuss what you prefer, but in my experience slowing down how quickly you correct the
driver about simple mistakes, like a style or syntax error, can have a big impact on the
level of mutual trust and comfort during the session. If I'm using the keyboard, I
always appreciate it when the other person waits a little while, maybe until I’m stuck,
so I have a chance to fix it myself. I'm a more relaxed and fluid programmer when I'm
not overly worried about making small mistakes.

I think it's also worth noting that I can almost side-step the issue by relying on some
nice automated ways of informing the driver of small mistakes. Compilers are great at
noticing syntax errors in the program. Likewise, linting tools like [Rubocop](https://github.com/rubocop-hq/rubocop) or [ESLint](https://eslint.org/) are
really good at noticing deviations from your team's style guide.  I've found leaving
these types of corrections to computers helps me focus on stuff that myself as humans
can uniquely do, like help out on a refactoring decision, which helps keep the
conversation and interactions more positive and interesting :). This is definitely just
my opinion though - your pair may have their own preference!

I hope there was a helpful idea for how to start a pairing conversation in there for
you. I do have one final note about being practical. Otherwise put, on ...

**Not overdoing it**

Just to be clear, if someone's just popping over to your desk to show you how to set up
your editor of choice, you don't exactly need to set down ground rules before they
proceed. Very short, immediate problem focused sessions, often where someone
is stepping in to quickly help out do not need the same treatment. Basically, if I'm not
sure I'd call it a pairing session, then I don't worry about it.

Finally, I don't want to give the wrong impression about pair programming – even though
there are ways it can go a little awry, it's nothing to be scared of – I've found that
it's almost always a fun and productive experience. Happy pairing!
