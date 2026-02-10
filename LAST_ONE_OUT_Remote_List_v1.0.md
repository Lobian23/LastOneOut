# LAST ONE OUT – Complete Remote List (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Gültig für:** Gameplace & Client UI  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## Basis-Pfad

```
ReplicatedStorage/Remotes/
```

Alle Ordner und Remotes sind **PascalCase**.

---

## Match

```
Remotes/Match/
```

### Server → Client

- **MatchState**  
  `{ phase, matchid, alivecount, outcount }`

- **RoundState**  
  `{ phase, roundindex, challengeid, challengename, description, seconds }`

- **ChallengeLoaded**  
  `{ challengeid, challengename }`

- **ChallengeUnloaded**  
  `{ challengeid }`

- **PlayerState**  
  `{ state }` → `active | safe | out | spectator`

- **SpectatorState**  
  `{ enabled, targetuserid }`

- **RoundWinner**  
  `{ userid, safeindex }`

- **MatchWinner**  
  `{ userid }`

### Client → Server

- **LeaveMatchRequest**  
  `{}`

---

## Safe

```
Remotes/Safe/
```

### Server → Client

- **SafeAwarded**  
  `{ safeindex }`

- **SafeUi**  
  `{ action }` → `showbig | hidebig | showframe | hideframe`

- **SafeLeaderboard**  
  `{ entries }` → `{ userid, safeindex }[]`

---

## Elimination

```
Remotes/Elimination/
```

### Server → Client

- **Eliminated**  
  `{ reason }`

- **LoseUi**  
  `{ action }` → `show | hide`

- **OutList**  
  `{ userids }`

---

## Ui

```
Remotes/Ui/
```

### Server → Client

- **InfoBoard**  
  `{ title, description }`

- **Countdown**  
  `{ label, seconds }`

- **Toast**  
  `{ text, duration }`

- **ModeUi**  
  `{ mode }` → `training | multiplayer`

- **DoorState**  
  `{ open }`

---

## Training

```
Remotes/Training/
```

### Server → Client

- **TrainingState**  
  `{ phase, challengetype, challengelist, selectedid }`

- **TrainingResult**  
  `{ result, time, besttime, bestdate }`

- **TrainingUi**  
  `{ action }` → `showstart | hidestart | showpicker | hidepicker`

### Client → Server

- **StartRequest**  
  `{}`

- **RestartRequest**  
  `{}`

- **ModeToggleRequest**  
  `{ nextmode }` → `picker | random`

- **PickerSelectRequest**  
  `{ challengeid }`

---

## Debug (optional)

```
Remotes/Debug/
```

### Client → Server

- **ForceChallengeRequest**  
  `{ challengeid }`

- **SkipCountdownRequest**  
  `{}`

---

## Leitsätze

- Client sendet **Requests**
- Server trifft **alle Entscheidungen**
- Remotes sind Transport, keine Logik
- Keine God-Remotes
- Payloads sind klein & validiert

---

**ENDE – Complete Remote List v1.0 (FROZEN)**
