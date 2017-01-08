---
layout: post
title: On Moving Away From ColdFusion
comments: true
---

I recently observed a Twitter discussion with some former (and soon to be former) ColdFusion developers, most notably
Raymond Camden ([@RaymondCamden](https://twitter.com/raymondcamden)), Adam Cameron ([@DAC_dev](https://twitter.com/dac_dev)),
and Todd Rafferty ([@webRat](https://twitter.com/webrat)), regarding their decision to move on from ColdFusion
as their primary web development platform. Adam decided to come up with a list of questions for current and former CF developers
to gather their thoughts on this topic. As a former ColdFusion developer who has moved
on to Node.js, I thought I would add my $0.02. Adam's blog post regarding the survey can be found [here](http://blog.adamcameron.me/2017/01/survey-cfml-usage-and-migration.html).
My responses are as follows:

1. *Provide a brief comment about yourself (don't worry about your CFML usage or dev work just yet: this is just about you). Don't worry if
you'd rather not give too much detail, that's cool. Note if you mention your name, I might end up sharing it in the context of what you
had to say in your other answers.*
>I currently work for a K-12 school system and also as a freelance developer and consultant. I originally started out
>in technology as a system administrator/network support engineer before moving into a full-time development role.
2. *How did you come to be a developer, and are you primarily a developer or is it an adjunct to another role (like a sysop or
designer or something like that)?*
>I had an interest in programming from the start of my IT career, and my supervisor at the time was supportive of the idea of me spending
>time learning web development. Around this time there began a large push in the education industry for more standardized
>testing and student/teacher performance evaluations. This began a bit of a gold rush for software development companies building
>products to track assessment results, centralize learning resources, etc., and there was pressure on school systems to
>buy in. By this time I had learned enough that I could
>put something together that was reasonably usable, and convinced (along with my supervisor) the higher-ups that we could
>develop most of these solutions in-house, thereby saving a lot of money. I've been writing software ever since.
3. *Summarise your CFML usage timeline (just timeline for this one). Include things like what year you started, when you moved on, if you have.
(or what's the time timetable for moving on if it's just planned). Mention versions in this one.*
>I'm not sure what year it was that I first started with ColdFusion, but it was an Allaire product at the time and it
>was version 4.5. We kept on a pretty regular upgrade schedule, eventually using versions 6, 7, 8, 9, and 11. In fact, I still
>have 2 ColdFusion servers in production - one running version 9 and the other running 11. Early in 2014, however, is when I really
>started getting involved with Node.js and had put a few small apps and utilities into production. I split time between
>Node.js and ColdFusion for about a year or so before deciding that all my new development would be using Node.js.
>Aside from supporting a few ColdFusion applications that have not been migrated to Node.js I consider myself a full-time Node.js
>developer at this point.
4. *During that time was it your primary or sole dev language, do you think? Or was it always an adjunct to some other
language? How did it fit within the mise en scene of your daily usage? For the purposes of this
answer, let's consider "a wee bit of client-side JS and a bunch of HTML & CSS" as a given. Only mention
those if they represent a significant part of your work.*
>ColdFusion was my primary language during that time for my job and freelancing. I spend a few years working on a Master's
>degree in Computer Science when I wrote a lot of Java code for school, but for anything that made money it was almost all ColdFusion.
>I always spent some time looking at other options (Ruby-on-Rails and Python most notably), but didn't make any changes
>in my day-to-day work.
5. *Did you work on just in-house code bases for your employer, or did you also work on third party code bases - like open
source projects - too.*
>Almost everything was in-house. In recent years I contributed a little to the [ColdFusion UI The Right Way](https://github.com/cfjedimaster/ColdFusion-UI-the-Right-Way)
>project and created an open source [Yeoman generator for scaffolding ColdFusion FW/1 apps](https://www.npmjs.com/package/generator-cfml-mvc),
>though I had pretty much moved on to Node.js by then so I didn't take it as far as I initially planned.
6. *If you're still primarily a CFML developer... why? That's not a loaded question, and I'm not suggesting that you're wrong for being where you are. It's just good framing information. Don't
answer this if you've moved on from CFML: the next question is for you.*
>I've moved on.
7. *If you've moved on from CFML: why? Did you just change jobs? Did other languages you were using just seem more
appealing? Over time did you find yourself using CFML less and less? Did you actively change because of career-longevity
considerations? What language(s) did you move to? Stuff like that.*
>Several factors are worth mentioning that had a role in my decision to leave ColdFusion. As I mentioned earlier I had
>always continued to explore other options, and as I began to get drawn into the Node.js/JavaScript ecosystem I was blown away
>by the community involvement. There is so much innovation and so much life in the JavaScript world; it's refreshing.
>ColdFusion seems so tired and old in comparison.
>
>JavaScript also seems to be one of those languages that you either love
>or hate. I happen to love it (ES6, FTW!), so that was also a consideration as well.
>
>Finally, and perhaps most importantly,
>I'm motivated by a fear of being unemployable. Head over to your favorite job board and search for Node.js, .Net, Go, PHP, Python,
>and Ruby-on-Rails jobs. Compare your finding to the results for a ColdFusion search. That scares me.
8. *Do (/did) you use primarily or solely ColdFusion; or Lucee or Railo; or some variation of BlueDragon?
In what proportions? If you migrated from one to another: why?*
>It was all ColdFusion.
9. *Do (/did) you participate in any CFML-based open source projects? To what degree (like you are the owner or primary committer of the
project; or just a single commit; or raised some bugs but never actually coded anything; or wrote some docs, or whatever)?*
>As mentioned earlier, a small contribution to the ColdFusion UI The Right Way project, and created a Yeoman generator
>for ColdFusion FW/1 apps.
10. *And what about in other languages?*
>Very little, but I intend to increase that moving forward.
11. *What is or was - for you - the best feature of CFML which has you going "yeah, that's pretty cool actually". List more
than one if you like. Importantly: had you compared similar functionality to how it's done in other languages, or was it
just based on liking the CFML feature?*
>Flash forms! I kid, I kid. I still think there is a lot to like about ColdFusion, but to me it really shines
>when it comes to working with databases. Getting data in-and-out of a database is so easy. It also has some nice features
>for working with LDAP. Oh, and the application scope is pretty sweet too.
12. *Are there any CFML features that would have fallen into that category for you when you were doing CFML, but
ended up not being as cool as you thought when you looked at other languages?*
>I admit that I initially thought the UI components were pretty cool. This was before I really got into JavaScript
>development and realized there were much better ways to do this.
13. *What is it about CFML (or the underlying ColdFusion / Lucee / etc platform) you like the least? Explain why, if poss. Again, list as
any or as few as you like. If this/these contributed to you moving on (if you have, I mean), mention that too.*
>I don't have too many bad things to say about ColdFusion; it served me well over the years. There are a few things
>that I find rather annoying, like how serializeJSON() automatically converts keys to uppercase, but any developer
>adequately versed in any language will find things to complain about.
>
>I do hate that ColdFusion marketing seems to be centered around "the easiest way to do X". There was a time when this
>was often true, but it led to a lot of bad features and quit being a selling point long ago. Yet I still feel the direction of the
>platform is too focused on this. Just keep up with modern features and we'll figure the rest out.
14. *If there was a project similar to [ColdFusion UI the Right Way](https://github.com/cfjedimaster/ColdFusion-UI-the-Right-Way),
but aimed at any part of CFML (like how to make a DB query in PHP instead of CFML for example), would you be keen to help on
it? Would it be of interest to you to be a "user" of it (I mean actively interested; not just a "[shrug] yeah, why not?")?*
>Meh.
15. *What else do you think might be useful to share?*
>I think that pretty much sums it up.



