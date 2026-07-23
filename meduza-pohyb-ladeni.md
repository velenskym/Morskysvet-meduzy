# Ladicí parametry — meduza-pohyb-kroky.html

Interní poznámka pro budoucí doladění animace pohybu medúzy (Opus Magnum T4).
Netýká se žádného jiného souboru — vše platí jen pro `meduza-pohyb-kroky.html`.

Jak upravovat: otevřít soubor v textovém editoru, změnit číslo u dané konstanty,
uložit, obnovit stránku v prohlížeči. Měnit jednu věc najednou a hned zkontrolovat efekt.

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

## Vzhled proudu (částic)

Nejsou to pojmenované konstanty, ale snadno se najdou a upraví přímo v čísle.

| Co hledat | Řádek | Teď | Co dělá |
|---|---|---|---|
| `const NP=950` | ~337 | `950` | Počet částic v proudu — víc = hustší/čitelnější proud, ale náročnější na výkon |
| `rgba(0,26,46,0.20)` (fillRect pozadí) | ~446 | `0.20` | Rychlost blednutí stop — ↑ kratší/ostřejší stopy · ↓ dlouhé "light trail" ocásky |
| `lineWidth=1.4*S` | ~456 | `1.4` | Tloušťka čárek proudu |

---

## Oprava z 23. 7. 2026 — stopy částic

Stopy proudu (vír + brázda) měly bug: čára se kreslila od pozice o **dva** snímky
zpátky místo o **jeden**. Důsledek: každá čárka byla ~2× delší, než měla být,
a v ohbí kolem víru rovná čára "řezala rohy" místo aby sledovala křivku —
odtud ten trhaný/rozbitý dojem.

Opraveno v `frame()` (~řádek 457): čára se teď kreslí přímo z právě přečtené
pozice `x,y`, ne ze zastaralého slotu v poli částic. Ověřeno přímo v běžící
simulaci — staré úsečky byly systematicky přesně 2× delší než nové.
