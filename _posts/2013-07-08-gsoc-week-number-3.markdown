---
layout: post
title: "GSoC week #3"
date: 2013-07-08 21:36
comments: true
categories:
 - Google Summer of Code
---

This is the summary of the third week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

Last week was about completing the compression code, adding logging and starting the stream implementation.

Header compression is something I've been working on for approximately a week, and now it's finally complete! I've written a [blog post](/blog/2013/07/03/the-http-slash-2-header-compression-implementation-of-node-http2/) last week about trying it out. The compression code is not as optimized as it could be, but it's a good start. It will probably improve in the future. The progress will be tracked in [this issue](https://github.com/molnarg/node-http2/issues/3). After it was finalized, I've spent a sleepless night with coming up with a [proposal](http://lists.w3.org/Archives/Public/ietf-http-wg/2013JulSep/0181.html) for making the spec easier to implement.

<img style="float: right; margin-left: 1em;" src="/img/Akeley-Minnesota-Paul-Bunyan.jpg">

For logging, I chose [node-bunyan](https://github.com/trentm/node-bunyan). It's a great logging library with JSON format output. The biggest upside of using it is that the log can be much more detailed than with other libraries (very important for debugging), while remaining easy to search and pretty printable if needed. There's a [great blog post on the node.js blog](http://blog.nodejs.org/2012/03/28/service-logging-in-json-with-bunyan/) about it. It is an optional dependency, so it won't burn CPU cycles when you don't want it to.

By the way, if the origin of the name 'bunyan' is not clear to you (which is probably the case if you are not American), have a look at [this](http://en.wikipedia.org/wiki/Paul_Bunyan) and [this](http://en.wiktionary.org/wiki/log#Verb) page on Wikipedia :)

The stream code just started to arrive. Originally, I planned to write the `Connection` class first, but the stream multiplexing was not that easy to implement as it seemed to, and I though implementing the `Stream` first would make it easier to figure it out, so the connection code remains for this week.

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/framer.html) was updated as well. Feel free to open issues in the [issue tracker](https://github.com/molnarg/node-http2/issues) or just [email me](mailto:gabor@molnar.es) directly if you find any problem.

