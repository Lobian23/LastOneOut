# LAST ONE OUT – Remote Events Spec (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Gültig für:** alle RemoteEvents/RemoteFunctions  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Zweck

Diese Spec definiert die **verbindliche Struktur, Benennung und Nutzung** von Remotes.

Sie legt fest:
- wo Remotes liegen
- wie sie heißen
- welche Richtung erlaubt ist (Server → Client / Client → Server)
- Payload-Regeln (Safety)
- No-Gos

---

## 1) Speicherort (verbindlich)

Alle Remotes liegen ausschließlich unter:

```text
ReplicatedStorage
└─ Remotes
```

Andere Speicherorte sind **nicht erlaubt**.

---

## 2) Explorer Naming

- Ordner und Remotes sind **PascalCase**
- Keine snake_case oder lowercase Namen im Explorer

Beispiele:
```text
Remotes/Match/RoundState
Remotes/Ui/ShowSafe
Remotes/Training/StartRequest
```

---

## 3) Ordnerstruktur

```text
ReplicatedStorage
└─ Remotes
   ├─ Match
   ├─ Ui
   ├─ Training
   └─ Debug            (optional, nur Studio/Dev)
```

### Regeln
- `Match`: Match- & Round-Lifecycle (Status, Winner, Spectator-State)
- `Ui`: reine UI-Anweisungen & UI-State (anzeigen/verstecken/updates)
- `Training`: Training-spezifische Requests/Updates
- `Debug`: nur wenn Dev aktiviert; darf niemals notwendig fürs Gameplay sein

---

## 4) Remote Typen & Richtungen

### RemoteEvent (Standard)
- **Server → Client:** UI/state updates, notifications
- **Client → Server:** Requests (z.B. Start Training), niemals Entscheidungen

### RemoteFunction (selten)
- nur wenn eine **synchrone Antwort** zwingend erforderlich ist
- bevorzugt trotzdem RemoteEvent + Callback Event Pattern

---

## 5) Richtungsregeln (Safety)

### Server → Client
Erlaubt:
- UI Zustände setzen
- Round/Match Status senden
- kosmetische Effekte triggern

Nicht erlaubt:
- geheime Serverdaten (Seeds, AntiCheat, interne States)
- direkte Objekt-Instanzen aus Workspace als „Authority“

### Client → Server
Erlaubt:
- UI-Requests (Start/Cancel/Toggle)
- Auswahl im Training (Picker Auswahl)
- „Ich bin bereit“ Signale

Nicht erlaubt:
- SAFE/Eliminate Entscheidungen
- Punkte oder Winner setzen
- direkte State-Mutationen (z.B. `SetOutOfGame(true)`)

> **Regel:** Client fragt – Server entscheidet.

---

## 6) Payload-Regeln

- Payloads sind **klein** und **seriell** (numbers/strings/booleans/tables)
- Keine Instances als Pflichtparameter
- Immer validieren:
  - Player-Status (active/training/spectator)
  - Rate Limits (cooldowns)
  - Plausibilität (IDs existieren, im Range, unlocked)

### Empfohlenes Pattern (Table Payload)
```lua
-- server -> client
remotes.ui.roundstate:FireClient(player, {
	phase = "countdown",
	seconds = 3,
	challengename = "LaserMaze",
})
```

---

## 7) Naming-Konvention für Events

Remotes sind **PascalCase** und beschreiben eine Aktion oder einen State.

### Empfehlungs-Liste
- State/Updates: `RoundState`, `MatchState`, `SafeState`, `SpectatorState`
- UI Actions: `ShowSafe`, `ShowLose`, `ShowWinner`, `UpdateBoard`
- Requests: `StartRequest`, `CancelRequest`, `SelectChallengeRequest`

---

## 8) Minimal-Set (empfohlen)

### Match
- `RoundState` (server → client)
- `MatchState` (server → client)
- `SpectatorState` (server → client)

### Ui
- `ShowSafe` (server → client)
- `ShowLose` (server → client)
- `ShowWinner` (server → client)
- `InfoBoard` (server → client)

### Training
- `StartRequest` (client → server)
- `ModeToggleRequest` (client → server)
- `PickerSelectRequest` (client → server)
- `TrainingState` (server → client)

---

## 9) No-Gos

- Remotes außerhalb von `ReplicatedStorage/Remotes`
- Client setzt Gameplay-Zustände
- unvalidierte Client-Payloads
- Remote spam ohne Cooldown/RateLimit
- „God Remote“ (ein Remote für alles)

---

## 10) Leitsatz

> *Remotes sind Transport – nicht Entscheidung.*

---

**ENDE – Remote Events Spec v1.0 (FROZEN)**
