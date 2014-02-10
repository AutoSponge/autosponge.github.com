---
published: true
layout: post
category: blog
tags: [application,static]
---

*First rule of hackathon*: "You don't talk about **hackathon**."

That's not really true.  But it does reflect how I usually approach these things.  I don't talk about it
until the last minute.  Why?  Well, some hack events, like [GiveCamp](http://givecamp.org/) keeps you
in the dark until it starts.  I guess that prevents some people from backing out and keeps teams from
depending on people who won't show. Other times, I either don't have a team or the team just can't get
motivated to do something *big*.

So, I usually aim to just *learn something*. Last weekend, a friend and I participated in
[StaticShowdown](http://www.staticshowdown.com/), a hackathon for static web apps.  My buddy, Andrew, works
mostly on the design side; making wire-frames, css, and HTML.  We had fun, and we both learned stuff,
but we could have done a lot of things better.  So this post is about a lot of non-technical stuff--what
I *really learned* this weekend.

## Communication

Teams need to share ideas, provide status updates, and help each other.  Have everyone install and test
chat, video conference, and screen sharing applications.  Try to arrange schedules early on so everyone
knows **when to sleep, work, and eat.**.  Take breaks, it's healthy; just tell the team when you leave
and when you will return.

## Tool Chain

Static web apps can still use server-based tooling, but that only works if everyone has the tool chain
installed and familiarizes themselves with it. In our case, Andrew did not have Node installed, was not
familiar with Git/Github, and was not used to setting up a local server.  These sorts of limitations only
amplify the time crunch because they usually save you time.  And if you can't work without it, it takes
some time to research and **setup**--time you can't afford.

## Skill Sets

Tools like [SparkBoard](http://sparkboard.com/) help organize teams based on skills/roles.  While I think
all hack events should offer this, most events leave this as an exercise for the team.  Try to identify
*who* will do *what when* **before** the event.  For really competitive teams, someone not contributing
code could be on the team and not count toward your limit.  This might include architects, testers,
deployment/ops, or project managers.  Lacking certain skills or depth in certain areas will
limit a team's **ability to execute**.  This can lead to some boot camps and crash courses before the event
starts and save critical building time.

## Scoping

Like any project, a well-developed scope document can help steer the ship and stay the course.  Estimating
build time for each feature as well as prioritizing the features will allow the team to produce something
that works at the end, even if they don't *finish*.  Two things help here: experience and an application
designer.  Someone who can see the *big picture* will save you a ton of time because you won't make as many
**corrective actions** during the event.

## Technology Mapping

Frameworks can save you a ton of time.  But they can also create time sinks if you lack experience with
implementing them or don't fully understand the use cases.  Only the coders need to grok this, but **all**
of them need to understand the basics of the selected framework.  Alternatively, pick as many small
libraries as you need and have a strategy to glue them together.  Stay flexible ready to swap out
components as your needs change.  Having lists of libraries as well as notes about what
each one does before the event will save you **research time**.

## Testing, Mocks, and Stubs

Technically, nothing in a test should be considered part of the app and as a result could be built before the
event starts.  For instance, if you know you will need a router, you can **write the tests** before you start.  You
just can't write the code that makes the test pass.  This can work really well if you use a syntax like
Gherkin and your architect can describe how (nearly) everything works before you start.

If you will use an API, write the code that scrapes the API and saves data to stub files.  While developing,
you can't afford to have people hit their rate limits, or have the API to go down for maintenance.  You will
need stub data.  Since you will not include this in your final app, do it **ahead of time**.  This will also help
you figure out if the target API has limitations or bugs (yes, it happens).

You can serve stub files locally, but some APIs may have complex usage patterns and you may want to create
a mock.  Mocks will allow you to **test your app heavily** and often make the transition from stubs to live
data a bit easier.  Since you don't include this in your app, build it after you capture some stub files.

## Conclusion

While I don't advocate cheating, the capability of weekend warriors to produce a working, stable, and *amazing* app
in 45 hours precludes a lot of teams from trying.  We, as a community, need to do a better job of educating
the public about teams and teamwork.  Optional questionnaires, pre-flight checklists, and dead simple tools
will go a long way to increase participation and application quality.

## For Organizers

*Disclaimer: These aren't all directed at StaticShowdown.*

* Don't tell me you have an IRC channel to "answer any question I might have."  I don't use IRC, I haven't since 1995.
Not everyone participating in your hack event is or wants to be a hipster.  Use chat on your website.  If you want
to use IRC for that chat widget, fantastic.  Just don't make me jump through hoops to get help.

* Don't tell me *after* the event starts that I need Node, NPM, and some beta CLI tool to deploy my finished project.
That should be in the *very first email* with links to tutorials written for small, clueless children.

* Create a tool that helps teams find members, share ideas, and merge resources *weeks* before the event starts.
Tools like this already exist, but if you don't want to use theirs, make your own and let lone developers share
in the fun, meet some *new* people, and learn.

* Actively discourage anyone who works together for a living from participating as a team.  While they might have
fun bro-crushing your event, it discourages amateurs and freelancers from even trying.  That's not good for the
community and ultimately bad for hackathons.

