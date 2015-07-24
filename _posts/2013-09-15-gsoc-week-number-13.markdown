---
layout: post
title: "GSoC week #13"
date: 2013-09-15 22:00
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the 13. week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

This week was about making node-http2 fast, and starting work on a new project: [http2-testpage](https://github.com/molnarg/http2-testpage).

It all began with a [GitHub issue](https://github.com/molnarg/node-http2/issues/47) opened by [Tatsuhiro Tsujikawa](https://twitter.com/tatsuhiro_t) (creator of [spdylay](https://github.com/tatsuhiro-t/spdylay) and [nghttp2](https://github.com/tatsuhiro-t/nghttp2)). It took lot of time to fix it, but then I was so deep into this performance optimization thing that I continued with other improvements. The optimizations were all based on CPU profiling data collected using [node-webkit-agent](https://github.com/c4milo/node-webkit-agent).

The result is that it now takes 2 seconds to serve 600 small parallel request from the same client, while it took so long before that I could not even wait for it to complete. It's great improvement, but I still plan to further optimize this, since there's still room for improvements. I also plan to post about the lessons learned during the process sometime next week.

Other than that, I started [http2-testpage](https://github.com/molnarg/http2-testpage). It's a tool that injects intentional failures into HTTP/2 connections to test clients' reaction. The next week will be dedicated to working on this.

As usual, the latest code is published on npm (`npm install http2` and `npm install http2-testpage`). The [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) and the [prototype public server](https://gabor.molnar.es:8080) was updated as well.

