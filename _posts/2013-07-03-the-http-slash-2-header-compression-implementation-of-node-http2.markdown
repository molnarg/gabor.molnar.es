---
layout: post
title: "The HTTP/2 Header Compression implementation of node-http2"
date: 2013-07-03 18:21
comments: true
categories:
 - Google Summer of Code
---

I'm happy to announce that node-http2 now implements the first draft of the [HTTP/2 Header Compression][1] spec. This is the '-00' version released on June 25, 2013.

The source code and the [docco][2]-generated documentation of node-http2 is available on [GitHub][3] and in the [NPM repository][4]. The documentation for the compression code is available [here][5] as well.

<!-- more -->

This implementation is designed to be part of a larger project, node-http2, but it's easily usable in itself as well. If you'd like to see it for yourself, please follow the instructions in the next sections.

[1]: http://tools.ietf.org/html/draft-ietf-httpbis-header-compression-00
[2]: http://jashkenas.github.io/docco/
[3]: https://github.com/molnarg/node-http2
[4]: https://npmjs.org/package/http2
[5]: http://molnarg.github.io/node-http2/compressor.html

First steps
-----------

Install node-http2 using npm, and start node.js:

```bash
$ npm install http2
npm http GET https://registry.npmjs.org/http2
npm http 200 https://registry.npmjs.org/http2
http2@0.0.3 node_modules/http2
$ node
>
```

Load the two compressor classes:

```javascript
> var http2 = require('http2');

> var Compressor = http2.compressor.Compressor;

> var Decompressor = http2.compressor.Decompressor;
```

Reproducing the [example of the compression spec][1]
----------------------------------------------------

[1]: http://tools.ietf.org/html/draft-ietf-httpbis-header-compression-00#appendix-B

```javascript
> var decompressor = new Decompressor('REQUEST');

> var headerblock1 = '44162f6d792d6578616d706c652f696e6465782e68746d6c4d0d6d792d757365722d6167656e' +
                     '74400b782d6d792d686561646572056669727374';

> decompressor.decompress(new Buffer(headerblock1, 'hex'));
{ ':path': '/my-example/index.html',
  'user-agent': 'my-user-agent',
  'x-my-header': 'first' }

> console.log(decompressor._context._table);
[ [ ':scheme', 'http', size: 43 ],
  [ ':scheme', 'https', size: 44 ],
  ...
  [ 'warning', '', size: 39 ],
  [ ':path', '/my-example/index.html', size: 59 ],
  [ 'user-agent', 'my-user-agent', size: 55 ],
  [ 'x-my-header', 'first', size: 48 ] ]

> var headerblock2 = 'a6a804261f2f6d792d6578616d706c652f7265736f75726365732f7363726970742e6a735f0a0' +
                     '67365636f6e64';

> decompressor.decompress(new Buffer(headerblock2, 'hex'));
{ 'user-agent': 'my-user-agent',
  ':path': '/my-example/resources/script.js',
  'x-my-header': 'second' }

> console.log(decompressor._context._table);
[ [ ':scheme', 'http', size: 43 ],
  [ ':scheme', 'https', size: 44 ],
  ...
  [ 'warning', '', size: 39 ],
  [ ':path', '/my-example/resources/script.js', size: 68 ],
  [ 'user-agent', 'my-user-agent', size: 55 ],
  [ 'x-my-header', 'first', size: 48 ],
  [ 'x-my-header', 'second', size: 49 ] ]

```

Compressing and then decompressing
----------------------------------

```javascript
> var decompressor = new Decompressor('REQUEST');

> var compressor = new Compressor('REQUEST');

> var buffer = compressor.compress({ ':path': 'index.html', 'user-agent': 'Firefox' });

> buffer;
<Buffer 04 03 0a 69 6e 64 65 78 2e 68 74 6d 6c 0d 0c 07 46 69 72 65 66 6f 78>

> decompressor.decompress(buffer);
{ ':path': 'index.html',
  'user-agent': 'Firefox' }
```

This implementation is prabably not without bugs. If you find any, please report in the [issue tracker][6]. Thank you!

[6]: https://github.com/molnarg/node-http2/issues


