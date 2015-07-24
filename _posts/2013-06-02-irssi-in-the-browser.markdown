---
layout: post
title: "IRSSI in the browser"
date: 2013-06-02 21:31
comments: true
categories: 
---

<img src="/img/irssi-in-the-browser.png" style="width: 100%;">

This is a quick guide to using the [IRSSI](http://irssi.org/) IRC client in a permanent browser tab. It is made possible by [tty.js](https://github.com/chjj/tty.js/), a great node.js based project that makes the command line accessible in the browser (using a lightweight webserver). A basic setup with IRSSI running on the local machine is quite easy, but it gets a little tricky if you happen to run IRSSI on a server to stay connected all the time. If you run IRSSI on a remote server and want to see it in a browser tab, then this guide is for you.

<!-- more -->

Start IRSSI on the server in screen
-----------------------------------

First of all, IRSSI has to be running on the server in a detached screen:

```bash
$ screen -d -m -S irc irssi
```

`-d -m` makes screen start detached and `-S irc` gives the name `irc` to the session.

Set up tty.js
-------------

On your local machine, you will need to have tty.js installed. It runs on [node.js](http://nodejs.org/), so if you don't have it installed yet, you can download it from [here](http://www.nodejs.org/download/), or install it using the package manager of your linux distribution.

Once you have node.js installed, installing tty.js goes like this:

```bash
$ sudo npm install -g tty.js
```

The config file of tty.js is located at `~/.tty.js/config.json`. Put these lines in the config:

```json
{
  "localOnly": true,
  "port": 8080,
  "shell": "ssh",
  "shellArgs": ["-t", "user@yourserver.com", "screen -r irc"]
}
```

Short explanation:

* `"localOnly": true`: listen on the loopback interface (so that it won't be accessible over the network)
* `"port": 8080`: run on the 8080 port
* `"shell": "ssh"`: use `ssh` instead of a standard terminal
* `"shellArgs": ["-t", "user@yourserver.com", "screen -r irc"]`: connect to the server and attach to the screen session named `irc`

Now you can start it with the `tty.js` command. To test it, open [localhost:8080](http://localhost:8080) in your browser and click on the "Open Terminal" button. You should see IRSSI running in the opening terminal window.

Start everything automatically
-----------------------------

Add the `tty.js` command to the autorun list of you window manager (on Gnome, use `gnome-session-properties`), and use "tab pinning" in your favorite browser to have a permanent IRC tab open.

The only remaining problem is that the "Open Terminal" button have to be clicked when you first switch to the IRC tab. Add these lines to the end of the `index.html` file of tty.js (which is usually located at `/usr/local/lib/node_modules/tty.js/static/index.html`):

```html
<script>
  tty.on('connect', function() {
    var w = new tty.Window();
    setTimeout(function() { w.maximize(); }, 200);
  });
</script>
```

And that's all :)

