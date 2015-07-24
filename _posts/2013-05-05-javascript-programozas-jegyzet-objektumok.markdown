---
layout: post
title: "JavaScript programozás - Objektumok"
date: 2013-05-05 10:50
comments: true
categories: 
- JavaScript programozás jegyzet
---

JavaScript-ben (és a legtöbb programozási nyelvben) két alapvető érték típus van: az **egyszerű értékek**, és az **objektumok**. Az egyszerű értékek közé tartoznak a **számok**, **sztringek**, és a **logikai értékek** (`true`, `false`), ezeket már ismerjük.

Az objektumok legfontosabb jellemzője, hogy **tulajdonságokkal (angolul property) rendelkeznek**. Egy tulajdonságnak mindig **neve**, és **értéke** van (például a tulajdonság neve "magasság" és az értéke "170cm").

Objektumokat használva elérhetjük, hogy az összetartozó adatokat mindig együtt kezeljük. Például egy könyvtár szoftverben az egyes könyveknek megfeleltethetünk egy-egy objektumot. Ezeknek az objektumoknak a tulajdonságai lehetnek például a következők: a könyv címe, azonosítója, a könyvet kikölcsönző olvasók listája, stb. Ilyen módon mindig egy helyen elérhető lesz a könyv összes adata, nem kell őket külön változókban tárolni. Ez megkönnyíti a könyvön végzett műveleteket is, például egy könyv leselejtezésekor egyszerre az összes adatát törölni kell a rendszerből.

<!-- more -->

Objektumok a létrehozása
------------------------

Objektumokat több módon lehet létrehozni. Az első, legegyszerűbb lehetőség, hogy **felsoroljuk a tulajdonságait** a következő módon:

```javascript
var konyv1 = {
  szerzo: "Douglas Adams",
  cim: "Galaxis útikalauz stopposoknak",
  kiadas: 1979,
  azonosito: 42
};
```

Ezzel létrehoztunk egy objektumot, aminek a tulajdonságai: az `cim`, a `szerzo`, az `azonosito` és a `kiadas`. A `konyv1` változóban eltároltuk az objektum **referenciáját**.

Az egyes tulajdonságok értéke bármi lehet (szám, sztring, logikai érték, függvény referencia, objektum referencia), a fenti példában minden tulajdonság értéke sztring vagy szám.

Objektumok használata
---------------------

A létrehozás után így érhetjük el az egyes tulajdonságok értékét:

```javascript
console.log(konyv1.cim);
console.log(konyv1.szerzo);
console.log('A könyv ennyi éve jelent meg: ' + (2013 - konyv1.kiadas));
```

Egy objektum egy tulajdonságára tehát úgy lehet hivatkozni, hogy az objektum referenciát tartalmazó változó neve után pontot teszünk, és kiírjuk a tulajdonság nevét.

Egy másik módszer, hogy az objektum változójának a neve után szögletes zárójelben megadjuk a tulajdonság nevét sztring-ként. A következő példák mindegyike működik ezzel a módszerrel is! Ez a példa ugyanazt írja ki, mint az előző:

```javascript
console.log(konyv1['cim']);
console.log(konyv1['szerzo']);
console.log('A könyv ennyi éve jelent meg: ' + (2013 - konyv1['kiadas']));
```

Egy tulajdonság értékének megváltoztatása:

```javascript
konyv1.cim = "Viszlát, és kösz a halakat!";
```

Egy tulajdonság törlése:

```javascript
delete konyv1.szerzo;
```

Az objektum referenciát át lehet adni függvényeknek argumentumként:

```javascript
var konyv_info = function(konyv) {
  return konyv.szerzo + ' - ' + konyv.cim + ' (' + konyv.kiadas + ')';
};

console.log(konyv_info(konyv1));
// Kiírja hogy: "Douglas Adams - Galaxis útikalauz stopposoknak (1979)"
```

Konstruktorok, osztályok
------------------------

Az objektumok létrehozásának egy másik módja, ha először egy objektum **osztályt** definiálunk, és azután azt **példányosítjuk**, aminek hatására létrejönnek az **objektumpéldányok**. Akkor érdemes osztályt létrehozni, ha sok hasonló objektummal fogunk dolgozni, amiknek van valamilyen közös jellemzője.

JavaScript-ben egy osztályt az osztály **konstruktor függvényének** megadásával lehet létrehozni. Ezek a hagyományos függvényektől három szempontból térnek el:

* meghívni őket a `new` kulcsszóval lehet
* a konstruktor meghívásakor létrejön egy üres objektum, amire a függvényen belül a `this` kulcsszóval lehet hivatkozni
* nem kell a függvény végén a `return` kulcsszóval visszatérési értéket megadni, mert a visszatérési érték automatikusan az újonnan létrejött objektumpéldány lesz

A konstruktor szerepe, hogy az újonnan létrejött üres objektum tulajdonságait beállítsa. Példa:

```javascript
var kovetkezo_azonosito = 1;  // A legkisebb szabad (nem foglalt) könyv azonosító

var Konyv = function(szerzo, cim, kiadas) {
  // Az argumentumként megadott adatokhoz tartozó tulajdonságok beállítása
  this.szerzo = szerzo;
  this.cim = cim;
  this.kiadas = kiadas;
  // A könyv azonosítájának beállítjuk a legkisebb lefoglalatlan azonosítót
  this.azonosito = kovetkezo_azonosito;
  // Növeljük a kovetkezo_azonosito-t, hogy a következő már nagyobb sorszámot kapjon
  kovetkezo_azonosito += 1;
};

// Könyv példányok:
var konyv1 = new Konyv("Douglas Adams", "Galaxis útikalauz stopposoknak", 1979);
var konyv2 = new Konyv("Kurt Vonnegut", "Macskabölcső", 1963);
var konyv3 = new Konyv("Déry Tibor", "Kedves bópeer…! ", 1973);
```

Látható, hogy a könyv objektumok létrehozása így rövidebb kóddal elérhető (1 sor objektumonként, vesd össze a jegyzet első példájával), másrészt a konstruktor automatikusan beállítja a könyvek egyedi azonosítóját is.

A konstruktorral létrehozott objektumok minden szempontból ugyanúgy viselkednek, mint a tulajdonságok felsorolásával létrehozott obejktumok: el lehet érni a tulajdonságaikat, át lehet őket adni függvényeknek argumentumként, stb.

Amikor azt mondjuk, hogy egy adott osztálynak van egy *x* tulajdonsága, akkor azalatt általában azt értjük, hogy az adott osztály minden példányának van *x* tulajdonsága (mert a konstruktor beállítja).

Metódusok
---------

Az egyes tulajdonságok értéke bármi lehet, akár függvény is. Az olyan függvényeket, amik egy objektum egy tulajdonságaként érhetőek el, **metódusok**nak nevezzük.

A metódusok különlegessége, hogy azt az objektumot, amihez tartoznak, a `this` kulcsszó segítségével elérhetik.

Az előző példában megírt `konyv_info()` függvényt metódusként is lehetne definiálni. Ha csak egy könyv objektumot hozunk létre, nem pedig osztályt, akkor ezt így tehetjük meg:

```javascript
var konyv1 = {
  szerzo: "Douglas Adams",
  cim: "Galaxis útikalauz stopposoknak",
  kiadas: 1979,
  azonosito: 42,
  info: function() {
    return this.szerzo + ' - ' + this.cim + ' (' + this.kiadas + ')';
  }
};

// A metódus használata:
var s = konyv1.info();
console.log(s);  // A konzolba kiírja hogy "Douglas Adams - Galaxis útikalauz stopposoknak (1979)"
```

Ha több könyv objektumunk is lesz, akkor nem túl praktikus az összeshez leírni az `info()` metódus kódját. Ilyenkor általában osztályt hozunk létre, és az osztály minden példányának beállítjuk a metódust a konstruktorban:

```javascript
var Konyv = function(szerzo, cim, kiadas) {
  // ...
  // Ide jöhet az előző konstruktoros példában megírt kód
  
  this.info = function() {
    return this.szerzo + ' - ' + this.cim + ' (' + this.kiadas + ')';
  };
};

var konyv2 = new Konyv("Douglas Adams", "Galaxis útikalauz stopposoknak", 1979);

var s = konyv2.info();
console.log(s);  // A konzolba kiírja hogy "Douglas Adams - Galaxis útikalauz stopposoknak (1979)"
```

A metódusok legnagyobb előnye, hogy az adott objektumhoz tartozó függvények az adott objektumon keresztül elérhetőek. A metódusokat és a tulajdonságokat használva az összetartozó adatok, és az adatokhoz tartozó műveletek mind egy helyen lesznek elérhetőek, ami általában logikusabb felépítésű és könnyebben olvasható kódhoz vezet.

Példák
------

Még néhány példa arra, hogy mikor szokás objektumokat használni:

* egy rajzolóprogramban lehet egy `Circle` osztály a következő tulajdonságokkal:
  * `x`: a kör középpontjának x koordinátája
  * `y`: a kör középpontjának y koordinátája
  * `r`: a kör sugara
  * `draw()`: metódus, ami kirajzolja a kört a képernyőre
  * `area()`: metódus, aminek a visszatérési értéke a kör területe
* egy zenelejátszó programban lehet egy `Song` osztály a következő tulajdonságokkal:
  * `author`: a szám szerzője
  * `title`: a szám címe
  * `play()`: metódus, ami elkezdi lejátszani a zeneszámot
  * `stop()`: metódus, ami leállítja a zeneszám lejátszását

