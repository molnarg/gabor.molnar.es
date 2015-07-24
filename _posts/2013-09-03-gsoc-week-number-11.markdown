---
layout: post
title: "GSoC week #11"
date: 2013-09-03 22:46
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the 11. week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The focus of this week was testing: I wrote lots of unit tests to achieve 80% for every test coverage metric on every submodule.

<img src="/img/test-coverage.png">

This led to lot of bugfixes. For example, I was able to fix the issue with push requests I saw last week when testing with Firefox. Additionally, I discovered that node's NPN implementation does not always work well, and opened a [ticket](https://github.com/joyent/node/issues/6168) for this issue.

Main work items for the next week are:

- [update to the -06 version of the draft](https://github.com/molnarg/node-http2/issues/39)
- start working on a HTTP/2 test page for browsers (using the to-be-done [failure injection capability](https://github.com/molnarg/node-http2/issues/33))

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) and the [prototype public server](https://gabor.molnar.es:8080) was updated as well.
