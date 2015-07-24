---
layout: post
title: "GSoC week #2"
date: 2013-07-01 19:02
comments: true
categories:
 - Google Summer of Code
---

This is the summary of the second week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

Last week was about completing the framer and implementing the [header compression spec](http://tools.ietf.org/html/draft-ietf-httpbis-header-compression-00).

The framer is now updated to the latest revision of the HTTP/2 spec, and the error handling is finalized as well. The error handling policy is as follows:

 * when bad frame comes from the layers above, throw exceptions because that's probably a bug
 * when bad frame comes from the network, emit an error event that gets propagated to the layer above that can handle it

The main focus for this week was on compression. This is basically a layer between the framer and the connection handler component. (See [this great slide deck](https://github.com/http2/wg_materials/raw/master/interim-13-06/editor-notes.pdf) from Mark Nottingham for the overview of the layering in HTTP/2)

The two end of the connection have to maintain a synchronized compression state thorough the life of the connection, and it is not always easy. For example, HEADER frames may be split into multiple smaller HEADER frames when they are too large, but when sending these smaller frames, race conditions have to be prevented. If these would be just like any other frame, sending two segmented HEADER frame-set on two streams would possibly result in a race condition, and the sender could not garantee that the frames are decompressed in the same order in which they were compressed. That would lead to a desynchronized compression state. Because of this, the frames in these frame-sets must be sent strictly after each other on the wire to avoid race conditions.

The implementation is in a very good shape, but it is not yet ready. Serialization and deserialization are both working and tested, but the more complicated stuff largely remains for this week.

In the process of implementing the spec, I discovered a few problems in the text and reported them either via [email](https://github.com/http2/compression-spec/issues/8) or with filing [GitHub](https://github.com/http2/compression-spec/issues/7) [tickets](https://github.com/http2/compression-spec/issues/6). I'm quite happy that I could provide useful feedback on the spec! :)

As always, I would be glad to receive feedback to further improve the code! Feel free to open issues in the [issue tracker](https://github.com/molnarg/node-http2/issues), or just [email me](mailto:gabor@molnar.es) directly.

