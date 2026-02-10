# LAST ONE OUT – Gameplace Architecture (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Gültig für:** alle Gameplace-Implementierungen  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Grundprinzip

Der Gameplace ist ein **Orchestrator**.

Er steuert:
- Ablauf
- Zustände
- Übergänge

Er entscheidet **keine Gameplay-Regeln**.

> **Leitsatz:**  
> *Gameplace orchestriert – Challenge entscheidet.*

---

## 1) Rolle des Gameplace

Der Gameplace ist der **zentrale Kontrollpunkt** für ein Spiel von *LAST ONE OUT*.

Er ist verantwortlich für:
- Match-Lifecycle
- Rundenabfolge
- Spielerstatus
- Kommunikation mit Challenges
- Konsistenz & Fairness

Der Gameplace ist **server-autoritativ**.

---

## 2) Verantwortlichkeiten

### Der Gameplace IST verantwortlich für:
- Start & Ende von Matches
- Start & Ende von Runden
- Auswahl und Laden von Challenges
- Übergabe des Context an Challenges
- SAFE-System inkl. eindeutiger Reihenfolge
- Eliminierungen
- Spectator-Zustände
- Training-Loop (Win / Lose / Bestzeit)
- UI-Zustände (Start, SAFE, LOSE, Winner)

### Der Gameplace ist NICHT verantwortlich für:
- Gameplay-Mechaniken
- Sieg- oder Verlustbedingungen innerhalb einer Challenge
- Spieler-Input-Auswertung
- Physik, Timer oder Zielbedingungen der Challenge

---

## 3) Orte & Trennung

### Lobby (Place 1)
- Startpunkt aller Spieler
- Party-/Queue-Logik
- Teleport in den Gameplace
- Punktesystem & Leaderboards

### Gameplace (Place 2)
- Orchestriert das Spiel
- Enthält keine Lobby- oder Meta-Systeme
- Erkennt den Spielmodus beim Join

### Challenge Maps
- Isolierte Gameplay-Einheiten
- Liegen in `ServerStorage/Challenges`
- Werden vom Gameplace geladen & entladen

---

## 4) Modes

Der Gameplace kennt genau **zwei Modi**:

### Multiplayer
- mehrere Spieler
- Last-One-Out Match
- mehrere Challenges
- permanente Eliminierung
- genau **1 Match-Winner**

### Training
- Singleplayer
- keine Match-Logik
- keine Punkte
- Fokus auf:
  - Win / Lose
  - persönliche Bestzeit
- endlos spielbar

Der erkannte Mode wird an jede Challenge übergeben.

---

## 5) Mode-Erkennung

Beim Join in den Gameplace:

- **Live:** `TeleportData`
- **Fallback (Studio):**
  - 1 Spieler → Training
  - >1 Spieler → Multiplayer

Der Mode ist **read-only** und darf nicht geändert werden.

---

## 6) Multiplayer – Match Flow

1. Spieler sind im Start-Haus
2. Tür ist geschlossen
3. Infotafel zeigt nächste Challenge
4. Door Countdown
5. Tür öffnet → Challenge START
6. Challenge läuft
7. Challenge meldet Ergebnisse
8. Eliminierungen werden durchgeführt
9. Match-Check:
   - genau 1 Spieler übrig → Match-Ende
   - sonst → nächste Runde

Ein Match darf **nie unentschieden** enden.

---

## 7) SAFE-System

- Spieler werden SAFE, sobald die Challenge dies meldet
- SAFE gilt **nur für die aktuelle Runde**
- SAFE wird **serverseitig** vergeben

### SAFE-Reihenfolge
- Der Server vergibt einen eindeutigen `SafeIndex`
- Reihenfolge: 1, 2, 3, …
- Reihenfolge ist verbindlich und stabil

SAFE-Reihenfolge wird genutzt für:
- SAFE-Leaderboard
- Round Winner

---

## 8) Round Winner

- Round Winner = Spieler mit `SafeIndex == 1`
- Falls in einer Runde kein neuer entsteht:
  - letzter gültiger Round Winner bleibt bestehen
- Round Winner ist kosmetisch relevant (UI)

---

## 9) Eliminierung & Spectator

- Eliminierungen werden ausschließlich durch Challenges ausgelöst
- Eliminierte Spieler:
  - erhalten LOSE-UI
  - werden `OutOfGame = true`
  - wechseln in Spectator Mode

Spectators:
- dürfen jederzeit leaven
- beeinflussen das Match nicht

---

## 10) Leave-Regel

- Active-Spieler leavt:
  - sofortige Eliminierung
  - 0 Punkte
- Spectators dürfen jederzeit leaven

Diese Regel ist **hart** und nicht verhandelbar.

---

## 11) Training – Architektur

- Training ist **kein Match**
- Start erfolgt **nur per UI-Button**
- Türmechanik ist deaktiviert
- Challenges melden:
  - `win`
  - `lose`
  - `timeout` (zählt als lose)

### Training-Ergebnisse
- bei Win:
  - Zeit messen
  - Bestzeit speichern
- bei Lose:
  - Lose-Zähler erhöhen

Nach Ende:
- Rückkehr ins Start-Haus
- nächste Challenge je nach Modus

---

## 12) Challenge-Architektur

### Speicherort
```
ServerStorage/Challenges/<ID - Name>/
```

### Pflichtbestandteile
- Map (Folder)
- Logic (ModuleScript)
- Description (StringValue)
- RoundTime (IntValue, optional)
- Challenge Start (Part, optional)

### Interface (verbindlich)
```lua
Logic.Init(context)
Logic.Start()
Logic.Stop(reason)

Logic.Signals.PlayerSafe(player)
Logic.Signals.Eliminate(player, reason)
Logic.Signals.Ended(reason)
```

Challenges dürfen **nur über diese Signale** mit dem Gameplace kommunizieren.

---

## 13) Config-Prinzip

- Alle Timings & Delays kommen aus der zentralen Config
- Keine Magic Numbers im Code
- Challenges dürfen keine globalen Timings definieren

Config ist:
- server-only
- read-only
- Single Source of Truth

---

## 14) UI-Philosophie

- Server entscheidet Zustände
- Client rendert UI
- UI folgt Zustand, nicht Logik

UI darf:
- anzeigen
- animieren
- visualisieren

UI darf nicht:
- entscheiden
- validieren
- beeinflussen

---

## 15) Explizite No-Gos

- Gameplay-Logik im Gameplace
- Entscheidungen im Client
- globale Variablen
- Magic Numbers
- Challenge-Abhängigkeiten untereinander

---

## 16) Schlussprinzipien

- Gameplace orchestriert – Challenge entscheidet
- Server ist autoritativ
- SAFE-Reihenfolge ist eindeutig
- Match endet nur mit genau 1 Winner
- Training ist kein Match

---

**ENDE – Architecture v1.0 (FROZEN)**
