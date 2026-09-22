---
name: komponensterv
description: Komponensterv-sablon PLC szoftverkomponensekhez (protokoll-illesztő, eszközillesztő, funkcióblokk). Használd, amikor komponenstervet írsz vagy vizsgálsz felül, illetve mielőtt egy új PLC komponens kódját elkezdenéd — a terv előbb készül el, mint a kód.
---

# Komponensterv-sablon

Minden komponensterv ezt a **11 fejezetet** járja be, ebben a sorrendben. Ha egy fejezet
nem értelmezhető az adott komponensre, **nem marad ki** — egy sorban le kell írni, hogy
miért nem.

**A sablon célja mérhető:** ha a terv alapján a kód megírható úgy, hogy közben nem kell
újra a gyártói adatlaphoz nyúlni, akkor a terv kész.

## Jelölésrendszer

Minden érdemi állítás jelölt:

| Jel | Jelentés |
|---|---|
| **[GY]** | **Gyártói adat** — adatlapból vagy könyvtárdokumentációból igazolt. Mindig konkrét forrásra és oldalszámra hivatkozik. |
| **[D]** | **Tervezői döntés** — a mi választásunk, az indoklás kiírásával. Visszavonható, de csak tudatosan. |
| **[?]** | **Nyitott** — még nem eldönthető. Mindig megnevezi, **honnan** zárható le: hiányzó dokumentumból vagy üzembehelyezési mérésből. |

Ami a gyártói adatlapból nem olvasható ki közvetlenül, az nem lehet **[GY]**. A számított
értékek (időzítés, buszterhelés) a levezetéssel együtt szerepelnek.

## A kötelező fejezetváz

### 1. Cél és hatókör
Mit old meg a komponens, és **mit nem**. A hatókör kimondása fontosabb, mint a célé: ez
akadályozza meg, hogy a szabályozás beszivárogjon egy illesztőbe.

### 2. Fizikai illesztés
Melyik porton/modulon át, milyen kábellel, milyen sorkapcsokra. Lezárás, árnyékolás,
galvanikus leválasztás. Az eszközfa konkrét útvonala a CODESYS projektben.
Ha a komponens tisztán szoftveres: „nincs fizikai illesztése".

### 3. Protokoll- és adatszerződés
A cím-, regiszter- vagy objektumkészlet, adattípusokkal és mértékegységekkel. Ez a
fejezet **teljes** legyen: ha nagy, gépi melléklet (`adatok/*.csv`) tartozik hozzá, és a
fejezet a szerkezetét meg a kiválasztott részhalmazt magyarázza.

### 4. Adatmodell
A DUT-ok (`ST_`, `E_`) mezőszinten. Minden mért mennyiséghez érvényességi jelző
(lásd az `st-nevkonvenciok` skill 3. pontját).

### 5. Működés
Állapotgép (ábrával), ütemezés, lekérdezési ciklusok. Melyik művelet mikor és milyen
gyakran fut.

### 6. Hibakezelés és diagnosztika
Az „soha ne fagyjon be néma, elavult érték" elv szerint: érvénytelenítés, `xOnline`
feltétele, újrapróbálkozás és visszalépés (backoff), hibaszámlálók, a gyártói
„érvénytelen" minták kezelése **a nyers adaton, konverzió előtt**.

### 7. Publikált interfész
Pontosan mit tesz a GVL-be, milyen néven, milyen mértékegységben. **Ez a szerződés a
funkcióréteg felé** — ez az a fejezet, amit egy másik komponens tervezője elolvas.

### 8. Konfigurációs paraméterek
Minden hangolható szám egy táblában: érték, mértékegység, forrás (**[GY]** / **[D]**), és
hogy a kódban konstans-e vagy futásidőben állítható.

### 9. Üzembehelyezési ellenőrzés
Számozott, végrehajtható lépéssor. Minden lépésnél: mit csinálunk, mit várunk, és mit
jelent, ha nem az jön ki. Tartalmazza a **hibainjektálást** is (kábel kihúzása), nem csak
a boldog utat.

### 10. Nyitott kérdések
Számozott `[?]` tételek. Mindegyik megnevezi, **honnan zárható le**: melyik hiányzó
dokumentumból, vagy melyik üzembehelyezési mérésből. Kockázati szint jelöléssel:
🔴 blokkoló · 🟠 jelentős · 🟡 mérsékelt · ⚪ dokumentációs · ✅ lezárva.

### 11. Forráshivatkozások
Minden felhasznált gyártói dokumentum azonosítóval és oldalszámmal, minden CODESYS
könyvtár verziószámmal. Egy **[GY]** állítás forrás nélkül nem **[GY]**.

## Amit a sablon tilt

- **Kód a tervben.** Típusdefiníció, állapotnév, regiszterszám igen; ST törzs nem.
  A terv attól terv, hogy a megvalósítás még nyitott.
- **Forrás nélküli szám.** Minden érték vagy gyártói adat (**[GY]**, hivatkozással),
  vagy döntés (**[D]**, indoklással), vagy számítás (a levezetéssel).
- **Elhallgatott bizonytalanság.** Ha egy adat nem volt kiolvasható, az `[?]` — nem
  „valószínűleg".

## Elhelyezés a projektben

A komponenstervek egy **katalógust** alkotnak, nem olvasási sorrendet: a
`komponensek/` mappa alatt **nincs sorszám**, csak beszélő slug (`multical603.md`,
`mbrtu-csatorna.md`). Így új komponens felvétele nem jár átszámozással; a sorrend a
`komponensek/README.md` táblájában él. A narratív, gyökérszintű fejezetek viszont
sorszámozottak (`00-architektura.md`, `01-nevkonvenciok.md`).
