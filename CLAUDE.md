# claude-plugins — munkakontextus

Ez a repó a saját Claude Code marketplace-em, `zsoltboko` néven, és egyben a benne
lakó pluginok forrása. Részletek a [README.md](README.md)-ben.

## Amit tudni kell munka előtt

- A repó gyökerében lévő `.claude-plugin/marketplace.json` a marketplace leírója.
  Minden plugin a `plugins/<név>/` alatt él, saját `.claude-plugin/plugin.json`-nal.
- A skillek `plugins/<név>/skills/<skill-név>/SKILL.md` alatt vannak, YAML
  frontmatterrel (`name`, `description`), az output style-ok
  `plugins/<név>/output-styles/<név>.md` alatt (`name`, `description`,
  `keep-coding-instructions`).
- **Ez a mérvadó példány.** Ha egy projektben eltérést vagy hibát találsz a
  konvencióban, a javítás ide jön vissza, nem a projektben marad.

## Verziózás

Minden érdemi változtatásnál emelni kell a verziót **két helyen**, azonos értékre:

1. `plugins/<név>/.claude-plugin/plugin.json` → `version`
2. `.claude-plugin/marketplace.json` → a plugin bejegyzésének `version` mezője

A kettő elcsúszása csendes hiba: a telepítő a marketplace-ben lévőt hiszi el.
Kiadás után git tag, `<plugin>-v<verzió>` alakban.

## Nyelv

A pluginok tartalma **magyar** (a skillek és az output style-ok szövege), a
technikai azonosítók (plugin- és skillnevek, mappanevek) **angol vagy ékezet
nélküli** alakban.

## Amihez ne nyúlj

A `README.md` telepítési parancsai konkrét neveket tartalmaznak (`zsoltboko`,
`consult`, `hvac-plc`). Ha átnevezel valamit, a README-t is javítsd, különben a leírt parancsok
nem működnek.
