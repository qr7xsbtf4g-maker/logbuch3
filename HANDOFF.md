# HANDOFF — Dashboard DJI

## Aktueller Stand

**Datei:** `/Users/djoker/Claude/Claude/Projects/Projekt Dashboard CC/index.html`  
**Zeilen:** ~3856  
**Git:** Ja — remote auf GitHub, GitHub Pages aktiv; aktuell lokale Änderungen in `index.html`, `AGENTS.md` untracked  
**Branch:** `main`  
**Syntaxcheck:** `node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')`

---

## GitHub / Deployment

- **Repo:** https://github.com/qr7xsbtf4g-maker/logbuch3
- **GitHub Pages:** https://qr7xsbtf4g-maker.github.io/logbuch3/
- **Remote URL enthält PAT** (in `.git/config`) — Token in `.git/config` gespeichert, nicht hier dokumentieren
- Push: `git push origin main`
- GitHub Pages baut automatisch nach jedem Push (~1–2 min)

---

## Was in der letzten Session erledigt wurde (2. Juni 2026)

### Aktuelle Session — Produktiver Dashboard-Ausbau
- **Daily Cockpit wieder entfernt:** Die Sektion **„Heute wichtig“** war für Dustin
  nicht hilfreich und wurde vollständig entfernt: HTML-Block, CSS-Klassen
  `.daily-cockpit`/`.cockpit-*`, JS-Funktionen `getDailyInsights`/`renderDailyCockpit`
  und alle Update-Aufrufe. Syntaxcheck danach erfolgreich.
- **Tagesimpulse besser markiert:** Der Tages-Impuls hat jetzt sichtbare Quellen-/Typ-Tags
  (`Evidenz`, `Anekdotisch`, `Experiment`, `Warnung`) via `.source-chip`.
  Anekdotische Inputs bleiben erlaubt, werden aber nicht als medizinische Fakten dargestellt.
- **Daily-Takeaways erweitert:** Einige anekdotisch markierte Impulse aus Dustins
  bevorzugten Richtungen ergänzt; medizinische/Supplement-Änderungen weiterhin nur
  nach separatem Stack-Check.
- **Export/Import robuster:** Export enthält Metadaten (`version`, `exportedAt`, Counts,
  Hinweis); Import zeigt vor dem Einspielen eine Zusammenfassung und behandelt Fotos
  separat, damit Speicherprobleme nicht den kompletten Import verhindern.
- **Keine neuen Dateien, kein Framework, kein Build-System.** Alles bleibt in `index.html`.

### Aktueller iOS/PWA-Stand
- `apple-mobile-web-app-status-bar-style` steht aktuell auf **`default`**.
- Grund: Das war auf dem iPhone die stabile Variante, bei der die untere Tab-Bar bündig
  und ohne springenden Phantom-Abstand bleibt.
- Wichtig: iOS liest diesen Meta-Wert nur beim Hinzufügen zur Home-App. Für den finalen
  Test auf dem iPhone nach Push/Deploy ggf. Home-App entfernen und neu hinzufügen.
  **Vorher immer Export machen**, weil dabei `localStorage` verloren gehen kann.
- Keine `black-translucent`-/`screen.height`-/Fixed-Position-Hacks mehr einbauen, solange
  nicht eine neue, sauber getestete Idee vorliegt.

### Kritischer Daten-Fix + Recovery
- **Trainings-Entwurf (Crash-Schutz):** Eingaben mitten im Training (Sätze/Wdh/kg)
  überleben jetzt App-Schließen — Live-Draft in `localStorage['lb_train_draft']`
  (`saveTrainDraft`/`restoreTrainDraft`, Hooks in onSetInput/addSet/selArm/visibilitychange).
  Speichern-Button bleibt der finale Save; Draft nur Absturzschutz.
- **Import repariert:** robuster (leere Datei/iCloud-Platzhalter abfangen, Fotos
  isoliert, echte Fehlermeldung) + **`hp`-Crash in `renderCIH` behoben** (undefinierte
  Variable brach Import+Check-in-Verlauf ab) → `_ps.front`.
- **Recovery:** Dustins iCloud-Backup `dashboard_2026-05-31.json` war valide; nach
  iCloud-Download + Import alle Daten zurück. (Re-Add für Statusleiste hatte localStorage
  geleert — Lehre: vor Re-Add IMMER exportieren.)

### Diese Runde (T1–T3)
- **T1 — JoyFit-Nachtrag:** OK-Einheit vom **01.06.** (JoyFitness Lübeck, 7 Übungen,
  Vol 3194) als **additive** Import-Datei `joyfit_2026-06-01.json` in Dustins
  iCloud-Ordner (`…/BPOL/Aufstieg/HS III/`) erstellt. Merge-getestet: überschreibt nichts.
- **T2 — Zone-2-Chart** im Check-In von Abschnitt *Wohlbefinden* → *Training* verschoben.
- **T3 — Session-Timer:** Auto-Start bei erster komplett eingetragener Übung, sichtbar
  im Training-Tab, Stop beim Speichern → `startTime`/`endTime`/`dauer`(min) im Eintrag
  (renderTH zeigt es). Übersteht App-Schließen (sessionStart im Draft).

### OFFEN (Reihenfolge)
1. **R3 Slides → App** (Dustin liefert Trainings-/Schlaf-PNGs): Tages-Impuls/Wissens-Tipps
   + Training-Logik + Schlaf-Empfehlungen. (Supplement-Bezug → erst `/supplement-stack-check`.)
2. **Phase B:** F0 Apple-Health-Sync, F1 Insight-Loop, F2 Top-3+Wenn-Dann, F3 Deep-Work-Timer,
   F4 Koffein-Cutoff (Details im Planfile).

### Status-Bar-Stand
Aktuell gilt die neuere Entscheidung aus dieser Session: **`default` behalten**, weil die
untere Tab-Bar dadurch stabil und bündig bleibt. Ältere `black-translucent`-Notizen unten
sind historische Tests, nicht der aktuelle Zielzustand.

---

## Was davor erledigt wurde (1. Juni 2026)

### Status-Bar-Saga (oben creme) — iOS-Grenze erreicht

**Ziel:** obere iOS-Statusleiste in App-Farbe (creme) statt schwarz.

**Erkenntnis (recherchiert + am Gerät getestet):** Dustins iPhone ist im **Dark Mode**.
- `default`/`black` → Statusleiste im Dark Mode IMMER schwarz (`color-scheme:light` hilft nicht).
- `black-translucent` → einzige Option für creme Top (Inhalt scheint hinter der
  transparenten Leiste durch). Schrift bleibt weiß (iOS-fest, auf creme etwas blass).

**Historischer Zwischenstand, inzwischen überholt:** Damals war
`apple-mobile-web-app-status-bar-style: black-translucent` aktiv
(+ `color-scheme:light`, theme-color creme für hell & dunkel), um oben creme zu halten.
Aktuell gilt aber wieder **`default`**, weil Dustin die untere stabile Tab-Bar priorisiert.

**Ungelöst / iOS-Grenze:** black-translucent meldet `innerHeight`/`100dvh` = **812**,
echter Screen = **874** (iPhone 16 Pro). Die 62px Differenz = creme Zone unten.
- `fitApp()` (app-frame = screen.height) füllt sie, schneidet aber die Tab-Labels ab → **entfernt**.
- Ohne fitApp: Tabs sichtbar, aber ~62px creme Zone unter der Bar (Home-Indikator-Bereich).
- **Fazit aus dieser Testreihe:** Creme oben UND bündig unten gleichzeitig war mit
  `black-translucent` im Dark Mode nicht stabil erreichbar. Keine weiteren
  fitApp-/black-translucent-Experimente ohne neue Idee.

**WICHTIG:** `status-bar-style` wirkt nur nach **Entfernen + Neu-Hinzufügen** der Home-App.
Dabei **löscht iOS den localStorage** → Daten gingen verloren (Streak war auf 1).
→ Vor jedem Re-Add **Check-In → Export**, danach Import. Cache-Test via Safari `?v=N`.

### Check-In-Tab entrümpelt (R2)

WEEKLY-View (`index.html:~893`) war 9 lose Karten. Jetzt **4 Abschnitte mit Überschriften**
(`.sh`) in Dustins Prioritäts-Reihenfolge:
- **Körper** → Gewichtsverlauf (offen by default), Wochenvergleich
- **Wohlbefinden** → Schlaf & Energie, Zone 2, Wochenschnitt Daily
- **Training** → Trainingsqualität, Muskelgruppen
- **Wöchentliche Eingabe** → Check-in, Verlauf, Daten

Reine HTML-Umsortierung, alle Charts/Funktionen unverändert. Oben weiter Quick-Score + Wochenschnitt.

### OFFEN — nächste Schritte (Reihenfolge)

1. **Check-In R2 verfeinern** (optional): Top-Übersicht prägnanter, wichtigste Körper-Zahlen
   (Gewicht-Diff, KFA-Trend) ohne Aufklappen sichtbar? Mehr standardmäßig offen? → Dustin fragen.
2. **R3 Slides → App:** Dustin liefert Trainings-/Schlaf-Folien als PNG. Claude liest Bilder
   direkt, extrahiert Prinzipien → Ziele: Tages-Impuls/Wissens-Tipps + Training-Logik +
   Schlaf-Empfehlungen. (Bei Supplement-Bezug erst `/supplement-stack-check`.)
3. **Phase B** (Features): F0 Apple-Health-Sync (Kurzbefehl-Brücke), F1 Insight-Loop,
   F2 Top-3 + Wenn-Dann, F3 Deep-Work-Timer, F4 Koffein-Cutoff. (Details im Planfile.)

---

## Was in der vorletzten Session erledigt wurde (31. Mai 2026)

### Tab-Bar bündig unten — ENDGÜLTIG GELÖST (iPhone 16 Pro Standalone)

**Symptom:** Tab-Bar schwebte zu weit oben, großer Cream-Streifen bis zum Rand.

**Wahre Ursache (per On-Device-Debug-Overlay gefunden, nicht geraten):**
`apple-mobile-web-app-status-bar-style: black-translucent` positioniert den
Viewport (812px) **oben** am Screen (874px) → unten bleiben **62px tot**.
`innerHeight`/`100dvh`/`100vh`/`visualViewport` melden ALLE 812; nur `screen.height`
kennt die echten 874. Deshalb scheiterten alle früheren CSS-Höhen-Fixes.

**Lösung:**
- `status-bar-style: black-translucent → default` → Viewport sitzt jetzt bündig
  unten am Rand, kein Phantom-Gap. (Oben dafür normale Creme-Statusleiste.)
- Tab-Bar = normales Flex-Kind (`flex-shrink:0`, kein `position:fixed`),
  `padding-bottom: 8px` (flach, kein Safe-Area-Cream-Streifen mehr).
- `#app-frame{height:100vh;height:100dvh;overflow:hidden}`, reine Flex-Spalte.
- `.view` padding-bottom 16px (alter 74px-Overlap-Hack raus).
- **WICHTIG:** `status-bar-style` wird von iOS nur beim Installieren gelesen →
  nach Änderung App vom Home-Bildschirm entfernen + neu hinzufügen.

**NIE WIEDER:** `black-translucent`, `position:fixed` auf Bar/Container verschachteln,
oder innerHeight/vh/dvh für die Höhe vertrauen. Ein `fitApp()`-screen.height-Hack
wurde probiert und wieder entfernt (Symptombehandlung).

### Wochenring saniert

- **Doppelte „7/7" entfernt:** Eyebrow (`#ring-phase`/`#ring-time`) wurde von
  `updateDayRing()` zweckentfremdet. Jetzt eigene `updateRingClock()` → echte
  Tagesphase (NACHT/MORGEN/TAG/ABEND) + Uhrzeit, Minuten-Takt.
- **Farb-Logik:** Tag erledigt (Training ODER Daily) = grün (vorher zweifarbig
  grün/orange → sah bei voller Woche „halb kaputt" aus). Trainingstage = dezenter
  dunkler Akzentpunkt aufs Segment.
- **Glow-Filter entfernt**, alle Segmente einheitlich `stroke-width 6` (vorher war
  heutiges dicker → wirkte oben unbündig).

### Aufräumen + Sonstiges

- `.gitignore` angelegt (`.DS_Store`, `index.backup.html`, `preview-t0*.html`,
  `.claude/launch.json`). Dateien NICHT gelöscht, nur ignoriert (Dustins Wunsch).
- `.claude/launch.json` für lokalen Preview-Server (python http.server 8742) angelegt.

### Strategie-Entscheidung

**Weiterbauen, nicht neu aufsetzen.** Substanz (Datenmodell, Trainingslogik, Charts,
Supps) funktioniert; Probleme waren Oberflächen-Bugs. Rewrite = Falle.

### OFFEN — Phase B (Features, von Dustin gewählt, eigene Runde)

Reihenfolge: F0 → F1 → F2/F3/F4. (#5 Morgenlicht / #6 Atem-Reset bewusst raus.)
- **F0 Apple-Health-Read-Sync:** PWA kann HealthKit NICHT direkt lesen. Lösung:
  Kurzbefehl-Brücke (`shortcuts://` wie bestehendes `writeToHealth()`, rückwärts)
  liest Schritte/Schlaf/Ruhepuls/HRV → öffnet App mit `?steps=&sleep=...` →
  Query-Parser in der App speichert. Optional iOS-Automation für freihändig.
- **F1 Daten→Insight-Loop:** Muster aus vorhandenen `db()`-Daten (z.B. Schlaf↔Energie).
- **F2 Tages-Top-3 + Wenn-Dann-Pläne** (Implementation Intentions).
- **F3 Deep-Work-Timer** (nutzt bestehende Rest-Timer-Logik, 90/20 ultradian).
- **F4 Koffein-Cutoff-Wächter** (Uhrzeit zum Koffein-Log).

### Optimierungs-Notizen (Hintergrund)

- **Caching-Schmerz:** Ohne Service Worker cacht iOS das HTML stur (Pages
  `max-age=600`). Updates testen via Safari + `?v=N` (Cache-Bust). Echte Lösung =
  Service Worker (= neue Datei, gegen aktuelle Regel) → Phase-B-Kandidat.
- **Datensicherheit:** `localStorage` einzige DB, iOS löscht bei ITP → regelmäßig
  exportieren; mittelfristig IndexedDB.

---

## Was davor erledigt wurde (28. Mai 2026)

### Layout-Fixes (iOS Safari)

- **Tab-Bar:** `position:fixed` entfernt → `flex-shrink:0` als normales Flex-Kind am Ende von `#app-frame`. Löst das iOS-Safari-Problem mit der schwebenden Tab-Bar dauerhaft.
- **`#app-frame`:** `height:100%; display:flex; flex-direction:column; overflow:hidden` — kein `position:fixed`, kein `position:relative` (verhinderte früher korrekte Fixed-Positionierung von Kindern auf iOS)
- **`body`:** kein `display:flex` mehr auf Mobile — verhinderte korrekte Viewport-Höhen-Berechnung
- **View `padding-bottom`:** 142px → 16px (Tab-Bar ist jetzt im Fluss, kein Overlay mehr)
- **Splash-Screen:** `font-size: min(72px, 15vw)` — skaliert auf iPhone-Größe; Safe-Area-Padding damit Inhalt nicht in Dynamic Island ragt; Animations-Letter-Spacing entfernt (verursachte Overflow)

### Mac Desktop Layout (`@media min-width: 768px`)

- **Volle Browserbreite** — kein zentriertes Card-Layout mehr, `#app-frame` nimmt `flex:1`
- **CSS Grid:** `190px sidebar | 1fr content`, `auto nav | 1fr content-row`
- **Sidebar:** Tab-Bar wird zur linken Navigationsspalte; DJI-Logo via `::before`; Tab-Buttons horizontal (Icon + Text linksbündig)
- **Nav:** nur in der Content-Spalte (`grid-column:2`)
- **Hover-Effekte:** Cards, Gym-Buttons, Supp-Items, Tab-Buttons
- **Modals:** zentrierte Dialoge (nicht Bottom-Sheet) auf Desktop

### Fortschrittsfotos: 3 Typen

- **Neues Layout:** 3×2-Grid — Spalten = Front / Seite / Back, Reihen = Vorwoche (readonly) / Diese Woche (Upload)
- **Storage-Format:** `ph[weekKey] = {front, side, back}` statt einzelnem String
- **Rückwärtskompatibilität:** alter String-Wert wird als `front` interpretiert
- **`loadPhotos()`** ersetzt `loadPrevPhoto()` — lädt alle 3 Typen für beide Wochen
- **`loadPhoto(input, type)`** — type-aware Upload (`'front'`/`'side'`/`'back'`)
- **`saveCheckin()`** — merged neue Fotos mit bestehenden (kein Überschreiben unbefüllter Slots)
- **Check-in-Verlauf:** zeigt jetzt z.B. "3 Fotos" statt nur "Foto"

### Training-Tab

- **Performance → Trainingsqualität** — Label im Training-Tab + Chart-Header im Weekly-Tab; Datenschlüssel `performance` unverändert (Bestandsdaten erhalten)
- **Progressive Overload Regel-Badges:** `setProgLabel()` zeigt jetzt inline welche Regel greift:
  - `R1 +Wdh` — gleich Gewicht, mehr Wiederholungen
  - `R2 +kg` — Gewicht erhöht bei ≥5 Wdh
  - `R3 8er` — 8-Wdh-Decke erreicht, nächste Session Gewicht erhöhen
- **Startbein statt Startarm** für Beinübungen — `isLegMuscle(muscle)` prüft Hamstrings/Quads/Waden/Adduktoren/Glutes
- **`isUnilateral()`** erkennt jetzt auch `Kurzhantel` (ausgeschrieben) und `Preacher` (immer unilateral)
- **Seitheben Maschine** zur Exercise-DB für JoyFitness (Index 1) hinzugefügt — via manuelle Seed-Liste in `initExDB()`

### Spacing

- `card margin-bottom`: 14px → 10px (alle Karten inkl. inline-Styles)
- `card padding`: 16px → 14px / 14px → 12px
- `view padding-top`: 16px → 12px
- `nav padding-top` extra: +10px → +6px, `nav-top margin-bottom`: 10px → 6px

---

## Was in der vorletzten Session erledigt wurde (27. Mai 2026)

### Wochenring + Statistik-Charts + UX

- **7-Segment Wochenring** (Mon–So) ersetzt alten Task-Ring
- **4 neue Statistik-Charts** im Weekly-Tab (Wohlbefinden, Performance/Trainingsqualität, Zone 2 Cardio, Sätze/Muskelgruppe)
- **Umfang-Felder:** Bauch (`u_nabel`), Oberarm (`u_oberarm`), Oberschenkel (`u_oberschenkel`)
- **PLAN-Muskelgruppen präzisiert:** Posterior→Hamstrings, Rücken→Lats/Oberer Rücken
- **UX:** Exercise-Completion-Badge, Weekly Quick Score, Hero-Gewicht-Direktsprung, Zone 2 HF-Hint

### Foundation Design System

- Light-Cream-Theme: `--bg:#E8E3D8`, `--gold:#C8643C`, Geist-Fonts, Hero-Strip `#0F0F0E`

### Übungsdatenbank + Session-Swap

- `lbv4_exdb`: `{name: {muscle, gyms:[...]}}` — Gym-Index-aware
- Swap-Modal (Bottom-Sheet) mit Suchfeld, Muskelgruppe-Sortierung
- `confirmSwap(ei, name, muscle)`: wechselt Übung, behält Satz-Werte + Startarm/bein

---

## Frühere Sessions (Zusammenfassung)

- Gym-Wechsel-Bug behoben (Inputs bleiben erhalten)
- Timer-Chime (C5–E5–G5) + visuelles Banner
- Progressive Overload 5–8-Wiederholungs-Regel (`setProgLabel`)
- ERLEDIGT-Badge nach `saveTraining()`
- Abend-Journal mit rotierenden Fragen
- Tages-Completion Ring, Split-Banner, Gewicht-History-Modal, Koffein-Log

---

## Technische Referenz

- **Syntaxcheck JS:** `node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')`
- **Health-Button Shortcut-Name:** exakt `Logbuch-zu-Health`
- **Gewicht-Format:** `val.toFixed(1).replace('.', ',')` — deutsches Format zwingend
- **Supplement-Änderungen:** immer zuerst `/supplement-stack-check` ausführen, Feedback vorlegen, erst nach Freigabe einbauen
- **Keine neuen Dateien** — alles in `index.html`
- **Vor Löschen/Überschreiben** immer nachfragen
- **Exercise-DB Seed-Liste:** in `initExDB()` — manuelle Einträge für Übungen die nicht im PLAN stehen: `['Name','Muskel',GymIndex]`
- **Foto-Storage:** `ph[weekKey]` = `{front, side, back}` (neu) oder `string` (alt → wird als front behandelt)
- **isLegMuscle(m):** Hamstrings|Quads|Waden|Adduktoren|Glutes → Startbein statt Startarm

---

## Aktueller Supplement-Stack

### DAILY_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Rhodiola Rosea | 500mg · nüchtern besser | morgens |
| Elektrolyte | 5g | morgens |
| Kreatin | ~8g (0.1g/kg) | morgens |
| Curcumin NovaSol | 500–1000mg · Mahlzeit zwingend | mahlzeit |
| Omega-3 | 2–4g EPA+DHA · Mahlzeit zwingend | mahlzeit |
| Vitamin D3 + K2 | 5000 IE · Mahlzeit zwingend | mahlzeit |
| GEN-All in One | 4–8 Kapseln · Mahlzeit zwingend | mahlzeit |
| Zink | 25mg | abends |
| Magnesium Bisglycinat | 300–500mg | abends |
| Ashwagandha | 600mg · mit etwas Fett | abends |
| Lithium Orotat | 8mg · Mahlzeit zwingend | abends |

### SLEEP_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Melatonin | 0.5–1mg | 30 min vorher |
| 5-HTP | 50–200mg · nicht dauerhaft | 1–2h vorher |
| GABA | 100–1000mg | 30–60 min vorher |
| Mg L-Threonat | 1–2g | 2h vorher |
| L-Theanin | 100–400mg | 30–60 min vorher |

### ILLNESS_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Zink (akut) | 100mg · max. 1–2 Wochen | morgens |
| Vitamin C | 2–3g | akut |
| NAC | 1.200mg | akut |
| Glutamin | 20–30g | morgens |
| Curcumin (akut) | 500–2000mg | zur Mahlzeit |
| Omega-3 (akut) | 3g EPA+DHA | zur Mahlzeit |
