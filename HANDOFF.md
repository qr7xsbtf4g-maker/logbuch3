# HANDOFF — Dashboard DJI

## Aktueller Stand

**Datei:** `/Users/djoker/Claude/Claude/Projects/Projekt Dashboard CC/index.html`  
**Zeilen:** ~3647  
**Git:** Ja — remote auf GitHub, GitHub Pages aktiv  
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

## Was in der letzten Session erledigt wurde (28. Mai 2026)

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
