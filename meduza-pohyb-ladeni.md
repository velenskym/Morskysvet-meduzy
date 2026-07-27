# Ladicí parametry — meduza-pohyb-kroky.html

Interní poznámka pro budoucí doladění animace pohybu medúzy (Opus Magnum T4).
Netýká se žádného jiného souboru — vše platí jen pro `meduza-pohyb-kroky.html`.

Jak upravovat: otevřít soubor v textovém editoru, změnit číslo u dané konstanty,
uložit, obnovit stránku v prohlížeči. Měnit jednu věc najednou a hned zkontrolovat efekt.

**Cache upozornění:** animace běží v iframe (`<iframe src="meduza-pohyb-kroky.html?embed=1&v=2">`
v `jednoduchost.html`). Po každé úpravě tohoto souboru zvyš `v=` o jedna v `jednoduchost.html`,
jinak se úprava nemusí kvůli cache projevit ani po Ctrl+F5.

---

## Hlavní ladicí konstanty

| Parametr | Řádek | Teď | Co dělá | Efekt při zvýšení / snížení |
|---|---|---|---|---|
| `FLEX` | ~197 | `1.0` | Síla ohebnosti okraje zvonu při stahu | ↑ zvon se víc prohýbá/vlní · ↓ tužší, strojovější pohyb |
| `WAKE_LIFE` | ~141 | `8.0` (s) | Jak dlouho žije odtržený hnací vír v brázdě, než zeslábne | ↑ vír viditelný déle po odplavání · ↓ zmizí rychleji |
| `WAKE_DRIFT` | ~141 | `26` (px/s) | Rychlost, jakou se odtržený vír sune dozadu do brázdy | ↑ vír uhání pryč rychleji · ↓ zůstává blíž medúze |
| `TRAVEL.stroke` | ~152 | `44` | Jak daleko medúza popojede vpřed během hlavního stahu | ↑ delší/razantnější záběr dopředu |
| `TRAVEL.recap` | ~152 | `40` | Jak daleko popojede během rekuperace (druhý, "zdarma" impulz) | ↑ rekuperace přispívá k pohybu víc |
| `TRAVEL.coast` | ~152 | `8` | Setrvačný dokluz mezi stahem a rekuperací | ↑ déle "pluje" bez aktivního popohnání |

## Vizuální reprezentace víru (`spiral()`, ~řádek 371)

Od 27. 7. 2026 nakreslená jako **tři soustředné přerušované oblouky rotující
společně + šipka na vedoucí hraně** (dřív jednoduchá spirála). Platí pro
všechny tři víry, co tuhle funkci volají — hnací, v brázdě, brzdný.

| Parametr | Řádek | Teď | Co dělá |
|---|---|---|---|
| `view.clock*2.1*sign` (rotace) | ~372 | `2.1` | Rychlost otáčení ikonky víru (rad/s). ↑ = rychlejší rotace. Nezměněno od začátku. |
| `gap` | ~373 | `1.15` (rad) | Velikost mezery v obloucích. ↓ = delší/uzavřenější oblouky, ↑ = kratší oblouky/víc mezery |
| `rings` (pole) | ~376 | 3 položky | Poloměr (podíl z `R`), tloušťka, krytí každého ze 3 prstenů — vnější je teď nejvýraznější (`a:1.00`), střední `0.65`, vnitřní `0.38` |

**Oprava z 27. 7. 2026:** zrcadlové páry vírů (např. hnací vlevo/vpravo) měly
nesymetrický vzhled — jeden správně dlouhý oblouk/krátká mezera, druhý obráceně.
Příčina: koncový úhel oblouku (`end`) se počítal stejně pro obě znaménka rotace,
ale `ctx.arc()` s opačným směrem (`anticlockwise`) k tomu samému úhlu doputuje
*opačnou* cestou — a ta vyjde přesně na velikost mezery místo oblouku. Opraveno
tak, že `end` teď počítá se znaménkem (`rot + sign*(2π−gap)`) — ověřeno numericky,
oba směry teď zametají identický úhel.

## Proud (částice) — bez stop, jen tečky

Od 27. 7. 2026 každá částice kreslí **jen tečku na aktuální pozici**, žádnou
čáru/stopu za sebou — plátno se každý snímek vždy plně vyčistí (žádné blednutí,
žádná akumulace). Tlačítko „Proudění" je pořád funkční, jen bez trail efektu.

| Co hledat | Řádek | Teď | Co dělá |
|---|---|---|---|
| `const NP=950` | ~337 | `950` | Počet částic v proudu — víc = hustší/čitelnější proud, ale náročnější na výkon |
| `PARTICLE_SPEED` | ~338 | `1.5` | Násobič rychlosti unášení částic (1.0 = původní tempo) |
| `spawn()` — min. poloměr | ~341 | `20*S` | Odkud nejblíž zvonu částice smí spawnout. **Neměnit zpátky nad ~140×S** — tam žijí víry (brzdný 43–76, hnací 77–137), a při vysokém minimu tam nikdy nejsou žádné částice |
| `spawn()` — rozložení | ~341 | `sqrt(random())` | Rovnoměrné pokrytí *plochy* (ne jen poloměru) — bez sqrt by byla hustota blíž středu uměle vyšší |
| `ctx.arc(nx,ny,1.3*S,...)` | ~468 | `1.3` | Poloměr tečky částice (dřív tloušťka čáry) |

**Oprava z 27. 7. 2026 — díra kolem zvonu:** minimální poloměr spawnu byl `120×S`,
ale brzdný vír žije na 43–76×S a hnací na 77–137×S — celá tahle zóna byla bez
částic natrvalo. Opraveno snížením minima na `20×S` + přechodem na
plocho-rovnoměrné rozložení.

## Bezpečnostní pojistka `dt`

Řádek ~417: `dt=Math.max(dt, 1/240);` — na slabším hardwaru umí
`requestAnimationFrame` občas vypálit snímky se stejným časovým razítkem
(`dt≈0`), což by na okamžik zastavilo pohyb. Tahle pojistka to nedovolí.
Nemá vliv na normální běh (běžné `dt` při 24–60 FPS je vždy výrazně nad 1/240).

---

## Historie oprav

**23. 7. 2026 — stopy částic "nemizely" (starý trail systém, dnes už neplatí):**
Bug byl ve dvou vrstvách: (1) čára se kreslila od pozice o dva snímky zpátky
místo o jeden, dělalo to 2× delší čárky; (2) blednutí pozadí bylo vázané na
počet snímků, ne na reálný čas, takže na pomalejším HW (kiosek) blednutí
prakticky neproběhlo. **Tohle je teď celé bezpředmětné** — 27. 7. byl celý
trail/blednutí mechanismus nahrazen jednoduchými tečkami bez stopy (viz výš),
takže žádné blednutí už neexistuje a nemůže se rozbít.

**27. 7. 2026 — díra kolem zvonu + asymetrie vírů:** viz sekce výš.
