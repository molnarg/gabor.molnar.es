---
layout: post
title: "GSoC week #4"
date: 2013-07-14 20:33
comments: true
categories:
 - Google Summer of Code
---

This is the summary of the fourth week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

This one is going to be short. Last week was mainly about finishing the Stream class. It has tests, docs and everything else such a class needs to have :) The annotated source code is available [here](http://molnarg.github.io/node-http2/stream.html).

Lately I spent more time participating in discussions on the [http-wg mailing list](http://lists.w3.org/Archives/Public/ietf-http-wg/). I feel like the standard is now familiar enough to have useful feedback on some parts of the design. Since my mentor, Nick encouraged me to continue participating, I plan to remain active on the list in the future as well.

Next week I plan to implement the Connection class, that is a major part of the big puzzle, and this will make it possible to have meaningful communication between a node-http2 server and a client. Handshake is not planned for next week yet, but HTTP/2 does allow for a client and a server to communicate without handshake in certain cases, so this will be standard HTTP/2 conversation between the two component! :)

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/framer.html) was updated as well. Feel free to open issues in the [issue tracker](https://github.com/molnarg/node-http2/issues) or just [email me](mailto:gabor@molnar.es) directly if you find any problem.

