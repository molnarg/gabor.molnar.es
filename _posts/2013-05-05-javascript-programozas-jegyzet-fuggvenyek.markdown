---
layout: post
title: "JavaScript programozás - Függvények"
date: 2013-05-05 10:41
comments: true
categories:
- JavaScript programozás jegyzet
---

Függvények a matematikában
--------------------------

**Bemeneti adatokhoz egy kimeneti adatot rendel hozzá**.

Példák:

* Matematika
  * `sin` függvény: egy radián értékhez a radián érték szinuszát rendeli hozzá
  * `T_téglalap` függvény: egy téglalap magasságához, és szélességéhez a téglalap területét rendeli hozzá
* Fizika
  * `v` függvény: egy adott út és idő értékhez egy sebességet rendel hozzá
  * `F` függvény: egy adott tömeg és gyorsulás értékhez egy erő értéket rendel hozzá
* stb...

<!-- more -->

Függvények a programozásban
---------------------------

**Bemeneti adatokhoz egy _opcionális_ kimeneti adatot rendel hozzá**, illetve **egyéb műveleteket végezhet el**.

Fontos fogalmak:

* a bemeneti adatok: a függvény **argumentumai** (function arguments)
* az elvégzett műveletek leírása (pl. számolás a bemeneti adatok alapján): a függvény **kódja** (code)
* a bementi adatokhoz hozzárendelt érték: a függvény **visszatérési értéke** (return value)

Például egy rajzolóprogramban a következő függvények lehetnek megtalálhatók:

* egy képpont színének lekérdezése
  * _argumentumok_: a képpont x és y koordinátája
  * _visszatérési érték_: a képpont színe
  * _egyéb műveletek_: nincs
* egy képpont színezése
  * _argumentumok_: a képpont x és y koordinátája, és a kívánt szín
  * _visszatérési érték_: nincs
  * _egyéb műveletek_: az adott képpontot a megadott színűre színezi

Legtöbbször az "egyéb műveletek" a legfontosabb rész, mert utasításokat akarunk adni a gépnek (pl. színezd be a megadott képpontot!), nem csak számolunk (pl. mennyi a téglalap területe?).

Hosszú programokban sokszor **ismétlődő műveletsorokat kell elvégezni**. Például:

* egy fizikai szimulációban sokszor kell sebességet, vagy lendületet számolni
* egy játékprogramban sokszor kell lekérdezni a leütött billentyűket
* egy levelező webalkalmazásban sokszor kell üzeneteket küldeni a levelezőszervernek

Egy-egy műveletsort nem akarunk minden egyes alkalommal végig, lépésenként leírni. Ilyenkor érdemes az adott műveletsorokhoz függvényeket létrehozni. Ezután már csak az adott függvényt kell használni a teljes műveletsor leírása helyett.

Függvények létrehozása és használata
------------------------------------

A függvényeket a használat előtt **definiálni** kell:

* meg kell adni a függvény
  * *argumentumainak listáját*
  * *kódját*
  * *visszatérési értékét*
* kapunk egy **referenciát** a függvényre

JavaScript-ben:

```javascript
var terulet = function(a, b) { // a függvény argumentumai: a és b
  var t = a * b;               // a számolás elvégzése az argumentumok felhasználásával
  return t;                    // a visszatérési érték a t változó aktuális értéke
};
```

A függvény használata, azaz **a függvény meghívása** a referencia segítségével lehetséges:

```javascript
var x = terulet(3,4);          // a terulet változó egy referenciát tartalmaz a fv-re!
document.write(x);             // 12
```

A referencia ugyanolyan érték típus, mint a számok, vagy a sztringek, tehát **változókban lehet tárolni**.

```javascript
var area = terulet;            // ezen túl az area változó is ugyanazt
var y = area(3,4);             // a függvényreferenciát tartalmazza
document.write(y);             // 12
```

Rekurzió, és környezeti változók
--------------------------------

A JavaScript-ben a függvények **elérhetik a környezetükben levő változókat**, például:

```javascript
var szoveg = "42!";           // "külső" változó (sztring)
var f = function() {
  document.write(szoveg);     // a függvényen belül elérhető
};
f();                          // kiírja, hogy "42!"
```

Sőt, elérhetik önmaguk referenciáját is, mivel ez is egy változóban van tárolva! A referencia segítségével **meghívhatják önmagukat is**. Ha egy függvény meghívja önmagát, azt **rekurziónak nevezzük**.

Példa: a [Fibonacci sorozat](http://hu.wikipedia.org/wiki/Fibonacci-sz%C3%A1mok) kiszámítása. A Fibonacci sorozat első két eleme: 0 és 1. Minden további eleme az előző két elem összege. Az első néhány elem tehát: 0, 1, 1, 2, 3, 5, 8, 13, 21, ... Az első 100 elem kiszámítása függvények, és rekurzió használatával:

```javascript
// Fibonacci fv: az argumentum az elem sorszáma, a visszatérési érték maga az elem:
var fibonacci = function(n) {
  if (n < 2) {
    return n;
  } else {
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
}

for (var i = 0; i < 100; i++) document.write(fibonacci(i) + ', ');
```

Magasabb rendű függvények
-------------------------

**Olyan függvény, ami egyik argumentumaként függvényt vár.** Ilyen függvény azért lehetséges, mert egy függvény referenciája változóként kezelhető, így argumentumként is át lehet adni. Ezt az argumentumot sokszor **callback** függvénynek nevezik.

Példák magasabbrendű függvényekre:

* késleltetés: egy adott idő letelte után meghívja a megadott callback függvényt
* rendezés: egy halmaz elemeinek sorba rendezése a függvényként megadott rendezési elv alapján

Magasabb rendű függvény használata JavaScript-ben:

```javascript
console.log('Ez az üzenet a weboldal betöltésekor jelenik meg a konzolban.');

var f = function() {
  console.log('Ez pedig 5 másodperccel később.');
};

setTimeout(f, 5000);
```

Nem feltétlenül kell először egy változóba elmenteni a függvény referenciát, közvetlenül is át lehet adni:

```javascript
console.log('Ez az üzenet a weboldal betöltésekor jelenik meg a konzolban.');

setTimeout(function() {
  console.log('Ez pedig 5 másodperccel később.');
}, 5000);
```

Beépített függvények
--------------------

Minden program kommunikál valahogy a külvilággal, pl:

* adatokat ír ki, olvas be
* a képernyőre rajzol
* üzeneteket küld más programoknak a hálózaton

Ezekhez a feladatokhoz a **futtatókörnyezet** beépített függvényeit használhatja. A JavaScript esetében a futtatókörnyezet a böngésző, más nyelvek általában más környezetben futnak.

A böngésző beépített függvényei például:

* `document.write(szoveg)`: kiírás a HTML-be
* `console.log(szoveg)`: kiírás a böngésző konzoljába
* `Math.sin(x)`: az x szinuszát adja vissza
* `setDelay(callback, kesleltetes_millisecben)`: késleltetés (az első paramétere függvény!)
* `setInterval(callback, kesleltetes_millisecben)`: periodikusan meghívja a callback függvényt
* [matematikai függvények, és dátumkezelés](http://nyelvek.inf.elte.hu/leirasok/JavaScript/index.php?chapter=12)

A későbbiekben folyamatosan ismerkedni fogunk további hasznos beépített függvényekkel!

