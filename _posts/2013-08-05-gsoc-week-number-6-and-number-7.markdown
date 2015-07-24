---
layout: post
title: "GSoC week #6 and #7"
date: 2013-08-05 23:01
comments: true
categories: 
 - Google Summer of Code
---

This is the summary of the sixth and seventh week of my Google Summer of Code project: [HTTP/2 prototype server implementation in node.js](https://google-melange.appspot.com/gsoc/project/google/gsoc2013/molnarg/5001).

<!-- more -->

The summary for the sixth week is short: I've had summer holiday :) Next week marked a quite big milestone for the project: having a public API and launching a public test server!

The public API is very similar to the [standard node.js HTTPS API](http://nodejs.org/api/https.html). The goal is to achieve perfect API compatibility, and to add HTTP/2 related stuff as extension to the API. Currently, there's o public API for server push, but that's probably coming this week.

A short example of using node-http2 as client:

```javascript
var http2 = require('http2');

var request = http2.request({
  method: 'get',
  host: 'gabor.molnar.es',
  port: 8080,
  url: '/',
  rejectUnauthorized: false
});
request.end();

request.on('response', function(response) {
  response.pipe(process.stdout);
});
```

Using as server:

```javascript
var http2 = require('http2');

var options = {
  key: fs.readFileSync('./example/localhost.key'),
  cert: fs.readFileSync('./example/localhost.crt')
};

http2.http.createServer(options, function(request, response) {
  response.end('Hello world!');
}).listen(8080);
```

Simple isn't it?

I've also set up a public server at [https://gabor.molnar.es:8080](https://gabor.molnar.es:8080). This serves up the same content (the blog) as the main server at port 80, but it is possible to negotiate HTTP/2 using the TLS NPN extension (no ALPN support yet). If the client does not support HTTP/2 then it falls back to simple HTTPS, so you should be able to see the served content even if the client does not support HTTP/2 yet!

That's it for now. I decided to use GitHub issues to maintain a TODO list, so have a look at the [issues page](https://github.com/molnarg/node-http2/issues) if you want to se what's ahead. Please open an issue or [email me](mailto:gabor@molnar.es) directly if you find issues while testing.

As usual, the latest code is published on npm (just type `npm install http2` to install it), and the [hosted version of the docs](http://molnarg.github.io/node-http2/doc/) was updated as well.
