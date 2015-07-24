---
layout: post
title: "GSoC week #10"
date: 2013-08-27 22:35
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the 10. week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The biggest work item I got done this week is [the](https://github.com/molnarg/node-http2/issues/19) [prioritization](https://github.com/molnarg/node-http2/issues/20).

Besides that, I worked on [API compatibility](https://github.com/molnarg/node-http2/issues/32). That means that the [node-http2 API](https://github.com/molnarg/node-http2/wiki/Public-API) now looks very similar to the standard node.js HTTP API.

Goals for the next week is [improving test coverage](https://github.com/molnarg/node-http2/issues/43), and starting work on a HTTP/2 test service with [failure injection](https://github.com/molnarg/node-http2/issues/33) capability. Failure injection makes it possible to test how HTTP/2 implementations react to malfunctioning peers. Testing browsers with a failure injection tool is one of the main goals of this project, so it's quite important to get right. Possible error scenarios are collected [in the wiki](https://github.com/molnarg/node-http2/wiki/Error-Scenarios).

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) and the [prototype public server](https://gabor.molnar.es:8080) was updated as well.

