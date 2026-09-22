---
name: st-nevkonvenciok
description: Structured Text elnevezési és kódolási konvenció CODESYS/IEC 61131-3 projektekhez. Használd, amikor ST kódot írsz, POU-t, DUT-ot vagy GVL-t nevezel el, változót deklarálsz, állapotgépet tervezel, vagy meglévő ST kódot vizsgálsz felül konvenció szempontjából.
---

# ST elnevezési és kódolási konvenció

Ez a konvenció **minden hőközpont/HVAC PLC projektre** érvényes (E13, R48, és a
későbbiek). A projektspecifikus kivételeket az adott projekt `CLAUDE.md`-je vagy
szoftvertervi mappája rögzíti — ha ilyet találsz, az felülírja az itt leírtakat.

## 1. Objektumok (POU, DUT, GVL)

| Előtag | Mi | Példa |
|---|---|---|
| `PRG_` | Program | `PRG_MbRtu` |
| `FB_` | Függvényblokk | `FB_Multical603` |
| `FUN_` | Függvény | `FUN_ScaleUnit` |
| `GVL_` | Globális változólista | `GVL_Heat`, `GVL_KNX` |
| `ST_` | Struktúra (DUT) | `ST_MC603_Meas` |
| `E_` | Felsorolás (DUT) | `E_MC603_State` |
| `ITF_` | Interfész | `ITF_MbDevice` |
| `TSK_` | Taszk | `TSK_Comm` |

**Kivétel:** a CODESYS alapértelmezett `PLC_PRG` neve marad, ha a projektben már létezik
és a taszkhozzárendelés rá épül. Ne nevezd át.

**Nyelv:** az objektumnév **angolul vagy nemzetközi rövidítéssel** íródik (`Multical603`,
`MbRtu`, `Heat`) — a CODESYS-en belüli kereshetőség és a gyártói könyvtárak nevei is
ilyenek. **A kommentek magyarul**, mert azokat ember olvassa.

## 2. Változó-előtagok

| Előtag | Típus | Előtag | Típus |
|---|---|---|---|
| `x` | `BOOL` | `r` | `REAL` |
| `by` | `BYTE` | `lr` | `LREAL` |
| `w` | `WORD` | `t` | `TIME` |
| `dw` | `DWORD` | `dt` | `DATE_AND_TIME` |
| `usi` / `si` | `USINT` / `SINT` | `s` | `STRING` |
| `ui` / `i` | `UINT` / `INT` | `a` | tömb (a típusjel elé: `awData`) |
| `udi` / `di` | `UDINT` / `DINT` | `fb` | függvényblokk-példány |

**Hatókör-jelölés:**

| Jelölés | Hol | Példa |
|---|---|---|
| `m_` | függvényblokk belső (`VAR`) tagváltozó | `m_uiRetryCnt` |
| `c` | konstans (`VAR CONSTANT`) | `cuiPollCycleMs` |
| előtag nélkül | `VAR_INPUT`, `VAR_OUTPUT`, `VAR_IN_OUT`, GVL | `xEnable`, `rEnergyHeat` |

**Miért `m_`:** egy FB törzsében ránézésre el kell tudni dönteni, hogy egy változó a
külvilág felé látszik-e. Az `m_` nélküli név bemenetet vagy kimenetet jelent — tehát
szerződést, amit nem lehet szabadon átnevezni.

## 3. Struktúramezők

A mezők **nem** ismétlik meg a struktúra nevét:

```
// jó
ST_MC603_Meas.rEnergyHeat
// rossz
ST_MC603_Meas.rMC603EnergyHeat
```

**Érvényességi jelző minden mért mennyiséghez**, a mező neve után `Valid` utótaggal.
Ez nem stílus, hanem a hibakezelési elv (lásd lent) következménye:

```
rEnergyHeat       : REAL;   // E1 fűtési energia [kWh]
xEnergyHeatValid  : BOOL;   // TRUE, ha friss és a mérő érvényesnek jelölte
```

**Mértékegység a kommentben, szögletes zárójelben** — minden fizikai mennyiségnél,
kivétel nélkül. Ugyanazt az energiát egy mérő kWh-ban és MWh-ban is adhatja a
konfigurációjától függően; a mértékegység nélküli szám félrevezető.

## 4. Állapotgépek

Állapot-felsorolás `E_<Komponens>_State` néven, a tagok **nagybetűs, alulvonásos**
angol nevek — így vizuálisan elkülönülnek a változóktól:

```
TYPE E_MC603_State : (INIT := 0, IDENT := 1, RUN := 2, BACKOFF := 3);
```

Az állapotváltozó neve `m_eState`. **Minden állapotátmenetnek van kommentje**, ami az
átmenet *feltételét* mondja ki — nem azt, hogy „átlépünk RUN-ba".

## 5. Névtérhasználat

A gyártói könyvtárak típusait **mindig teljes névtérrel** hivatkozd:

```
m_fbMaster : WagoAppPlcModbus.FbMbMasterSerial;
m_eFrame   : WagoAppPlcModbus.eMbFrameType := WagoAppPlcModbus.eMbFrameType.RTU;
```

**Miért:** ezekben a projektekben több tucat könyvtár van referálva, átfedő
fogalomkészlettel — `FbSerialInterface` például két WAGO könyvtárban is létezik
(`WagoAppCom` és `WagoSysSerial`). A rövidítés itt kifejezetten hibaforrás.

## 6. Formázás

- Behúzás: **4 szóköz**, nem tabulátor
- Egy sor egy utasítás
- Függvényblokk-hívás paraméterei **soronként egy**, ha kettőnél több van
- Minden POU fejlécében rövid magyar kommentblokk: **mit csinál**, **melyik taszkból kell
  hívni**, **melyik tervdokumentum írja le**:

```
(* FB_Multical603 -- Kamstrup MULTICAL 603 hőmennyiségmérő Modbus RTU illesztő.
   Hívás: PRG_MbRtu-ból, ciklikusan (TSK_Comm, 20 ms).
   Terv:  05. Szoftverterv/komponensek/multical603.md *)
```

## 7. A két architekturális szabály, amit a kód nem sérthet meg

Ezek nem elnevezési kérdések, de minden ST sor rájuk épül:

1. **Az illesztőréteg soha nem szabályoz**, a funkcióréteg soha nem lát protokollt.
   Egy protokoll-illesztő beolvas, konvertál, érvényesít és publikál — de nem dönt.
   Regiszterszám, csoportcím, slave-azonosító nem kerülhet a funkciórétegbe.
2. **Soha ne fagyjon be néma, elavult érték.** Minden publikált mért mennyiséghez
   érvényességi jelző tartozik, minden eszközhöz `xOnline`, és kommunikációkiesésnél az
   értékek **érvénytelenné válnak** — nem maradnak az utolsó jó értéken. Egy befagyott
   45 °C-os előremenő hőmérséklet veszélyesebb, mint egy hiányzó.

Rétegek közti adatcsere **kizárólag GVL-en keresztül, egyirányúan**. Egy GVL-mezőnek
pontosan egy írója van. Illesztő és funkcióréteg között nincs közvetlen függvényhívás.
