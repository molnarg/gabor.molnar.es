---
layout: post
title: "JavaScript programozás - Tömbök"
date: 2013-05-05 18:31
comments: true
categories: 
- JavaScript programozás jegyzet
---

Sokszor van arra szükség, hogy valamilyen elemek listáját kezeljük. Egy webshop esetén például:

* termékek listája
* ügyfelek listája
* megrendelések listája

Az ilyen adatsorok kezelésére az összes programozási nyelv biztosít lehetőséget. Ezeket **tömbök**nek (angolul: **array**), vagy **listák**nak szokás hívni (a két fogalom között sok nyelv különbséget tesz valamilyen szempont alapján, a JavaScript-ben viszont a kettő között nincs különbség).

<!-- more -->

A tömbök mint objektumok
------------------------

A tömbök jól modellezhetők hagyományos JavaScript objektumokkal. Egy terméklistát egy webalkalmazásban kódjában így is megadhatunk:

```javascript
var termeklista = {
  0: 'zöld pengető',
  1: 'kék pengető',
  2: 'húrok',
  3: 'erősítő',
  4: 'hangfal'
}
```

A tömbök nagyon gyakran előjönnek a programozásban, ezért létezik egy kényelmesebben használható **rövidített formátum**, amivel gyakorlatilag ugyanezt az objektumot lehet megadni (a főbb különbségekről később még lesz szó):

```javascript
var termeklista = ['zöld pengető', 'kék pengető', 'húrok', 'erősítő'];
```

**Egy tömb egy eleme bármi lehet, amit egy változó tudna tárolni** (szám, sztring, objektum, függvény, stb.). A fenti példában a tömb összes eleme sztring.

Hozzáférés a tömb elemeihez
---------------------------

A tömbök olyanok, mint a hagyományos objektumok. Objektumok esetében az tulajdonságokat így szokás elérni: `objektum.tulajdonság_neve`. Ez viszont csak akkor működik, ha az tulajdonság neve megfelel bizonyos szabályoknak. Az egyik ilyen szabály, hogy a név ne kezdődjön számmal, emiatt a fent létrehozott objektum tulajdonságait nem fogjuk tudni elérni így: `termeklista.0`, `termeklista.1`, stb.

Ehelyett az objektumoknál látott másik módszert kell használni: `objektum['tulajdonság_neve']`. A szögletes zárójelben sztringet kell megadni, mert a tulajdonság neve mindig sztring. Ebből az is következik, hogy a tömb tulajdonságainak nevei valójában sztring típusúak (pl: `'0'`, `'1'`, `'2'`, nem pedig: `0`, `1`, `2`). Ez azért nem gond, mert ha mégis számot adunk meg a szögletes zárójelben, akkor azt a JavaScript automatikusan sztringgé alakítja át, tehát a következő módszer működni fog: `termeklista[0]`, `termeklista[1]`, `termeklista[2]`, stb. (ugyanennek egy kevésbé kényelmes leírása ez: `termeklista['0']`, `termeklista['1']`, `termeklista['2']`).

Tehát röviden: **egy tömb *n*-edik eleméhez úgy férünk hozzá, hogy a tömb referenciája után szögletes zárójelben megadjuk az *n*-et**. Az *n* lehet bármilyen kifejezés, aminek a kiértékelése számot eredményez, például:

* egy konkrét szám
* számot tartalmazó változó
* bármilyen matematikai kifejezés, ami számot eredményez (pl. `1 + 2`, `3 * a`)

Az előbb létrehozott terméklista néhány elemét például így lehetne kiiratni, illetve megváltoztatni:

```javascript
document.writeln('A terméklista 3. eleme: ');
document.writeln(termeklista[2]);

document.writeln('A terméklista 5. eleme: ');
document.writeln(termeklista[4]);

// A 4. elem nevét megváltoztatjuk:
termeklista[3] = 'zöld erősítő';
```

**A tömböket mindig 0-tól számozzuk**, ezért van az, hogy a 3. elemre a 2-es sorszámmal, az 5. elemre pedig a 4-es sorszámmal hivatkozunk.

Bejárás `for` cikussal, és a `length` tulajdonság
------------------------------------------------

Ha tudjuk a tömb hosszát, akkor akár `for` cikust is használhatunk a tömb bejárására:

```javascript
for (var i = 0; i < 5; i++) {
  document.writeln('A terméklista ' + i + '. eleme: ');
  document.writeln(termeklista[i]);
}
```

A tömbök olyanok, mint a hagyományos objektumok, viszont van néhány plusz tulajdonságuk. A tömb szintaxis használatával valójában mindig a tömb (`Array`) osztály egy példánya jön létre. Ennek az osztálynak van néhány hasznos tulajdonsága illetve metódusa (ezeket minden egyes tömb példány megkapja). Az egyik ilyen **a `length` tulajdonság**, ami **megadja, hogy hány eleme van a tömbnek**. Ez akkor hasznos, ha ezt nem tudjuk előre, például egy adatbázisból kapjuk meg, vagy a felhasználó adja meg a tömböt.

A terméklista kiíratása a lista hosszának megadása nélkül:

```javascript
for (var i = 0; i < termeklista.length; i++) {
  document.writeln('A terméklista ' + i + '. eleme: ');
  document.writeln(termeklista[i]);
}
```

Elemek hozzáadása, törlése
--------------------------

A tömböket termszetesen lehet módosítani a létrehozás után is. Erre használhatóak az objektumok tulajdonságainak állítgatására szolgáló módszerek (pl. új termék felvétele: `termeklista[5] = 'tremolo kar';`, az ötödik termék törlése: `delete termeklista[4];`). Leggyakrabban a tömb első, illetve utolsó elemén kell műveleteket végezni, erre pedig vannak kényelmesebben használható metódusai is a tömb osztálynak:

* `tomb.pop()`: törli, és visszaadja a tömb utolsó elemét
* `tomb.push(uj_elem)`: a tömb végére beszúrja a megadott értéket
* `tomb.shift()`: törli, és visszaadja a tömb első elemét (a tömb minden eleme 1-el előrébb 'csúszik')
* `tomb.unshift(uj_elem)`: a tömb elejére beszúrja a megadott értéket (a tömb minden eleme 1-el hátrébb 'csúszik')

A következő példában a megjegyzésekben szerepel a tömb aktuális állapota az egyes műveletek után:

```javascript
var termeklista = ['zöld pengető', 'kék pengető', 'húrok', 'erősítő'];
// ['zöld pengető', 'kék pengető', 'húrok', 'erősítő']

var utolso_elem = termeklista.pop();
document.write(utolso_elem); // kiírja, hogy 'erősítő'
// ['zöld pengető', 'kék pengető', 'húrok']

termeklista.push('pick-up');
// ['zöld pengető', 'kék pengető', 'húrok', 'pick-up']

var elso_elem = termeklista.shift();
document.write(utolso_elem); // kiírja, hogy 'zöld pengető'
// ['kék pengető', 'húrok', 'pick-up']

termeklista.unshift('hangoló');
// ['hangoló', 'kék pengető', 'húrok', 'pick-up']
```

Egyéb metódusok
---------------

A tömb osztálynak sok hasznos metódusa van, amiket itt nincs hely bemutatni:

* másolás: [slice](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/slice)
* összefűzés: [concat](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/concat)
* átrendezés, törlés: [splice](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/splice), [reverse](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/reverse), [sort](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/sort)
* elemek szűrése, helyettesítése: [map](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/map), [filter](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/filter)
* sztring létrehozása az elemekből: [join](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/join)

