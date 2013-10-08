---
published: true
layout: post
category: blog
tags: [javascript, quality, process]
---

The more [complex](http://en.wikipedia.org/wiki/Programming_complexity) your code, the more trouble you
(or other hapless souls) will have maintaining or enhancing it.  This is especially important for
open source software. Sometimes we pride ourselves on complexity and some shameless developers
parley it into job security. But while we _can_ make complex systems, we _should_ make the inner
components of that system as simple as possible.

Certain design and testing methodologies can guide developers to create smaller, less complex components.
[Flow-based Programming (FBP)](http://en.wikipedia.org/wiki/Flow-based_programming)
seems to encourage organized, maintainable applications. Especially for
modern web applications where we inter-connect disparate systems, "design by contract" makes sense.

Take the [clock example](http://noflojs.org/noflo-ui/#example/6699161) from [NoFlo](http://noflojs.org/):
most experienced front-end developers can recreate this widget and you can find several tutorials
by searching the interwebs. I decided to follow the NoFlo design to write a clock application using
SVG and the minimum of HTML markup. I wanted to see if following the design like a road map
helped me make a more maintainable application.

I was quite pleased with the resulting maintainability score of *135*, which beat similar tutorials
by at least 15 points.  I made sure I was on the right track during development by using the
[grunt-complexity](https://github.com/vigetlabs/grunt-complexity) task in my build step.
I also wanted to explain my implementation, so I used [Docco](http://jashkenas.github.io/docco/)
to comment the code.

*Note*: since my normal [TDD](http://en.wikipedia.org/wiki/Test-driven_development) practices often
steer code quality, I did this development without tests to make sure that only the FBP map guided
my decisions.  Hence, the project has no tests.

See the project: [https://github.com/AutoSponge/svg-clock](https://github.com/AutoSponge/svg-clock)