---
published: true
layout: post
category: blog
tags: [javascript, frameworks, polymer, webcomponents, performance]
---

I did an experiment adding the [Polymer todomvc implementation](https://github.com/polymer/todomvc) to the great
[todo-perf-comparison](https://github.com/evancz/todomvc-perf-comparison) project. My theory was that, based
on how web components update their models, any application using small components (with small
models) would naturally perform as well or better than React.

(I also found a bug in one of the tests)

While my hunch was validated, I was equally surprised when I thought to run the benchmark
in Safari and realized how much better it ran in Chrome where the web components are
supported natively.

##Chrome

![chrome](https://github.com/AutoSponge/todomvc-perf-comparison/raw/master/chrome.png)

##Safari

![safari](https://github.com/AutoSponge/todomvc-perf-comparison/raw/master/safari.png)

see the [repo](https://github.com/AutoSponge/todomvc-perf-comparison)
