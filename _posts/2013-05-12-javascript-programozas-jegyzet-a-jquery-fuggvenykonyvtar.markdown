---
layout: post
title: "JavaScript programozás - A jQuery függvénykönyvtár"
date: 2013-05-12 20:16
comments: true
published: true
categories:
- JavaScript programozás jegyzet
---

A böngésző az oldal betöltésekor beolvassa a HTML fájlt, feldolgozza a stíluslapokat és lefuttatja a hivatkozott szkripteket. Ez azonban csak a kezdet :)

A JavaScript segítségével könnyen interaktívvá tehető egy weboldal. Ehhez két nagyon fontos eszközünk is van:

* lehetséges a felhasználó tevékenységeire reagálni (egérkattintás, szöveg beírása, stb)
* meg lehet változtatni a weboldal tartalmát a betöltődés után

Ez a két dolog már elég ahhoz, hogy egyszerű webalkalmazásokat készítsünk.

A beépített függvények viszont, amikkel ezeket a funkciókal elérhetjük, nem mindig egyszerűen használhatók, és van, hogy böngészőnként eltérően működnek. Szerencsére több olyan függvénykönyvtár is van, ami leegyszerűsíti a dolgunkat. Az egyik ilyen, nagyon népszerű függvénykönyvtár a [jQuery](http://jquery.com/), ennek a használatát fogom röviden bemutatni.

<!-- more -->

Document Object Model
---------------------

**A böngésző** a HTML fájl beolvasásakor sorról sorra halad, és **a beolvasott HTML tag-ekből objektumokat hoz létre**. Ezeket az objektumokat **element**-eknek, **elem**eknek vagy **node**-oknak nevezzük, és JavaScript-ből is elérhetőek. Az element objektumoknak olyan metódusai, és tulajdonságai vannak, amiket felhasználva lehetséges az adott element adatait (pl. hol helyezkedik el a képernyőn, stílus adatok, stb.) kiolvasni és módosítani.

Mivel a HTML-ben az egyes tag-eknek lehetnek "gyerekei", ezért a létrejövő objektumhalmaz is hierarchikusan épül fel. A hierarchia legfelső szintjén a `document` objektum van, ennek a gyereke a `<html>` tag-nek megfelelő element, ennek pedig a `<head>` és `<body>` tag-eknek megfelelő objektumok, stb.

**Ezt a hierarchikus szerkezetet nevezzük Document Object Model (DOM) fának.**

Két jellemző tulajdonság, amivel minden element rendelkezik:

* `nodeName`: az adott element típusát tároló sztring (legtöbbször a tag neve csupa nagybetűvel, pl. `"HEAD"`, `"BODY"`, `"P"`)
* `childNodes`: az adott element "gyerekeit" tároló tömb, aminek elemei szintén element-ek

Ezen kívül nagyon sok más tulajdonsággal rendelkezik minden element, de ezekkel egyelőre nem foglalkozunk.

DOM példa
---------

Egy rövid példán keresztül mutatom be, hogy egy HTML kódból milyen DOM fa jön létre. A példában az elementeknél csak a `nodeName` és a `childNodes` tulajdonságot írtam le, a többit kihagytam. A bal oldalon a HTML, a jobb oldalon a belőle létrejövő (egyszerűsített) DOM fa látható:

<div style="float: left; width: 48%">
```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <div id="negyzet"
         style="width: 100px;
                height: 100px;
                background-color: black;"
    ></div>
  <body>
</html>
```
</div>
<div style="float: right; width: 48%">
```javascript
var document = {
  nodeName: '#document',
  childNodes: [{
    nodeName: 'HTML',
    childNodes: [{
      nodeName: 'HEAD',
      childNodes: []
    }, {
      nodeName: 'BODY',
      childNodes: [{
        id: 'negyzet',
        nodeName: 'DIV',
        childNodes: []
      }]
    }]
  }]
};
```
</div>
<div style="clear: both"></div>

A fenti HTML kód egy fekete színű négyzetet jelenít meg, aminek az `id` attribútuma `negyzet`:

<div id="negyzet"
     style="width: 100px;
            height: 100px;
            background-color: black;"
></div>
<p></p>

Most változtassuk meg a négyzet színét! A lenti kódrészletet akár a konzolban (CTRL+SHIFT+J) is ki lehet próbálni ezen az oldalon:

```javascript
var negyzet_element = document.getElementById('negyzet'); // A "négyzet" element lekérdezése id alapján
negyzet_element.style.backgroundColor = 'green';          // Háttérszín beállítása
```

A jQuery függvénykönyvtár
-------------------------

Az előző kódrészletet nem magyaráztam el részletesen, mert olyan DOM függvényeket, tulajdonságokat használtunk, amikre később nem lesz szükségünk. A DOM-ról általánosságban elmondható, hogy elég bonyolult használni, és mindenre speciális függvények, objektum tulajdonságok vannak, amiket mindig a dokumentációban kell megnézni, mert nehezen megjegyezhetők.

Ehelyett a **jQuery** függvénykönyvtárat fogjuk használni, ami megoldja helyettünk a DOM kezelését, és **egyszerűen használható, ha értünk a CSS-hez**. A DOM bevezetésre azért volt szükség, hogy lássuk, a jQuery nem varázslat, hanem olyan dolgokat könnyít meg, amit mi is meg tudnánk csinálni, csak körülményesen.

A jQuery-t könnyen be lehet tölteni egy szkript tag segítségével:

```html
<script src="http://code.jquery.com/jquery-1.9.1.min.js"></script>
```

A betöltött szkript egyetlen függvény típusú globális változót hoz létre, aminek a neve `$` (dollárjel).

Elementek kiválasztása
----------------------

A jQuery-nél egy-egy művelet elvégzése (pl. a négyzet színét változtassuk meg zöldre) mindig két részre osztható:

1. **kiválasztjuk** azokat **az elementeket**, amiken a módosítást el akarjuk végezni (pl. a "negyzet" id-vel rendelkező element)
2. a kiválasztott elementeken valamilyen metódus segítségével **elvégezzük a módosítást** (pl. a kiválasztott elementek színét megváltoztatjuk zöldre)

Elementeket kiválasztani [CSS szelektor kifejezések](http://patrik.what.hu/web/css.html#chapter2) segítségével lehet. Emlékeztetőül néhány egyszerű CSS szelektor kifejezés:

* `#azonosito`: azt az egy elementet választja ki, aminek az `id` attribútuma `azonosito`
* `.osztaly`: azokat az elementeket választja ki, amiknek a `class` attribútumában szerepel az `osztaly` (a class attribútumban szóközzel elválasztva több különböző osztálynév is szerepelhet)
* `div`: kiválasztja az összes div típusú element-et

Ha megvan a szelektor kifejezés, akkor ezt sztringként kell átadni a `$` függvénynek, az pedig visszaad egy objektumot (nevezzük ezt *jQuery objektumnak*), ami a kiválasztott elementeket tartalmazza. Később ezen az objektumon keresztül lehet majd a különféle műveleteket elvégezni.

Néhány példa kiválasztásra:

```javascript
var negyzet = $('#negyzet');  // a "negyzet" id-vel rendelkező element
var kodsorok = $('.line');    // a "line" class-ba tartozó elementek
var bekezdesek = $('p');      // az összes "p" típusú element (vagyis a bekezdések)
```

CSS tulajdonságok módosítása
----------------------------

Ha megvan a kiválasztás, a visszakapott jQuery objektumon a `.css(nev, ertek)` metódust meghívva állíthatunk be stílus tulajdonságokat. Ahogy stíluslapok szerkesztésekor, itt is a stílus szabály nevét, és a hozzá tartozó értéket kell megadni.

A négyzet háttérszínét tehát így lehet átállítani (a konzolban ki lehet próbálni ezen az oldalon, a fenti négyzet színét fogja átállítani!):

```javascript
var negyzet = $('#negyzet');            // a "negyzet" id-vel rendelkező element kiválasztása
negyzet.css('background-color', 'red'); // a kiválasztott element(ek) háttérszínének átállítása vörösre
```

Azt nem kell tudnunk előre, hogy a kiválasztás hány elementet ad vissza. Ha a kiválasztásban egyszerre több element szerepel, akkor egyszerre mindegyiken végzünk műveleteket, ha pedig nem szerepel egy element sem, akkor nem történik semmi.

A következő két példa közül az első az összes bekezdés betűszínét megváltoztatja, a második viszont nem csinál semmit, mert nincs olyan element, amire illeszkedne a megadott kiválasztó kifejezés:

```javascript
var bekezdesek = $('p');          // a "p" típusú element(ek) kiválasztása
bekezdesek.css('color', 'blue');  // a kiválasztott element(ek) betűszínének átállítása kékre

var ures_kivalasztas = $('.nemletezo'); // a "nemletezo" osztályú element(ek) kiválasztása
ures_kivalasztas.css('color', 'blue');  // a kiválasztott element(ek) betűszínének átállítása kékre
```

Események
---------

A böngésző a weboldal felhasználójának legtöbb tevékenységét (kattintás, szöveg beírása, stb.) eseményként kezeli. Egy eseményhez mindig hozzá tartozik két fontos tulajdonság (de ezen felül lehet több adata is):

* az esemény típusa (pl. kattintás)
* az az element, amin az esemény keletkezik (pl. az az element, amire a felhasználó rákattintott)

Az **eseményekre fel lehet iratkozni**, ami úgy zajlik, hogy **megadunk egy függvényt, ami meghívódik, ha az esemény bekövetkezik**. A feliratkozásnál mindig meg kell adni, hogy melyik element milyen típusú eseményeire iratkozunk fel.

A jQuery-ben az `.on(esemeny_tipusa, esemenykezelo_fv)` metódus használható feliratkozásra, amit azon a jQuery objektumon kell meghívni, amelyiken a várt esemény be fog következni. A két argumentum jelentése:

* `esemeny_tipusa`: sztring típusú argumentum, ami az esemény típusát adja meg (pl. 'click')
* `esemenykezelo_fv`: függvény típusú argumentum, ami meg fog hívódni az esemény hatására

Példa a használatára:

```javascript
var negyzet = $('#negyzet');
negyzet.on('click', function() {
  if (Math.random() < 0.5) {
    negyzet.css('background-color', 'green');
  } else {
    negyzet.css('background-color', 'red');
  }
});
```

A példát érdemes a konzolban kipróbálni. Ez után a fenti négyzet kattintás hatására véletlenszerűen vörös vagy zöld színű lesz.

További események, metódusok
----------------------------

Néhány további gyakran használt esemény típus:

* `mouseenter`: az egérkurzor a kiválasztott element felé kerül
* `mouseleave`: az egérkurzor a kiválasztott element területét elhagyja
* `dblclick`: dupla kattintás
* `change`: beviteli mezőkön (`<input>`) jön létre, ha megváltozik a mező tartalma

Egyéb hasznos metódusok jQuery objektumokon egyszerű alkalmazások készítéséhez:

* `.val(ertek)`: beviteli mezők (`<input>`) tartalmának beállítása vagy kiolvasása (ha meg van adva az `ertek` argumentum, akkor beállít, egyébként kiolvas; [dokumentáció](http://api.jquery.com/val/))
* `.attr(nev, ertek)`: egy HTML attribútum lekérdezése, vagy beállítása (ha meg van adva az `ertek` argumentum, akkor beállít, egyébként kiolvas; [dokumentáció](http://api.jquery.com/attr/))
* `.hide()`: a kiválasztott elementek elrejtése ([dokumentáció](http://api.jquery.com/hide/))
* `.show()`: a kiválasztott elementek mutatása, ha el voltak rejtve ([dokumentáció](http://api.jquery.com/show/))


