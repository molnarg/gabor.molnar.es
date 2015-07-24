---
layout: post
title: "JavaScript programozás - Fájlok és globális változók"
date: 2013-05-05 10:52
comments: true
categories: 
- JavaScript programozás jegyzet
---

A legtöbb programozási nyelvben lehetőség van arra, hogy **a program egyes részeit külön fájlokban tároljuk**. Egy-egy fájlban általában logikailag összetartozó programkódok vannak: egy webshop programban például valószínűleg nem egy fájlban található a számlakezelés és a raktárkezelés. A számlakezelés lehet hogy több fájlba van szétszedve, attól függően, hogy mekkora a kód mérete, és hogy mennyire elkülöníthetőek az egyes részek egymástól. Az, hogy mennyi, és milyen programkód kerül egy fájlba, az a programozó döntése, hasonlóan ahhoz, ahogy egy író is maga dönti el, hogy a könyvét hogyan bontja részekre, fejezetekre és bekezdésekre. A fájlokra bontással a kód átláthatóbb, könnyebben olvasható lesz (ahogy egy könyv is könnyebben olvasható, ha fejezetekre van osztva).

Az egyes programrészeknek valamilyen módon együtt kell dolgozniuk. Ez JavaScript-ben úgy valósul meg, hogy minden  fájl elérhetővé tesz bizonyos változókat a többi programrész számára (ezeket hívjuk **globális változók**nak), valamint használhatja a többi programrész által elérhetővé tett globális változókat.

Sokszor használunk olyan programrészeket, amiket mások írtak. Az olyan programrészleteket, amiket szándékosan arra terveztek, hogy mások használják őket a saját programjukban, **függvénykönyvtár**aknak nevezzük. Ezeket legtöbbször külön fájlként lehet letölteni, és előre meghatározott globális változókat hoznak létre. Ezek a globális változók általában függvények (innen ered az elnevezés), osztályok vagy objektumok, de természetesen tárolhatnak bármilyen más értéket is (szám, sztring, stb.). Az, hogy hogyan kell használni ezeket a globális változókat (pl. milyen argumentumokat kell átadni az adott függvényeknek) a függvénykönyvtár dokumentációja írja le. Ezt nevezzük a könyvtár **API**-jának (Application Programming Interface).

<!-- more -->

HTML és JavaScript fájlok
-------------------------

Amikor egy weboldalon futtatunk JavaScript kódot, akkor a kódot `<script>` tag-ek közé írva adjuk meg. Egy HTML oldalon akár több ilyen is lehet. Egy-egy `<script>` tag tartalmát **külső fájlként is megadhatjuk az `src` attribútum használatával**:

```html index.html
<!DOCTYPE html>
<html>
<head></head>
<body>

<!-- Ez egy 'beágyazott' script tag, ami kiírja, hogy "16 * 42 = 672" -->
<script>
var szorzat = 16 * 42;
document.write('16 * 42 = ' + szorzat);
</script>

<!-- Ez egy külső fájlként hivatkozott script tag. -->
<script src="elso.js"></script>

<!-- Ez egy másik, külső fájlként hivatkozott script tag. -->
<script src="masodik.js"></script>

<body>
</html>
```

Ilyenkor a böngésző a következőképpen jár el:

1. lefuttatja az első, beágyazott kódrészletet (ami kiírja hogy `16 * 42 = 672`)
2. letölti az `elso.js` fájlt (vagy beolvassa a lemezről, ha a HTML helyi fájl)
3. lefuttatja a letöltött kódrészletet
4. letölti a `masodik.js` fájlt (vagy beolvassa a lemezről, ha a HTML helyi fájl)
5. lefuttatja a letöltött kódrészletet

Az `elso.js` és a `masodik.js` tartalma ilyenkor mindig JavaScript kód (HTML nélkül), például:

```javascript elso.js
// Az elso.js fájl tartalma:
document.write('Ezt a szoveget az elso fajl irta ki.');
```

```javascript masodik.js
// A masodik.js fájl tartalma:
document.write('Ezt a szoveget a masodik fajl irta ki.');
```

Ha az `src` attribútum csak egy fájlnevet tartalmaz, akkor a böngésző a fájlt abban a mappában fogja keresni, ahol a HTML fájl van. Tartalmazhat teljes URL-t is (pl. `http://sajatweboldalam.hu/elso.js`), ilyenkor a fájl az adott helyről tölti le.

Globális változók
-----------------

Amikor egy függvényben a `var` kulcsszót használva létrehozunk egy változót, akkor az a változó csak a függvényen belül látszik. Az ilyen változókat **lokális változók**nak nevezzük. Ha nem egy függvényen belül használjuk a `var` kulcsszót változó létrehozására, akkor globális változó jön létre. Az eddigi programokban tehát legtöbbször globális változókkal dolgoztunk.

A következő példában az `elso.js` egy globális változót hoz létre, a `masodik.js` pedig ezt a változót iratja ki:

```javascript elso.js
// Az elso.js fájl tartalma:
var n = 42;
```

```javascript masodik.js
// A masodik.js fájl tartalma:
document.write("Az elso.js altal letrehozott globalis valtozo tartalma: " + n);
```

A HTML-ben a következő szöveget fogjuk látni: `Az elso.js altal letrehozott globalis valtozo tartalma: 42`.

Létezik másik két módszer is a globális változók létrehozására, ami akár függvényeken belül is használható:

* `window.x = 5;`
  * ha van `x` nevű globális változó: értékül adjuk neki az 5-öt
  * ha nincs `x` nevű globális változó: létrejön, és értékül adjuk neki az 5-öt
* `y = 10;`
  * ha van `y` nevű lokális változó: értékül adjuk neki a 10-et
  * ha van `y` nevű globális változó: értékül adjuk neki a 10-et
  * ha nincs `y` nevű lokális vagy globális változó: létrejön az `y` *globális* változó, és értékül adjuk neki a 10-et

Látható, hogy a második módszer kísértetiesen hasonlít a lokális változó létrehozására használható `var y = 10;`-hez. Ha nem figyelünk oda, akkor könnyen létrehozhatunk globális változókat lokális változók helyett függvények kódjában is. Hogy ez miért lehet baj, azt a következő rész mutatja be.

Ha lehet, használjunk lokális változókat!
-----------------------------------------

Függvényekben nagyon könnyen hibákhoz vezet az, ha mindenre globális változót használunk.

Egy példa arra, hogy milyen hibákról van szó:

```javascript
// Program, ami kiiírja az összes egyjegyű szám négyzetét.

var negyzet_kiiras = function(x) {
  i = x * x;  // Lemaradt a var kulcsszó, ezért itt a globális i változónak adunk értéket
  document.write(x + " négyzete: " + i + '<br>');
};

// Az 'i' ciklusváltozó globális, mert nem függvényben hoztuk létre
for (var i = 0; i < 10; i++) {
  negyzet_kiiras(i);
}
```

Ez a program a következőket írja ki:

```
0 négyzete: 0
1 négyzete: 1
2 négyzete: 4
5 négyzete: 25
```

A függvényben egy elírás miatt lemaradt a `var`, ezért a globális `i` változót használta egy ideiglenes érték eltárolására. Ezt a változót használtuk a for ciklusban is, ez okozta a problémát. Az ilyen hibák nehezen felderíthetők, mert csak ritkán jönnek elő (pl. csak akkor, ha a ciklusváltozó neve éppen `i`), és nagyon furcsa hatásokat tudnak produkálni. A javított `negyzet_kiiras` függvény, és a program helyes kimenete:

```javascript
var negyzet_kiiras = function(x) {
  var i = x * x;
  document.write(x + " négyzete: " + i + '<br>');
};
```

```
0 négyzete: 0
1 négyzete: 1
2 négyzete: 4
3 négyzete: 9
4 négyzete: 16
5 négyzete: 25
6 négyzete: 36
7 négyzete: 49
8 négyzete: 64
9 négyzete: 81
```

Egy egyszerű szabály betartásával megelőzhetőek a hasonló problémák: **a függvények ideiglenes változói mindig legyenek lokálisak!** A lokális változók a függvényen belül mindig "eltakarják" az ugyanolyan nevű globális változókat, tehát nem kell körültekintőnek lenni a lokális változók elnevezésénél, lehet akár azonos neve is egy globális változóval.

Függvénykönyvtár példa
----------------------

Rengeteg letölthető JavaScript függvénykönyvtár létezik, amik a legkülönfélébb dolgokat tesznek lehetővé. Egy népszerű függvénykönyvtár pl. az underscore. Ez egyetlen globális változót hoz létre, aminek a neve `_` (alulvonás) és objektum típusú. Ennek az objektumnak sok metódusa van, mindegyik más feladatot képes elvégezni. Az API dokumentáció a függvénykönyvtár weboldalán megtalálható: [http://underscorejs.org](http://underscorejs.org). Az `_.random` metódus véletlen számok generálására használható ([dokumentáció](http://underscorejs.org/#random)), például így:

```html otoslotto.html
<!DOCTYPE html>
<html>
<head></head>
<body>

<!-- Az underscore betöltése. Léterjön a _ globális változó. -->
<script src="http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.4.4/underscore.js">
</script>

<script>
document.write('Az otos lotto mai nyeroszamai:<br>');
for (var i = 0; i < 5; i++) {
  document.write(_.random(1, 90))
  document.write(' ')
}
</script>

<body>
</html>
```

