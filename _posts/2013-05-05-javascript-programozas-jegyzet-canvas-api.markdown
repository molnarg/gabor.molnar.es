---
layout: post
title: "JavaScript programozás - Canvas API"
date: 2013-05-05 10:48
comments: true
categories: 
- JavaScript programozás jegyzet
---

A `<canvas>` egy olyan HTML element, ami egy "rajzvásznat" jelenít meg, amire JavaScript segítségével lehet rajzolni. A rajzoló függvények összefoglaló neve **Canvas API**.

<!-- more -->

Inicializáció
-------------

Az inicializáció során a JavaScript kódban lekérjük a vászon referenciáját (`rajz` változó), majd kiválasztjuk annak 2 dimenziós rajzoló kontextusát (`context` változó). Ez egy objektum, aminek különböző függvényeit meghívva lehet rajzolni.

```html
<!DOCTYPE html>
<html>
<head></head>
<body>

<!-- Egy 500x500 pixel méretű rajzvászon, "rajz" id-vel. -->
<canvas width="500" height="500" id="rajz"></canvas>

<script>
  // A canvas objektumhoz az id-je alapján lehet hozzáférni:
  var rajz = document.getElementById('rajz');
  
  // A rajzoló függvényeket a vászon 2 dimenziós "kontextusában" fogjuk használni
  // (van 3 dimenziós is, de azzal most nem foglalkozunk)
  var context = rajz.getContext('2d');
  
  // Ide jön a további rajzoló kód
  // ...
</script>

<body>
</html>
```

A kurzor
--------

A rajzvásznon mindig van egy láthatatlan kurzor aminek van pozíciója, és iránya. A kurzor kezdő pozíciója a vászon bal felső sarka, és a bal alsó sarok felé néz. Rajzolni mindig a kurzorhoz képest lehet, például utasíthatjuk a gépet, hogy "rajzolj egy kört, aminek a középpontja a kurzor" (lásd később). A kurzor mozgatására használható függvények:

```javascript
// A kurzor elmozgatása balra x-el, és előre y-nal
// Az x és y helyén számnak, vagy számot tartalmazó változónak kell állnia!
// (ez a következő példáknál is érvényes)
context.translate(x, y);

// A kurzor elfordítás az óramutató járásával megegyező irányba r radiánnal
context.rotate(r);
```

Ha fokban szeretnénk számolni radián helyett, használjuk például ezt a függvényt:
```javascript
// A kurzor elfordítás az óramutató járásával megegyező irányba f fokkal
var rotateDegree = function(f) {
  context.rotate((f / 360) * 2 * Math.PI);
};
```

Alakzatok
---------

```javascript
// Rajzol egy szelesseg * magassag méretű téglalap körvonalat a kurzorhoz képest eltolva.
// Az eltolást az x és az y adja meg (értékük lehet 0 is).
// A téglalap a kurzorhoz képest előre és balra fog elhelyezkedni (+eltolás).
context.strokeRect(x, y, szelesseg, magassag)

// Ez a függvény rajzol egy r pixel sugarú kört aminek a középpontja a kurzor
var circle = function(r) {
  context.beginPath();                       // elkezd egy útvonalat
  context.arc(0, 0, r, 0, Math.PI*2, true);  // rajzol egy körívet
  context.stroke();                          // beszínezi a körívet
};
```

Kitöltött alakzatok létrehozásához használjuk a téglalap esetén a `context.fillRect`, a kör esetén a `context.fill` függvényeket a fenti `context.strokeRect` és `context.stroke` helyett. Ezen kívül nem kell semmit megváltoztatni a kódban.

Színek
------

A kurzorhoz mindig tartozik egy kitöltési szín, és egy körvonalszín. Az alakzatok rajzolása mindig az aktuális kitöltési illetve vonalszínnel történik. Alapértelmezés szerint a vonalszín fekete, a kitöltési szín pedig átlátszó. Ezeket egyszerűen meg lehet változtatni:

```javascript
context.strokeStyle = 'green' ;  // A kitöltési szín legyen zöld
context.fillStyle = 'blue';      // A vonalszín legyen kék
```

Az angol nevek mellett használható többféle színskála is. Ezek egyike az ún. HSL színskála (példuál: `context.strokeStyle = 'hsl(123, 50%, 50%)';`), ezzel a http://hslpicker.com/ weboldalon lehet kisérletezni.

Kurzor mentés, visszatöltés
---------------------------

Szükség lehet arra, hogy a kurzor aktuális helyzetét és színét elmentsük, hogy később vissza lehessen tölteni. Például egy összetett alakzatot kirajzoló függvénynek először illik elmenteni a kurzort, és az összetett alakzat kirajzolása után visszatölteni. Erre azért van szükség, mert a hívó arra számít, hogy a függvény meghívása után is ugyanott találja a kurzort, ahol a meghívás előtt volt.

```javascript
context.save();    // A kurzor elmentése.
// egyéb műveletek...
context.restore(); // A kurzor visszatöltése.
```

Animáció
--------

Animációt létrehozni a Canvas API-val nem nehéz, de szükséges hozzá néhány olyan függvény, amiket nem érdemes külön-külön megismernünk, mert ritkán használatosak. Ehelyett használjuk a következő függvényt, ami minden képkocka elején letörli a vásznat, és meghívja az argumentumként megadott `draw_callback` függvényt, ami az adott képkocka kirajzolását kell hogy elvégezze.

```javascript
var animate = function(context, draw_callback) {
  var nextFrame = window.requestAnimationFrame ||
                  window.mozRequestAnimationFrame ||
                  window.msRequestAnimationFrame;
  nextFrame(function callback(timestamp) {
    context.save();
    context.setTransform(1, 0, 0, 1, 0, 0);
    context.clearRect (0, 0, context.canvas.width, context.canvas.height);
    draw_callback(timestamp);
    context.restore();
    nextFrame(callback);
  });
};
```

Példa az `animate` használatára, ami egy jobbra mozgó téglalapot rajzol ki:

```javascript
animate(context, function(t) {        // A t az animáció kezdete óta eltelt idő (ms-ben).
  context.translate(t / 100, 0);      // A kurzort a bal felső sarokból balra mozgatjuk.
  context.strokeRect(0, 0, 100, 100); // Rajzolunk egy 100x100 pixeles téglalapot.
});
```

Olvass tovább
-------------

Ha mélyebben érdekel a Canvas API, [ezen az oldalon](https://developer.mozilla.org/en-US/docs/HTML/Canvas/Tutorial) sokkal részletesebb leírás található angolul.
