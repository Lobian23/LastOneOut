# LAST ONE OUT – Project Structure (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Gültig für:** gesamtes Projekt  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Zweck

Diese Spec definiert die **verbindliche Gesamtstruktur** des Projekts im Roblox Explorer.

Sie legt fest:
- welche Ordner wo liegen
- wofür sie zuständig sind
- was dort **nicht** liegen darf

Sie definiert **keine Gameplay-Regeln** und **keinen Code-Stil**.

---

## 1) Grundregeln

- Alle Explorer-Objekte sind **PascalCase**
- Keine losen Scripts im Root eines Services
- Struktur ist **stabil** und ändert sich selten
- Abweichungen gelten als Bug

---

## 2) Top-Level Services

```text
Workspace
ServerScriptService
ServerStorage
ReplicatedStorage
StarterPlayer
```

Andere Top-Level-Strukturen sind **nicht erlaubt**.

---

## 3) Workspace

```text
Workspace
├─ StartHouse
├─ ActiveMap          (runtime, von Gameplace geladen)
└─ Visuals            (rein kosmetisch)
```

### Regeln
- keine dauerhafte Gameplay-Logik
- `ActiveMap` wird zur Laufzeit befüllt / geleert
- keine Challenges direkt im Workspace

---

## 4) ServerStorage

```text
ServerStorage
├─ Challenges
│  └─ NNN_ChallengeName
│     ├─ Map
│     ├─ Logic
│     ├─ Description
│     ├─ RoundTime        (optional)
│     └─ ChallengeStart  (optional)
│
├─ Config
│  └─ GameConfig
│
└─ Assets
   ├─ Maps
   ├─ Models
   └─ Sounds
```

### Regeln
- Challenges liegen **nur** hier
- Config ist server-only
- Assets sind nicht aktiv im Spiel

---

## 5) ServerScriptService

```text
ServerScriptService
└─ Gameplace
   ├─ Bootstrap
   ├─ MatchFlow
   ├─ SafeSystem
   ├─ TrainingLoop
   ├─ PlayerState
   ├─ Spectator
   └─ Utils
```

### Regeln
- gesamte Orchestrator-Logik liegt hier
- kein Gameplay-Code
- klare Trennung pro System

---

## 6) ReplicatedStorage

```text
ReplicatedStorage
├─ Remotes
│  ├─ Match
│  ├─ Ui
│  └─ Training
│
└─ SharedModules
   ├─ Types
   └─ Helpers
```

### Regeln
- nur daten- oder strukturbezogene Module
- kein Server-State
- keine Entscheidungen

---

## 7) StarterPlayer

```text
StarterPlayer
└─ StarterPlayerScripts
   └─ Ui
      ├─ Screens
      ├─ Components
      └─ Controllers
```

### Regeln
- Client rendert UI
- kein Gameplay-Entscheiden
- reagiert nur auf Server-Zustände

---

## 8) Explizite No-Gos

- Gameplay-Logik außerhalb von Challenges
- Challenges außerhalb von ServerStorage
- Server-Entscheidungen im Client
- doppelte Systemverantwortungen
- uneinheitliche Ordnernamen

---

## 9) Leitsatz

> *Struktur ist Ordnung – Ordnung ist Stabilität.*

---

**ENDE – Project Structure v1.0 (FROZEN)**
