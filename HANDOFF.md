# HANDOFF — Dashboard DJI

## Aktueller Stand

**Datei:** `/Users/djoker/Projekt Dashboard CC/index.html`  
**Zeilen:** ~2810  
**Kein Git**, kein Build-System — direkte Dateibearbeitung, Syntaxcheck via `node --check`

---

## Was in dieser Session erledigt wurde

### UX-Verbesserungen (nach Vergleich mit Whoop/Oura/Strong/Garmin)

#### Ring: Tageszeit → Tages-Completion
- Neue Funktion `calcDayCompletion()` — 4 Aufgaben à 25%: Morgen (Gewicht/Schlaf), Supps (alle DAILY_SUPPS abgehakt), Training (type==='training' oder 'cardio' für heute), Abend (Stimmung & Energie)
- `updateDayRing()` komplett ersetzt: zeigt `pct%`, nächste offene Aufgabe (`MORGEN`/`SUPPS`/`TRAINING`/`ABEND`/`KOMPLETT`), `done/4`
- Ring-Farbe: `rgba(255,255,255,0.12)` bei 0%, `--gold` bei 1–3/4, `--okl` bei 4/4
- `updateDayRing()` wird jetzt auch nach `saveMorning`, `saveEvening`, `saveTraining`, `saveCardio`, `toggleSuppItem` aufgerufen
- Entfernt: `ringColor()`, `RING_WAKE`, `RING_SLEEP`, Konstante `RING_C`, `setInterval` für Ring

#### Training-Tab: Split-Banner
- Neues HTML-Banner `#today-plan-banner` oben im Training-Tab (vor "Krafttraining") mit `#today-plan-day`, `#today-plan-label`, `#today-plan-badge`
- Neue Funktion `initTodayPlan()` — zeigt Wochentag + Split-Label + Badge ("GEPLANT"/"RUHETAG"), bordert gold an Trainingstagen
- `_todaySplit` wird in `ST` gespeichert (z.B. `'OK'`, `'UK'`, `'GK1'`, `'GK2'` oder `null`)
- `togKraft()` erweitert: wenn Kraft-Ja gedrückt und noch kein Split gewählt, wird `ST._todaySplit` automatisch selektiert
- `initTodayPlan()` wird in `init()` und in `goTab()` bei idx===1 aufgerufen

#### Datum-Feld: kompaktes Inline-Format
- Ersetzt: `<div class="f"><label>Datum</label><input type="date" id="d-datum"></div>`
- Neu: kompakte Flex-Zeile — kleines `DATUM`-Label links, `#d-datum` Input transparent/gold rechts
- `id="d-datum"` bleibt unverändert (wird überall referenziert)

#### Hero-Metriken: Farbdifferenzierung
- `updateHeroNumbers()` setzt jetzt `style.color` pro Metrik:
  - Gewicht/Schlaf: `--txd` wenn leer, `--tx` wenn Wert vorhanden
  - Streak: `--txd` bei 0, `--gold` bei > 0

### Bug-Fix: Initialisierungsreihenfolge
- **Ursache:** `DAILY_SUPPS`, `SLEEP_SUPPS`, `ILLNESS_SUPPS` und `suppData` waren mit `let`/`const` nach dem `init()`-Aufruf deklariert → Temporal Dead Zone → ReferenceError → Ladescreen friert ein
- **Fix:** Alle drei SUPPS-Konstanten und `suppData` nach oben zu den anderen Globals verschoben (direkt nach `ST`-Deklaration, ~Zeile 876)
- `ST._todaySplit:null` direkt in der ST-Deklaration ergänzt

---

## Was in früheren Sessions erledigt wurde

### Apple Health — vollständig entfernt (frühere Session)
Alle Health-Sync-Funktionen (`writeToHealth`, `syncFromClipboard`, `importFromHealth`, Health-Card im Daily-Tab, Schritte-Statistiken im Weekly-Tab) wurden auf Wunsch komplett entfernt.

Einzige Ausnahme: Der **"♥ In Apple Health schreiben"-Button** unter dem Gewicht-Feld ist noch vorhanden, da er explizit wieder hinzugefügt wurde. Er triggert `writeToHealth()` via `shortcuts://run-shortcut?name=Logbuch-zu-Health&input=...` und sendet das Gewicht im Format `85,1` (Komma statt Punkt — wegen deutschem iOS Shortcuts-Dezimalformat).

### Umbenennung: Logbuch → Dashboard
- App-Ladescreen: "LOGBUCH" → "DASHBOARD"
- Nav-Logo: "LOGBUCH" → "DASHBOARD"
- Ordner umbenannt: `Projekt Logbuch CC` → `Projekt Dashboard CC`
- CLAUDE.md aktualisiert

### Neue Features
- **Gewicht-History-Modal:** Tap auf Gewicht-Hero-Item zeigt letzte 7 Einträge mit Delta-Anzeige (grün/rot)
- **Koffein:** ½ Höllenfeuer-Button (50mg) und freies Custom-Eingabefeld für beliebige mg-Menge
- **Health-Schreib-Button:** Steht als eigenständiger Button unter den Gewicht/Schlaf-Feldern (vorher im Label, was versehentliche "Eintrag löschen"-Taps verursachte)

### Entfernte Felder
- **Wasser (l)**, **Cheats**, **Kalorien-Ziel** — komplett aus HTML, JS (save/load/render) und Weekly entfernt

### Supplement-Stack aktualisiert
- Vitamin D3 + K2: von `abends` → `mahlzeit`
- GEN-All in One: neu hinzugefügt (`mahlzeit`)
- Nattokinase: aus ILLNESS_SUPPS entfernt
- Vitamin D3 + K2 (akut): aus ILLNESS_SUPPS entfernt
- Alle Dosis-Strings annotiert mit Fettlöslichkeit und Mahlzeit-Pflicht (via `/supplement-stack-check`)

### Bug-Fixes
- **App-Crash nach Felderentfernung:** `loadTodayEntry()` referenzierte gelöschte DOM-Elemente (`cheat-field`, `d-cheats`) — durch bereinigten Block ersetzt
- **Overscroll ins Schwarze:** `min-height:100vh` auf `.view` entfernt
- **Dezimalformat Health:** `85.05` wurde als `8.505` in Health angezeigt — Fix: `toFixed(1).replace('.', ',')`

### Code-Cleanup
- Totes Konstant `const TABS=[...]` entfernt
- Tote Funktionen `popExSel()` und `renderExHistory()` entfernt (referenzierten nicht-existente IDs `ex-select`, `ex-history-table`)
- Tote Variable `weeklyDaily` entfernt (wurde nach Kalorien/Cheats-Entfernung nicht mehr gelesen)

### Performance & Stabilität
- **In-Memory-Cache für `db()`:** `_dbCache` hält geparsten Array; wird nur bei Write invalidiert — kein unnötiges `JSON.parse` pro Render
- **localStorage-Quota-Fehlerbehandlung:** Alle Save-Funktionen (`dbS`, `dbCIS`, `dbPHS`, `dbHIS`) nutzen `_safeSave()` mit `try/catch` — bei `QuotaExceededError` erscheint Toast statt stiller Crash
- **Foto-Komprimierung mit Fallback:** Max-Dimension 800px, Qualität 0.75 → 0.5 → 0.3 wenn Bild nach erster Stufe noch >300KB

---

## Aktueller Supplement-Stack (in `index.html`)

### DAILY_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Rhodiola Rosea | 500mg · nüchtern besser | morgens |
| Elektrolyte | 5g · Mahlzeit egal | morgens |
| Kreatin | ~8g (0.1g/kg) · Mahlzeit egal | morgens |
| Curcumin NovaSol | 500–1000mg · fettlöslich · Mahlzeit zwingend | mahlzeit |
| Omega-3 | 2–4g EPA+DHA · fettlöslich · Mahlzeit zwingend | mahlzeit |
| Vitamin D3 + K2 | 5000 IE · fettlöslich · Mahlzeit zwingend | mahlzeit |
| GEN-All in One | 4–8 Kapseln · fettlöslich (ADEK) · Mahlzeit zwingend | mahlzeit |
| Zink | 25mg · nicht mit Calcium/Eisen · Mahlzeit empfohlen | abends |
| Magnesium Bisglycinat | 300–500mg · Mahlzeit empfohlen | abends |
| Ashwagandha | 600mg · mit etwas Fett · Mahlzeit empfohlen | abends |
| Lithium Orotat | 8mg · Mahlzeit zwingend (GI) | abends |

### SLEEP_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Melatonin | 0.5–1mg · nüchtern besser | 30 min vorher |
| 5-HTP | 50–200mg · nicht dauerhaft · nüchtern besser | 1–2h vorher |
| GABA | 100–1000mg · Mahlzeit egal | 30–60 min vorher |
| Mg L-Threonat | 1–2g · Mahlzeit egal | 2h vorher |
| L-Theanin | 100–400mg · Mahlzeit egal | 30–60 min vorher |

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

## Wichtige technische Details

- **localStorage-Keys:** `lbv4` (Daily + Training), `lbv4_ci` (Check-in), `lbv4_ph` (Fotos), `lbv4_hi` (History), `lbv4_supp` (Supplement-Status), `lbv4_koff` (Koffein-Log), `lb_kraft_tog` / `lb_cardio_tog` (Toggle-Persistenz bis Mitternacht), `lb_backup_ts` (letzter Export-Zeitstempel)
- **Cache-Invalidierung:** Nur `dbS()` setzt `_dbCache`. `dbCI`, `dbPH`, `dbHI` haben keinen Cache — werden seltener aufgerufen
- **Health-Button Shortcut-Name:** muss exakt `Logbuch-zu-Health` heißen (bestehender Shortcut, nicht neu gebaut)
- **Gewicht-Format für Health:** `val.toFixed(1).replace('.', ',')` — deutsches Dezimalformat zwingend
- **Syntaxcheck-Befehl:** `node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')`

---

## Prozessregeln (aus CLAUDE.md)

- **Supplement-Änderungen:** Immer zuerst `/supplement-stack-check` laufen lassen, Feedback vorlegen, erst nach Freigabe in `index.html` einbauen
- **Keine neuen Dateien** — alles bleibt in `index.html`
- **Vor Löschen/Überschreiben** immer nachfragen

---

## Offene Punkte / Ideen

- iOS Shortcut "Health → Logbuch" existiert nicht mehr (wurde gelöscht) — falls wieder gewünscht, Anleitung aus alter HANDOFF konsultieren
