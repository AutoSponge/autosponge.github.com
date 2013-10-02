---
published: true
layout: post
category: blog
tags: [javascript]
---

Both and neither.

There are some great books out there and some not so good.  And even some of the most popular
frameworks (e.g., jQuery) have some really bad habits baked into their source--just ask JSLint.

If you want to learn JavaScript, find the principles that resonate with you as a programmer or
designer and learn to implement solutions that do not compromise those principles.

For instance, I dislike ad hoc inheritance that simply uses prototypes.  For one thing, what
if I want to inherit from a Class that has yet to be instantiated?  For another, is the constructor
important?  Because ad hoc inheritance patterns overwrite the constructor.  I also feel strongly
about namespaces, patterns that support commenting (e.g., small, abstract methods), and performance-oriented patterns.

Second, don't confuse your ignorance of the DOM or BOM with your ignorance of JavaScript.
Recognize that if you can't explain event delegation, it's not a deficiency in your knowledge of JavaScript,
you just have a few things to learn about one of the most frequently used APIs in the world.

Ultimately, knowledge of JavaScript and the ability to implement JavaScript solutions are
separated by your knowledge of the APIs and environments.  Rarely does anyone need to code an
application that runs in multiple operating systems or multiple virtual machines.  We tend to
assume the environment as a constant and specialize in our knowledge of the code implementation.
 JavaScript can run in so many environments--and the list is growing.

Learn JavaScript in a standards-based environment, without a framework or crutch.  Don't start
with a project that "runs in every browser."  You'll end up learning everything about environmental
differences and not nearly enough about the language or the proper way to implement it.

For instance, if you learn JavaScript by only using jQuery, your ability to implement an application
on the iPhone will suffer.  However, if you know JavaScript, and you learn the ins-and-outs of the
iPhone's nuances, you can efficiently and effectively evaluate the proper framework to make your job
easier and you can adapt your coding style to fit the new framework.  But if you don't know your
principles or you don't understand the target environment(s) for your code, you'll likely make a poor choice.

For myself, I started with SharePoint shims using jQuery.  I learned jQuery and the deficiencies
of IE but it wasn't until I built my own framework for iPhone's Safari that I really started to learn JavaScript.