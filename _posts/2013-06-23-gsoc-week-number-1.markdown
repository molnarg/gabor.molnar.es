---
layout: post
title: "GSoC week #1"
date: 2013-06-23 13:03
comments: true
categories:
 - Google Summer of Code
---

This is the summary of the first week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The component I started writing this week is the framer. Frames are the basic unit of communication in HTTP/2 and are defined [here](http://http2.github.io/http2-spec/#frame-types) in the spec. The framer translates between two representations of a frame: JavaScript frame objects and binary buffers.

I decided to implement it as two node.js [transform streams](http://nodejs.org/api/stream.html#stream_class_stream_transform): the Serializer and the Deserializer. I tried to keep things as simple as possible: the main functions are static and either get one frame object as argument and return a buffer or the other way. This is not always optimal since it involves copying data around, but the readability and simplicity of the code is more important now.

I used docco for generating developer documentation, which can be found [here](http://molnarg.github.io/node-http2/framer.html) for the framer. API documentation will be probably part of the README once it makes sense to write it.

Testing is very important part of the project, so I wrote BDD style tests for the framer. These are very easy to run, just install [mocha](http://visionmedia.github.io/mocha/) and [chai](http://chaijs.com/) (`npm install mocha chai`) and then run `npm test`.

The code is hosted on [GitHub](https://github.com/molnarg/node-http2) and can be installed as a (not-yet functional) node.js module (`npm install http2`).

Tasks for the next week:

* handling errors in the framer (probably emitting error events so that upper layers can get notified)
* syncing with the latest version of the spec (new and removed frame types, etc.)
* writing the compressor (that handles header compression/decompression)
* connection management code if I get to it

I would be glad to receive feedback so that I can improve the code. Feel free to open issues in the [issue tracker](https://github.com/molnarg/node-http2/issues), or just [email me](mailto:gabor@molnar.es) directly. If you want to get notified when I post status updates in the future, you can subscribe to the [GSoC category feed](http://gabor.molnar.es/blog/categories/google-summer-of-code/atom.xml) of this blog.

