# Artifexing CRM — állapotmentés (handoff)

**Utolsó frissítés:** 2026-06-25
**Állapot:** ÉLES, működik. Többfelhasználós, felhő-szinkronnal.

---

## 1. Infrastruktúra — minden hozzáférés egy helyen

| Szolgáltatás | Részlet |
|---|---|
| **Élő URL** | https://artifexingcrm.vercel.app |
| **GitHub repó** | github.com/XylOhmPon/ArtifexingCRM (push: **XylOhmPon** fiók!) |
| **Vercel projekt** | `artifexing_crm` (csapat: "XylOhmPon's projects") |
| **Supabase projekt** | `kwljyfcbboorghjjpkqs` — "Artifexing CRM", eu-west-1, **INGYENES csomag** |
| **Supabase URL** | https://kwljyfcbboorghjjpkqs.supabase.co |
| **Supabase kulcs** | `sb_publishable_pEtxqvcN3bC48G-o5j7GGg_z6aMBunl` (publishable, a kódban) |

A Supabase URL + kulcs a `index.html`-ben a `TENANT_CONFIG`-ban (~1119-1120. sor).

### Supabase adatbázis
- **Tábla:** `crm_sync` — egyetlen sor (`id=1`), `data` jsonb tartalmazza az egész DB-t.
  RLS bekapcsolva: `authenticated` szerepkör olvas/ír.
- **Auth:** 3 felhasználó, mind email-megerősített:
  `andras.benxe@gmail.hu`, `palyazatmn87@gmail.com`, `ks.energetika@gmail.com`
- Új user: Supabase → Authentication → Add user → **Create new user** (auto-confirmed).

---

## 2. Architektúra dióhéjban
- **Egyetlen fájl:** `index.html`, nincs build, nincs framework. Vanilla JS/HTML/CSS.
- **CDN függőségek:** jsPDF 2.5.1, SheetJS (xlsx) 0.18.5, supabase-js@2, Geist font.
- **Adatmodell:** globális `DB` objektum (clients, projects, quotes, invoices, documents,
  worksheets, garantia, messages, calEvents, inventory, settings...). `saveDB()`/`loadDB()`.
- **Mentés logika:** minden változás → localStorage azonnal + Supabase debounce 600ms
  (`_sbScheduleSave`). Fülbezáráskor azonnali flush (`_sbFlushSync`, keepalive fetch).
- **Routing:** `nav(screen,id)` → `renderScreen()` → `render*()` függvények.
- **Branding:** `TENANT_CONFIG` (companyName, accent `#2980C9`, beágyazott logó SVG).

---

## 3. Kész funkciók
- Ügyfelek, Projektek (típus: Klímaszerelés/Hőszivattyú/Épületgépészet), Árajánlatok (PDF),
  Dokumentumok, Munkalapok (PDF), Számlák (ki+bejövő), **Garancia**, Statisztika, Üzenetek.
- **Többfelhasználós belépés** (Supabase Auth), felhő-szinkron, fül-fókusz auto-sync.
- **Avatar menü** (jobb fent): megjelenített név beállítása (egyszer mentve csak Módosítás
  gombbal írható át) + Kijelentkezés. A név Supabase `user_metadata.display_name`-ben.
- **Belső üzenetek:** mutatják a küldő nevét + pontos időbélyeget (visszakövethető).
- Naptár kézi események, TODO projektenként, kommunikációs napló ügyfeleknél.
- Excel export (SheetJS), mobil reszponzív (900/600px + alsó nav), favicon a logó SVG-ből.
- Tab cím: "Artifexing CRM".

## Elrejtve / későbbi fázis (szándékosan)
- **Készlet** és **Alvállalkozók** menü `display:none` (még nem használják).
- Szerepkör-alapú jogosultság: jelenleg mindenki mindent lát (2–5 fős csapat).

---

## 4. Ismert korlát / vigyázat
- **Egyetlen közös `crm_sync` rekord** ("last write wins"): ha KETTEN pontosan egyszerre
  mentenek, az utolsó felülírhatja a másikat. A fül-fókusz sync nagyrészt kezeli; valódi
  gyakori egyidejű szerkesztésnél később táblánkénti mentésre lehet váltani.
- **Ingyenes Supabase:** a keep-alive workflow védi a felfüggesztéstől. Ha az ügyfél
  garantált rendelkezésre állást/napi backupot vár → Pro csomag (25 USD/hó).
- Adatmentés: a kódból a "Beállítások → Biztonsági mentés (JSON)" gomb tölt le mindent.

---

## 5. Folytatás — hol tartottunk
A legutóbbi munka a **GitHub/Vercel deploy + többfelhasználós Supabase auth** beállítása
és tisztázása volt. Lezárt feladatok ebben a körben:
- ✅ Login újra működik (a felfüggesztett Supabase projekt visszaállítva → ACTIVE_HEALTHY)
- ✅ Keep-alive workflow (`.github/workflows/keepalive.yml`) — naponta 2× ping
- ✅ Megjelenített név + üzenet időbélyeg + avatar-menüs kijelentkezés
- 🔲 (felhasználói teendő volt) a duplikált `artifexing-crm` Vercel-projekt törlése

### Lehetséges következő lépések (ha felmerül)
- Az `artifexingcrm.vercel.app` rákötése egyedi domainre (pl. `crm.artifexing.hu`) —
  a Rackhost cPanel DNS-ében egy CNAME a `cname.vercel-dns.com`-ra.
- Készlet modul aktiválása, ha a raktárkezelés napirendre kerül.
- Táblánkénti mentés, ha az egyidejű szerkesztés gond lesz.
