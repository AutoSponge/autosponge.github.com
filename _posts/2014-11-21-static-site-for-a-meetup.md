---
published: true
layout: post
category: blog
tags: [javascript, static, jekyll, bash, yaml, api, meetup]
---

[Github's free hosting](https://pages.github.com/) of jekyll and static sites makes my life easier.
I host this blog, for instance, using `gh-pages`.  I recently started a [meetup](http://www.meetup.com/)
in my hometown and wanted to create a single-page site to both promote the group and serve as a training grounds for
anyone new to front-end work.

To avoid double-entering any data from the meetup site, I created a
[build step](https://github.com/StorrsWebDevelopment/storrswebdevelopment.github.com/blob/master/build.sh)
that copies group data from the [meetup API](http://www.meetup.com/meetup_api/apps/),
pipes it through a couple of filters, and makes a jekyll `_config.yml` file.

When I want to update the data from my site's group data, I run `./build.sh <api key>` and push the changes
to github.  [Jekyll's liquid templates](http://jekyllrb.com/docs/templates/) take care of updating the static
content of the `_site` folder once github detects the push to master.  If I wanted to provide the same
data in a live call I would either need to expose my API key (in the js source) or create a proxy (like in heroku)
to hold my key.  For such a small-scale implementation, neither option seemed appealing.

I think this pattern could be very helpful for generating sites based on API content; especially
if that API has a differencing endpoint.  As long as a content API can describe (and provide affordances for)
any resources changed since a particular date, a script could copy the new data and generate a new site.  If
I needed to manage several sites or a particularly large site, a custom app could sit in between the content
API and github; checking for changes and pushing updates at particular times.
