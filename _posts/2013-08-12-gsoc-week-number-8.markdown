---
layout: post
title: "GSoC week #8"
date: 2013-08-12 22:36
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the 8. week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The two main topics of this week was: testing and flow control.

I participated remotely on the interoperability testing that was organized on the second day of the [HTTPBis Interim Meeting in Hamburg](https://github.com/http2/wg_materials/blob/master/interim-13-08/agenda.md). Working with [Tatsuhiro Tsujikawa](https://twitter.com/tatsuhiro_t) ([nghttp2](https://github.com/tatsuhiro-t/nghttp2)), [Shigeki Ohtsu](https://twitter.com/jovi0608) and [Fred Akalin](https://twitter.com/fakalin) (Chrome) helped to discover a few bugs in both mine and their implementation. The public server set up last week proved to very useful for testing. This screenshot is made by Fred with an experimental build of Chrome:

<img src="/img/gabor_interop.png">

There were several corner cases in flow control that were not handled well, and I believe that these are solved now. In the process of solving these, the flow control implementation of the [Connection](http://molnarg.github.io/node-http2/doc/connection.html) and the [Stream class](http://molnarg.github.io/node-http2/doc/stream.html) were merged into a common parent class: [Flow](http://molnarg.github.io/node-http2/doc/flow.html).

I try to constantly improve the developer documentation, and as part this, I documented the APIs of the Stream, the Connection and the Flow classes. I also added two nice [ASCII art](http://molnarg.github.io/node-http2/doc/stream.html#section-14) [figures](http://molnarg.github.io/node-http2/doc/flow.html#section-14) to make reading the source code less boring :)

For the next week, I plan to [improve the](https://github.com/molnarg/node-http2/issues/26) [public API](https://github.com/molnarg/node-http2/issues/32), [expose server push](https://github.com/molnarg/node-http2/issues/31), and [implement](https://github.com/molnarg/node-http2/issues/20) [prioritization](https://github.com/molnarg/node-http2/issues/19).

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) and the [prototype public server](https://gabor.molnar.es:8080) was updated as well.

