# LAST ONE OUT – GAMEPLACE SPEC v1.0 (FROZEN)

**Status:** FINAL  
**Version:** v1.0  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Spiel-Setup (WICHTIG)
**LAST ONE OUT** besteht aus mindestens zwei Orten:

1. **Lobby (Ort 1)**
   - Spieler starten hier
   - Von hier aus Teleport in den Gameplace
   - Alleine → Training
   - Mit mehreren Spielern → Multiplayer Match

2. **Gameplace (Ort 2)** ← diese Spec
   - Hier läuft das eigentliche Spiel
   - Der Gameplace erkennt den Mode automatisch beim Join

---

## 1) Rolle des Gameplace
Der Gameplace ist ein **Orchestrator**, kein Gameplay-Designer.

**Er ist verantwortlich für:**
- Match- & Rundenabläufe
- Spielerzustände
- Start / Stop von Challenges
- SAFE-Reihenfolge & Eliminierungen
- Training-Loop (Win / Lose / Bestzeit)

**Er ist NICHT verantwortlich für:**
- konkrete Gameplay-Regeln
- Siegbedingungen innerhalb einer Challenge
- Respawn-Mechaniken

> **Grundregel:**  
> *Gameplace orchestriert – Challenge entscheidet.*

---

## 2) Modes

### Multiplayer
- Mehrere Spieler
- Last-One-Out Match
- Mehrere Challenges (Runden)
- Genau **1 Match-Winner**
- Spieler scheiden dauerhaft aus
- Punkte werden vergeben (nur Lobby-Leaderboard)

### Training
- Singleplayer
- Keine Match-Logik
- Kein Punktesystem
- Fokus:
  - Win / Lose
  - persönliche Bestzeit
- Endlos spielbar

---

## 3) Mode-Erkennung
Beim Join in den Gameplace:

- **Live:** `player:GetJoinData().TeleportData`
- **Studio/Fallback:**
  - 1 Spieler → Training
  - >1 Spieler → Multiplayer

Der erkannte Mode wird an jede Challenge übergeben:
```lua
context.mode = "multiplayer" | "training"
```

---

## 4) Multiplayer – Grundprinzip (Last One Out)
- Match besteht aus aufeinanderfolgenden Challenges
- Eine Challenge kann:
  - 0 Spieler eliminieren (Draw → nächste Challenge)
  - 1 Spieler eliminieren
  - mehrere Spieler eliminieren
- Match endet **nur**, wenn:
  - **genau 1 Spieler übrig ist**
- Wenn 0 Spieler übrig sind:
  - neue Challenge starten
  - Match darf **nie** unentschieden enden

---

## 5) Multiplayer – Rundenablauf
1. Spieler sind im **Start-Haus**
2. Tür ist geschlossen
3. Infotafel zeigt nächste Challenge (Name + Description)
4. **DoorCountdown**
5. Countdown = 0
6. **Tür öffnet → Challenge START**
7. Optional Teleport zu `Challenge Start`
8. Challenge läuft
9. Challenge endet → Eliminierungen (0..N)
10. Match-Check:
    - 1 Spieler übrig → Match Winner
    - sonst → nächste Challenge

---

## 6) SAFE-System (Multiplayer)
- Spieler wird SAFE, sobald Challenge-Bedingung erfüllt ist
- SAFE gilt nur für die aktuelle Runde

**SAFE-UI:**
- großes SAFE-PNG (kurz)
- danach grüner Screen-Rahmen

### SAFE-Reihenfolge (verbindlich)
- Server vergibt **SafeIndex = 1,2,3,…**
- Reihenfolge ist immer eindeutig
- Wird genutzt für:
  - SAFE-Leaderboard
  - Round Winner

---

## 7) Round Winner
- **Round Winner = SafeIndex #1**
- Falls in einer Runde kein neuer entsteht:
  - letzter gültiger Round Winner bleibt bestehen
- Round Winner wird in der nächsten Runde markiert (kosmetisch)

---

## 8) Eliminierung & Spectator
- Challenge entscheidet, wer eliminiert wird
- Eliminierte Spieler:
  - erhalten LOSE-UI
  - werden `OutOfGame = true`
  - wechseln in **Spectator Mode**
- Spectators dürfen jederzeit leaven

---

## 9) Leave-Regel (hart)
- Active-Spieler leavt (egal warum):
  - **0 Punkte**
  - sofort aus Match entfernen
- Spectators dürfen jederzeit gehen

---

## 10) Punkte (nur Multiplayer)
- Punkte sind nur für Lobby-Leaderboard
- Kein Einfluss auf Gameplay
- Schema:
  - 1. Runde raus → 0 Punkte
  - jede überlebte Runde → Punkte verdoppeln
  - erste gewonnene Runde = 1 Punkt

---

## 11) Training – Grundprinzip
- Singleplayer
- Keine Punkte
- Keine Match-Logik
- Fokus:
  - Win / Lose
  - Bestzeit

---

## 12) Training – Start & Ablauf
- Spieler ist im **Start-Haus**
- Tür bleibt geschlossen
- Start erfolgt **immer per UI-Button**:
  - **START**
- Gilt für Picker-Mode und Random-Mode

Nach Ende:
- Rückkehr ins Start-Haus
- nächste Challenge je nach Modus

---

## 13) Training – Win / Lose / Highscore
- Challenge endet mit:
  - `Ended("win")`
  - `Ended("lose")`
  - `Ended("timeout")` → zählt als Lose

**Bei Win:**
- Zeit von START → Ended("win") messen
- persönliche Bestzeit speichern (mit Datum)
- `Wins += 1`

**Bei Lose:**
- `Loses += 1`

---

## 14) Training – Challenge Auswahl

### Picker Mode
- Spieler wählt Challenge selbst

### Random Mode
- Zufällige freigeschaltete Challenge
- Nach Ende kein Picker
- Direkt nächste Challenge

Toggle per Button:
- `Next Challenge: Picker`
- `Next Challenge: Random`

---

## 15) Training – Unlock-Regeln
Eine Challenge ist spielbar, wenn:
1. **ID = 0 (Tutorial)** → immer verfügbar  
2. Challenge wurde im Multiplayer bereits gespielt  
3. Dev-Override `Unlock All` aktiv  

Sonst:
- Anzeige `???`
- nicht auswählbar

---

## 16) Challenge-System (verbindlich)

### Speicherort
```
ServerStorage/Challenges/<ID - Name>/
```

### Inhalt
- `Map` (Folder)
- `Challenge Start` (Part, optional)
- `Description` (StringValue)
- `RoundTime` (IntValue, 0 = kein Timer)
- `Logic` (ModuleScript)

### ID-Regeln
- ID ist **numerisch**
- `0` = Tutorial (Training-only)
- Multiplayer nutzt nur IDs ≥ 1
- `TestChallenge`:
  - `0` = OFF
  - `>0` = erzwingt diese ID (nur Multiplayer)

---

## 17) Challenge-Logic Interface (PFLICHT)

```lua
Logic.Init(context)
Logic.Start()
Logic.Stop(reason)

Logic.Signals.PlayerSafe(player)
Logic.Signals.Eliminate(player, reason)
Logic.Signals.Ended(reason)
```

### Training-Pflicht
- `Ended(reason)` **muss liefern**:
  - `"win"` oder `"lose"`
  - `"timeout"` zählt als Lose

---

## 18) Zentrale Leitsätze
- Gameplace orchestriert – Challenge entscheidet
- Multiplayer Start = Tür auf
- Training Start = START Button
- SAFE-Reihenfolge ist immer eindeutig
- Match endet nur mit genau 1 Winner
- UI wird gebaut, nicht generiert

---

**ENDE – SPEC v1.0**
