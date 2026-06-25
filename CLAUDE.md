# Artifexing CRM — projekt kontextus

> Ez a fájl minden session indításakor automatikusan betöltődik. Részletes állapot: **HANDOFF.md**.

## Mi ez
Egyfájlos böngészős CRM az **Artifexing** (klímaszerelés · hőszivattyú · épületgépészet) cégnek.
A teljes alkalmazás egyetlen fájl: **`index.html`** (~5300 sor, vanilla HTML/CSS/JS).
Adattárolás: böngésző `localStorage` + **Supabase** felhő-szinkron (többfelhasználós).

## Hol mi van
- **Forráskód:** `index.html` (EZT szerkeszd közvetlenül — nincs build lépés)
- **Git repo:** github.com/XylOhmPon/ArtifexingCRM
- **Élő URL:** https://artifexingcrm.vercel.app (Vercel projekt: `artifexing_crm`)
- **Supabase projekt:** `kwljyfcbboorghjjpkqs` ("Artifexing CRM", eu-west-1)

## Munkafolyamat (deploy)
1. Szerkeszd az `index.html`-t
2. `git add index.html && git commit -m "..."`
3. `git push` → a Vercel ~1 perc alatt automatikusan deployol

## KRITIKUS tudnivalók
- **GitHub push:** a repó az **XylOhmPon** fiók alatt van, NEM rehszilveszter-max alatt.
  Ha a push 403-at ad: `gh auth switch --user XylOhmPon` majd `git push`.
- **NE generálj előnézetet a chatben** — a felhasználó mindent Chrome-ban néz/ellenőriz.
- **JS szintaxis ellenőrzés** szerkesztés után:
  `node -e "new Function(require('fs').readFileSync('index.html','utf8').match(/<script>([\s\S]*?)<\/script>/)[1]); console.log('OK')"`
- **Supabase INGYENES csomag** → ~1 hét tétlenség után felfüggesztődne, de a
  `.github/workflows/keepalive.yml` naponta 2× pingeli, így életben marad. Ha mégis
  "INACTIVE" lenne és nem megy a login: a Supabase a hibás, nem a kód (restore kell).

## Figyelem — mappa-zavar
- Ez a CRM = `Artifexing_Deploy/`. **Innen dolgozz.**
- A `/5.Clauded/artifexing/` egy KÜLÖN Next.js projekt (a weboldal) — ne keverd.
- A `/5.Clauded/KS-Energetika_Vallalatiranyitasi_rendszer/Artifexing_CRM.html` a régi
  munkapéldány, jelenleg azonos ezzel — de a forrás mostantól ez az `index.html`.
