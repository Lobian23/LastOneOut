# LAST ONE OUT – Challenge Structure (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Gültig für:** alle Challenges  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Zweck

Diese Spec definiert die **verbindliche Ordner- und Objektstruktur einer Challenge**.

Sie legt fest:
- wie ein Challenge-Ordner aufgebaut ist
- welche Elemente **pflichtig** sind
- welche **optional** sind
- was **nicht erlaubt** ist

Sie definiert **keine Gameplay-Regeln**.

---

## 1) Speicherort

Alle Challenges liegen **ausschließlich** unter:

```
ServerStorage/Challenges/
```

Andere Speicherorte sind **nicht erlaubt**.

---

## 2) Ordner-Naming

### Verbindliches Format
```
NNN_ChallengeName
```

- `NNN` = numerische ID (3-stellig, führende Nullen)
- `ChallengeName` = PascalCase
- Trennzeichen: `_`

### Beispiele
```text
001_RedLightGreenLight
010_LaserMaze
120_FinalArena
```

---

## 3) Pflichtstruktur (Minimal)

Jede Challenge **muss** folgende Struktur besitzen:

```text
NNN_ChallengeName
├─ Map
├─ Logic
├─ Description
```

### Beschreibung der Pflichtobjekte

#### `Map` (Folder)
- enthält alle Parts / Models der Challenge
- darf beliebig komplex sein

#### `Logic` (ModuleScript)
- enthält die Gameplay-Logik der Challenge
- implementiert das verpflichtende Interface

#### `Description` (StringValue)
- kurze Beschreibung der Challenge
- wird in der UI angezeigt

---

## 4) Optionale Elemente

```text
NNN_ChallengeName
├─ Map
├─ Logic
├─ Description
├─ RoundTime        (IntValue)
├─ ChallengeStart   (Part)
```

### `RoundTime` (IntValue)
- definiert ein Zeitlimit in Sekunden
- `0` oder nicht vorhanden = kein Timer

### `ChallengeStart` (Part)
- optionaler Startpunkt
- Spieler können dorthin teleportiert werden

---

## 5) Pflicht-Interface (Logic)

Jede `Logic` **muss** folgendes Interface implementieren:

```lua
Logic.Init(context)
Logic.Start()
Logic.Stop(reason)

Logic.Signals.PlayerSafe(player)
Logic.Signals.Eliminate(player, reason)
Logic.Signals.Ended(reason)
```

Andere Kommunikationswege sind **nicht erlaubt**.

---

## 6) Erlaubt / Verboten

### Erlaubt
- zusätzliche Folder **innerhalb von `Map`**
- Hilfs-Module **innerhalb des Challenge-Ordners**
- lokale Constants innerhalb der Logic

### Verboten
- zusätzliche Root-Objekte auf Challenge-Ebene
- globale Variablen
- direkte Abhängigkeiten zu anderen Challenges
- Gameplay-Logik außerhalb von `Logic`

---

## 7) Verantwortlichkeiten

- Challenge definiert:
  - SAFE-Bedingungen
  - Eliminierungen
  - Win / Lose / Timeout
- Challenge entscheidet **nicht**:
  - Match-Ende
  - Punkte
  - SAFE-Reihenfolge
  - UI-Zustände außerhalb der Challenge

---

## 8) Versionierung & Stabilität

- Strukturänderungen sind **Breaking Changes**
- Änderungen nur über neue Version
- Alte Challenges bleiben kompatibel

---

## 9) Leitsatz

> *Challenge ist Gameplay – Gameplace ist Orchestrator.*

---

**ENDE – Challenge Structure v1.0 (FROZEN)**
