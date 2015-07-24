---
layout: post
title: "GSoC - wrap up"
date: 2013-09-25 20:39
comments: true
categories: 
 - Google Summer of Code
---

This year's Google Summer of Code program has sadly come to an end. It has been a great summer for me: I've met fantastic people, worked on software that will hopefully be widely used in the future, and contributed to the evolving HTTP/2 standard. 

<!-- more -->

My Summer of Code project in numbers:

- 2 node.js modules:
  - [node-http2] : an HTTP/2 client and server implementation for node.js
  - [http2-testpage] : a test server for testing client side HTTP/2 implementations
- 3147 lines of code (the main HTTP/2 implementation code)
- 2647 lines of test (in 94 node-http2 unit tests, and 32 http2-testpage testcases, providing ~90% [code coverage])
- 1426 lines of code comment (base of the generated [developer documentation] and [API reference])
- 502 [commits]
- 16 [blog posts]
- 15 [releases]
- launched the second public HTTP/2 server ever (after Microsoft's [http2-katana] test server)
- with the help of one great mentor :)

My http2 node.js module is ready to use, and I don't plan to break the API in the foreseeable future, so I tagged last week's release as 1.0.0. As most 1.0.0 software releases, this is the end of a long process, but certainly not the end of the work. There are still lot of things to do:

- continuously upgrading to the latest draft of the standard (current draft: [draft-ietf-httpbis-http2-06])
- fixing the issues that come up when more and more people start using this module
- implementing the few [missing features] (the most important one is the [TLS ALPN support])
- [performance optimization]

I will keep maintaining this module in my free time, so expect regular updates in the future too :)

I would like to say thank you to all the amazing people who made all of this possible, especially Nick Hurley of Mozilla, my mentor, who spent countless hours with reviewing my code, listening to my problems and giving advice.

[node-http2]: https://github.com/molnarg/node-http2
[http2-testpage]: https://github.com/molnarg/http2-testpage
[code coverage]: https://github.com/molnarg/node-http2#test-coverage
[developer documentation]: http://molnarg.github.io/node-http2/doc/
[API reference]: https://github.com/molnarg/node-http2/wiki/Public-API
[commits]: https://github.com/molnarg/node-http2/commits/master
[releases]: https://github.com/molnarg/node-http2/releases
[blog posts]: /blog/categories/google-summer-of-code/
[http2-katana]: https://github.com/MSOpenTech/http2-katana
[draft-ietf-httpbis-http2-06]: http://tools.ietf.org/html/draft-ietf-httpbis-http2-06
[missing features]: https://github.com/molnarg/node-http2/issues?labels=feature
[TLS ALPN support]: https://github.com/molnarg/node-http2/issues/5
[performance optimization]: https://github.com/molnarg/node-http2/issues?labels=performance

