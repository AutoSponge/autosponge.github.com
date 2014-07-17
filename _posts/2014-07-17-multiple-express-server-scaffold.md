---
published: true
layout: post
category: blog
tags: [javascript, devops, automation]
---

I recently needed to create a demo of an OAuth2 workflow.  This required four servers in total,
most with ssl, all running concurrently.  The trick was to actually develop this system
within _my_ workflow.

By bringing together the various plugins and writing a configurable base express app, I was able
to make a [scaffold](https://github.com/AutoSponge/multiple-express-server-scaffold) capable of 
supporting multiple servers, creating ssl certificates on the fly, and loading middleware, routers,
and routes dynamically.

 1. Copy the `/app` template and paste it to a sibling folder
 1. Change the `config.json` entry to match your new folder
 1. Make any changes needed to the config
 1. `npm install`
 1. `grunt`
 
If you need to run more than one server at a time, follow the first 2 steps of the previous list,
then copy the basic config and change the app name and port of the new config, then run `grunt`. 

If you have ideas for improving this scaffold or find it useful, please let me know.