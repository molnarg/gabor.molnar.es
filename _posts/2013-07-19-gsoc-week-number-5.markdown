---
layout: post
title: "GSoC week #5"
date: 2013-07-19 17:47
comments: true
categories:
 - Google Summer of Code
---

This is the summary of the fifth week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

This week was about achieving an important milestone: making the first successful HTTP/2 request with node-http2! I am quite happy that I managed to achieve it before going to holiday next week. Please note that it does not mean support for the [HTTP/1 upgrade process][1] or [ALPN negotiation][2]. It's [raw HTTP/2 without any negotiation][3].

[1]: http://tools.ietf.org/html/draft-ietf-httpbis-http2-04#section-3.2
[2]: http://tools.ietf.org/html/draft-ietf-httpbis-http2-04#section-3.3
[3]: http://tools.ietf.org/html/draft-ietf-httpbis-http2-04#section-3.4

Before showing a quick demo, a little bit more info about other things I did this week. First of all, it is now possible to measure test coverage of the node-http2 codebase. It is made possible by a great code coverage tool called [istanbul](https://github.com/gotwarlost/istanbul). It is this easy:

```bash
$ npm test --coverage

...

=============================== Coverage summary ===============================
Statements   : 91.18% ( 775/850 )
Branches     : 84.69% ( 249/294 )
Functions    : 88.03% ( 103/117 )
Lines        : 91.18% ( 775/850 )
================================================================================
```

It also generates a detailed, line-by-line analysis, that you can have a look at [here](http://molnarg.github.io/node-http2/coverage/lcov-report/lib/). Using this, I managed to achieve 100% test coverage for the compression code, and in the process, I fixed several bugs that were discovered while writing the new test cases. I know that this does not mean that the compression code is now free of bugs, but still, it's more reliable than before.

To make a HTTP/2 request for yourself using node-http2, follow these steps.

Clone node-http2 and install the development dependencies.

```bash
$ git clone https://github.com/molnarg/node-http2.git

$ cd node-http2

$ npm install
```

Start the server in one shell (use the HTTP2_PORT environment variable to change the port):

```bash
$ node ./example/server.js
Listening on localhost:8080, serving up files from ./example
```

Finally, use the client in an other shell to download the source code of the server:

```bash
$ node ./example/client.js 'http://localhost:8080/server.js' 2>/tmp/server.js
```

To see the nice colored log output, try these (or use `bunyan --no-color` if your terminal does not play nice with bunyan):

```bash
$ sudo npm install -g bunyan # To be able to use the bunyan command line tool to format the logs
$ HTTP2_LOG=debug node ./example/server.js | bunyan -o short
$ HTTP2_LOG=debug node ./example/client.js 'http://localhost:8080/server.js' 2>/tmp/server.js | bunyan -o short
```

The week after I come back, the plan is to implement a public API, sort out the handling of PUSH_PROMISE, and to write more documentation and test cases. I also plan to run a public server with publicly available debug logs to make it easy for client implementors to test with node-http2.

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) was updated as well. Feel free to open issues in the [issue tracker](https://github.com/molnarg/node-http2/issues) or just [email me](mailto:gabor@molnar.es) directly if you find any problem.

