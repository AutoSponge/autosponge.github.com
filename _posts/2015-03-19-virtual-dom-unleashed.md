---
published: true
layout: post
category: blog
tags: [javascript, frameworks, react, virtualdom]
---

I worked on a very large, very complex system with some great folks in one of my previous
jobs. A particular leader kept using abstractions I couldn't buy into: _factory, widget,
state machine_. I eventually found a use for these abstractions but now they're under threat of 
being completely replaced. These are tools of the OO playbook--creating a system of "smart objects".

[React](http://facebook.github.io/react/), [virtual-dom](https://github.com/Matt-Esch/virtual-dom),
and any future incarnations, will change the way we code for the _short term_. No more smart objects;
we want ["dumb" objects](http://slides.com/bahmutov/javascript-journey-scotland-js#/7/7) used through a smart, 
composable, pipeline.

Why only the short term? Well, I still think [web components](http://webcomponents.org/) hold a bright future for development
targeting the browser and something better _could_ land tomorrow.

Some [benchmarks](https://github.com/evancz/todomvc-perf-comparison) show great performance gains 
using the virtual-dom rendering pattern but the real gains come in the form of developer performance. The rendering 
logic can more easily separate from business logic which makes each more testable and easier to both understand
and maintain.

In a recent project, our team delivered functionality using virtual-dom and ES6 syntax
that supported IE8+. Seeing this project reach production under considerable constraints was a personal triumph.
I felt like the system was ultimately easy to reason about, extend, and (with proper testing) maintain.

I feel the next challenge is scaling such an architecture and the key innovations will come from data 
structures and patterns that feed the virtual-dom's render engine. David Nolen's 
[talk](http://www.ustream.tv/recorded/61483785) or 
[article](http://swannodette.github.io/2013/12/17/the-future-of-javascript-mvcs/) on the topic are a great
inspiration.

I think future systems will rely on *cursors* (with or without immutable structures supporting it), *streams*,
and a *virtual-dom* or React-like diffing/rendering algorithm. Cursors, with event emitters, become the heart-beat of 
streams. Reactive libraries then take over yielding only the correct data to the render logic where the virtual-dom's 
proven patterns finish the job.

Another great boost in developer performance will come from streams, and their functional handlers, becoming the 
universal data-type of user input, server-side/peer data, and application state changes. This will ultimately reduce
the developer vocabulary needed to understand a complex system.
