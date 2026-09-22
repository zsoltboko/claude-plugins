# claude-plugins — saját Claude Code marketplace

Ez a repó egyszerre **marketplace** és a benne lakó pluginok forrása. A Claude Code
innen telepíti őket, a projektek pedig verzióval hivatkoznak rá.

Marketplace neve: **`zsoltboko`** (ezt írja ki a Claude Code a telepítésnél, és ez a
`@` utáni rész a plugin azonosítójában).

## Pluginok

| Plugin | Mi | Verzió |
|---|---|---|
| [`hvac-plc`](plugins/hvac-plc/) | Közös ST konvenció, komponensterv-sablon és konzultációs output style CODESYS alapú hőközpont/HVAC projektekhez | 0.2.0 |

## Telepítés

```
/plugin marketplace add zsoltboko/claude-plugins
/plugin install hvac-plc@zsoltboko
```

Ezután **indítsd újra a Claude Code-ot** — a skilleket és az output style-okat
induláskor olvassa be.

Frissítés később:

```
/plugin marketplace update zsoltboko
```

## Automatikus elérhetőség egy projektben

Ha azt szeretnéd, hogy egy projekt klónozása után a plugin magától elérhető legyen,
vedd fel a projekt `.claude/settings.json`-jébe:

```json
{
  "extraKnownMarketplaces": {
    "zsoltboko": {
      "source": { "source": "github", "repo": "zsoltboko/claude-plugins" }
    }
  },
  "enabledPlugins": {
    "hvac-plc@zsoltboko": true
  }
}
```

## Szerkesztés

Ez a repó a **mérvadó példány**. Ha egy konvención vagy sablonon változtatsz, itt
változtass, emeld a verziót a `plugins/<név>/.claude-plugin/plugin.json`-ban **és** a
`.claude-plugin/marketplace.json`-ban, majd tagelj:

```
git tag hvac-plc-v0.3.0
```

A projektekben talált eltérés vagy hiba javítása **ide** jön vissza, nem fordítva.

## Szerkezet

```
.claude-plugin/marketplace.json      a marketplace leírója (repó gyökér)
plugins/<név>/
  .claude-plugin/plugin.json         a plugin leírója
  skills/<név>/SKILL.md              skillek
  output-styles/<név>.md             output style-ok
  agents/  commands/  hooks/         (ha lesz)
```

## Kapcsolódó

A CODESYS library projekt **nem** itt van, hanem a `hvac-lib` repóban. A kettő
párhuzamos: az egyik a kód újrahasznosításáról szól, a másik a munkamódszeréről.
