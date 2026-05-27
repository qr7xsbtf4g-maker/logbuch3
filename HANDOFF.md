# HANDOFF — Dashboard DJI

## Aktueller Stand

**Datei:** `/Users/djoker/Claude/Claude/Projects/Projekt Dashboard CC/index.html`  
**Zeilen:** ~3533  
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

## Was in der letzten Session erledigt wurde (27. Mai 2026)

### Wochenring + Statistik-Charts + UX

- **7-Segment Wochenring** (Mon–So) ersetzt alten Task-Ring — jeder Tag ein Arc; Grün = Training erledigt, Terracotta = Daily-only, dim = nichts; heute-Segment fetter + Glow; Zahl `x/7` im Zentrum
- **4 neue Statistik-Charts** im Weekly-Tab (nach Gewichtsverlauf):
  - Wohlbefinden: Schlafq. (lila) + Energie (terracotta), dual-line
  - Performance: Balken, überdurchschnittliche Tage volle Farbe
  - Zone 2 Cardio: Watt (blau, linke Achse) + Ø HF gestrichelt (terracotta, rechte Achse)
  - Sätze/Muskelgruppe: horizontal bar, aktuelle vs. Vorwoche
- **Umfang-Felder Check-in geändert:** Nabel→Bauch (`u_nabel`), Taille→Oberarm (`u_oberarm`), Hüfte→Oberschenkel (`u_oberschenkel`) — Feld-Keys + History + PDF-Export aktualisiert
- **Sätze/Woche/Muskelgruppe im Weekly-Tab** — kleine Chip-Reihe in `wbs-daily` und im Check-in (`ci-muscle-checkin`)
- **Einheit-Muskel-Summary im Training-Tab** — Chips zeigen Sätze je Muskelgruppe für aktuelle Session (`session-muscle-sum`)
- **PLAN-Muskelgruppen präzisiert:**
  - `'Posterior'` → `'Hamstrings'` (alle 3 Gyms)
  - `'Rücken'` → `'Lats'` (Pulldown-Varianten) / `'Oberer Rücken'` (Row-Varianten, Hammer Strength High Row) — web-recherchiert
- **UX #1 — Exercise-Completion-Badge:** `x/y` Chips bei jedem Übungsblock, partial=terracotta, done=grün, via `updateExDone(ei)` in `onSetInput()`
- **UX #2 — Weekly Quick Score:** 3 immer sichtbare Kacheln im Weekly-Tab (Einheiten, Ø Perf, Ø Schlafq.)
- **UX #3 — Hero-Gewicht direktsprung:** `heroGewichtTap()` — kein Gewicht eingetragen → scrollt zu Eingabe; bereits eingetragen → öffnet History-Modal
- **UX #4 — Zone 2 HF-Hint:** inline im Cardio-HF-Label, berechnet aus Geburtsdatum 1998-06-07 — aktuell „Zone 2: 125–145"
- **Illness-Stack mobil erreichbar:** 40px Spacer am Ende des Health-Tabs

### Foundation Design System — vollständige Transformation

- Dark-Gold-Theme → Light-Cream-Theme nach Foundation Design System (Apple × Uber × Claude)
- **Farben:** `--bg:#E8E3D8` (dunkles Cream), `--gold:#C8643C` (Terracotta), `--tx:#0F0F0E`, `--txm:#4D4B45`
- **Fonts:** Bebas Neue → Geist 700, Barlow Condensed → Geist, DM Mono → Geist Mono; Instrument Serif italic für Editorial-Momente
- Foundation Letter-Spacing Override CSS-Block (Geist braucht keine weiten Abstände)
- Hero-Strip: dunkles `#0F0F0E` Panel mit Radial-Gradient-Glow (Terracotta), Ring darin, KPI 2×2 Grid darunter
- Editorial-Block: Instrument Serif italic für Tages-Impuls-Quote, bold-fix für Takeaway-Text

### UX-Verbesserungen

- **Apple Health + Löschen:** Nebeneinander als Ghost-Buttons (50/50), nicht mehr im Fokus
- **Performance-Slider:** Startet immer auf 1 (war: 7) — zwingt zum bewussten Eintragen
- **Check-in Redesign:** Datum + KFA auf einer Zeile, Umfänge als inline KPI-Input-Cards, Fotos im 3:4-Format
- **Sonntags-Backup-Banner:** Wöchentliche Erinnerung im Weekly-Tab — Export-Button + Dismiss (wird nicht wieder angezeigt bis nächsten Sonntag, `lbv4_backup_dismissed` Key)

### Plan-Änderung

- **GK1 Trizeps:** Alle Gyms → `Polquin Extensions Unilateral`

### Übungsdatenbank + Session-Swap

- Neuer localStorage-Key `lbv4_exdb` — speichert `{name: muscle}` für alle bekannten Übungen
- `initExDB()`: scannt beim App-Start alle PLAN-Übungen (alle 3 Gym-Varianten), trägt neue automatisch ein
- `addToExDB(name, muscle)`: fügt einzelne Übung hinzu — wird z.B. beim Swap aufgerufen
- **⇄-Button** in jedem Übungsblock (gold, neben ↑↓) → öffnet Swap-Modal
- **Swap-Modal** (Bottom-Sheet): zeigt aktuellen Übungsnamen + Muskelgruppe, Suchfeld, sortierte Liste (gleiche Muskelgruppe zuerst → dann Rest alphabetisch)
- `confirmSwap(ei, name, muscle)`: wechselt Übung, behält eingetragene Satz-Werte + Startarm, übersteht auch ↑↓-Reorder
- Swap-State (`ST.swapOverrides`) wird beim Split-Wechsel zurückgesetzt

---

## Was in der vorletzten Session erledigt wurde (Mai 2026)

### Bug-Fix: Gym-Wechsel löscht Training-Inputs
- `selGym()` speichert jetzt alle Set-Inputs (kg + reps) vor dem `buildEx()`-Rebuild
- Nach dem Rebuild werden die Werte wiederhergestellt (identisch zu `moveEx`)

### Timer-Signal: 3-Ton-Chime + visuelles Banner
- `beep()`: aufsteigendes C5–E5–G5 Sinus-Chime mit Envelope
- `timerDoneNotify(type)`: Vibration + gold Banner `#timer-banner` oben im Viewport

### Progressive Overload: 5–8 Wiederholungs-Regel
- `setProgLabel()` nach Dustins Regel: ▼ <5 Wdh, ▲ Gewicht+≥5 Wdh, ▲ 8 Wdh erreicht, ▲ gleiche kg mehr Wdh, → 8 Wdh Vorwoche kein Aufschlag, ▼ Gewicht reduziert

### Check-in: Wochenverlauf-Modal + Sonntag-Only-Vergleich
- Gewicht- und Cardio-Stat-Box → Modal mit letzten 8 Wochen
- Vergleich "vs. Vorwoche" nur sonntags sichtbar

### Training-Tab: ERLEDIGT Badge
- Nach `saveTraining()` → Badge "GEPLANT" → "ERLEDIGT" (grün)

### Abend-Karte: One Week + 3-Min Journal
- Ja/Nein Toggle `oneWeekDone`
- Täglich rotierende Journalfrage + Textarea → speichert `eveningJournal` + `journalQuestion`
- "Letzte Einträge"-Button zeigt letzte 5 inline

---

## Frühere Sessions (Zusammenfassung)

### UX
- **Tages-Completion Ring** — 4 Aufgaben à 25% (Morgen, Supps, Training, Abend), gold → grün bei 100%
- **Split-Banner** im Training-Tab — Wochentag, Einheit, GEPLANT/RUHETAG/ERLEDIGT Badge
- **Gewicht-History-Modal** — letzte 7 Tage mit Delta (antippen auf Gewicht im Hero)
- Koffein-Log mit Preset-Buttons + Custom-Eingabe + Undo/Reset

### Daten / Storage
- **localStorage-Keys:** `lbv4` (Daily + Training), `lbv4_ci` (Check-in), `lbv4_ph` (Fotos), `lbv4_hi` (Hints), `lbv4_supp` (Supp-Status), `lbv4_koff` (Koffein-Log), `lbv4_exdb` (Übungsdatenbank), `lb_kraft_tog` / `lb_cardio_tog` (Toggle-Persistenz bis Mitternacht), `lbv4_backup_dismissed`
- `_dbCache` für Performance; `_safeSave()` mit Fallback

### Apple Health
- Alle automatischen Health-Sync-Funktionen entfernt
- Einziger Überrest: **"♥ In Apple Health schreiben"-Button** — Shortcut `Logbuch-zu-Health`, Gewicht im Format `85,1` (deutsches Komma, zwingend)

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

---

## Technische Referenz

- **Syntaxcheck JS:** `node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')`
- **Health-Button Shortcut-Name:** exakt `Logbuch-zu-Health`
- **Gewicht-Format:** `val.toFixed(1).replace('.', ',')` — deutsches Format zwingend
- **Supplement-Änderungen:** immer zuerst `/supplement-stack-check` ausführen, Feedback vorlegen, erst nach Freigabe einbauen
- **Keine neuen Dateien** — alles in `index.html`
- **Vor Löschen/Überschreiben** immer nachfragen
