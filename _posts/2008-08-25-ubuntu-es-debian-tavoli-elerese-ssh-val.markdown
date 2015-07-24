---
layout: post
title: "Ubuntu és Debian távoli elérése SSH-val"
date: 2008-08-25 13:13
comments: true
published: true
categories: 
---

<!-- more -->

Telepítés
------------

Először is arra a gépre, amire majd távolról akarunk bejelentkezni, telepíteni kell egy ssh szervert:

```bash
$ sudo apt-get install openssh-server
```

Config fájlok
-------------

A konfigurációs fájlokat csak rendszergazdaként lehet szerkeszteni, ezért egy terminálból kell indítani a szövegszerkesztőt, méghozzá sudo-val:

```bash
$ sudo gedit /etc/ssh/sshd_config
```

Három dolgot kell átírni. Először is a fájl legelején van a következő pár sor:

```ruby
# What ports, IPs and protocols we listen for
Port 22
```

Ez annyit jelent, hogy a 22-es porton várja a kapcsolatokat az ssh. Ezt érdemes átírni egy olyan 10000 és 65535 közé eső számra, ami nincs benne [ebben a listában](http://nmap.org/data/nmap-services). Miért? Mert ha 22-esen hagyja az ember, akkor előbb utóbb rátalál a gépre egy hacker/script kiddie és elkezdi próbálgatni a jelszó/felhasználónév párokat, és nem kizárt hogy egy idő után kitalálja, ezt pedig nem szeretnénk.

A következő átírandó rész kb. 20 sorral lentebb van:

```ruby
# Authentication:
LoginGraceTime 120
PermitRootLogin yes
StrictModes y
```

Azt is meg akarjuk akadályozni, hogy valaki root-ként lépjen be ssh-n keresztül, ezért a PermitRootLogin mellett írjunk "no"-t. Ez azért kell, mert a root nevü felhasználó minden linux rendszeren van, ezért egy esetleges támadó először az ehhez tartozó jelszót próbálja majd kitalálni, mert így nem kell bajlódnia azzal, hogy a felhasználónevet is megtippelje.

Beállítások érvénybe léptetése
------------------------------

Az ssh szerver csak akkor olvassa be a konfigurációs fájlt, amikor elindul, ezért a fájl szerkesztése után újra kell indítani:

```bash
$ sudo /etc/init.d/ssh restart
```

Teszt
-----

Egy másik gépről így lehet csatlakozni a szerverhez:

```bash
$ ssh felhasználónév@célpont_ip_címe -p portszám
```

A portszám ugyanaz a szám amit a legelején beállítottunk. Ha minden jól megy, akkor először megkérdezi, hogy megbízunk-e a célpont RSA kulcsának eredetiségében, erre igen a válasz. Ez után a jelszót kéri, és végül, ha minden rendben van, akkor egy shell-t kapunk.

SSH router mögé
---------------

Ha a célpont gép otthoni router mögött van, akkor kívülről közvetlenül nem lehet elérni. A routeren be kell állítani a megfelelő port továbbítást. Hogy ezt hogyan kell, az a router márkájától függ. Szokás szerint a Google tud segítséget nyujtani, a kulcsszó a "port forwarding".

