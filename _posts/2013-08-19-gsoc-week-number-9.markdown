---
layout: post
title: "GSoC week #9"
date: 2013-08-19 21:46
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the 9. week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The focus of this week was improving the top layer of the stack: the public API and the mapping of HTTP concepts to HTTP/2 streams.

Besides that, with the help of Patrick McManus from the Mozilla networking team, I managed to get node-http2 to work with the [latest HTTP/2 development build of Firefox](https://wiki.mozilla.org/Networking/http2)! In the process we also discovered a bug in Firefox's header compression implementation. If you follow this blog you've probably seen the proof, but here it is again :)

<img src="/img/node-http2-loves-firefox.png">

Improving the top layer meant

* [implementation of connection pooling](https://github.com/molnarg/node-http2/issues/26)
* [exposing server push](https://github.com/molnarg/node-http2/issues/31)
* [adding logging](https://github.com/molnarg/node-http2/issues/27)
* [lot of work on API compatibility with the standard HTTPS module](https://github.com/molnarg/node-http2/issues/32) (full API compatibility is not achieved yet, but should not be long time before it will be)

What I did not have time for is [implementing](https://github.com/molnarg/node-http2/issues/20) [prioritization](https://github.com/molnarg/node-http2/issues/19), which is the main goal for the following days. If I have more time this week, the next thing on my TODO list is an [API for intentional failure injection](https://github.com/molnarg/node-http2/issues/33). This would make it possible to write a server that tests its clients' error handling capabilities.

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) and the [prototype public server](https://gabor.molnar.es:8080) was updated as well.

