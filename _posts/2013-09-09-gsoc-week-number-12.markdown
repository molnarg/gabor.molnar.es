---
layout: post
title: "GSoC week #12"
date: 2013-09-09 22:58
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the 12. week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The main item that I got done this week was the upgrade to the latest draft of the spec: [draft-ietf-httpbis-http2-06](http://tools.ietf.org/html/draft-ietf-httpbis-http2-06). This was a really smooth transition.

I had to rewrite a large part of the [compression code](http://molnarg.github.io/node-http2/doc/compressor.html), but that was expected. I have to say that I like the [current version of the compression spec](http://tools.ietf.org/html/draft-ietf-httpbis-header-compression-03) much better than previous versions. The text is much cleaner and it finally allows the building of a streaming header encoder and decoder implementation. The new implementation is therefore consists of a pair of transform streams that do the compression/decompression. The decompressor is in fact just buffering the input and then processing it in one large batch at the end, but fixing that is already on my todo list ([issue #46](https://github.com/molnarg/node-http2/issues/46)). The next step would be exposing these streaming interfaces on the public API.

For the next week, I'd like to finally start implementing a HTTP/2 test page for browsers (using the to-be-done [failure injection capability](https://github.com/molnarg/node-http2/issues/33)). It has been on the table for a long time now, but I think that the time has come to finally start working on this.

As usual, the latest code is published on npm, and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/). The [prototype public server](https://gabor.molnar.es:8080) is down at the moment, but will be up, and updated soon!

