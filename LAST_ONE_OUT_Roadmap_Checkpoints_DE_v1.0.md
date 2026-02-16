# LAST ONE OUT – Ultra-detaillierte Entwicklungs-Roadmap (Checkpoint-basiert)
**Version:** 1.0  
**Datum:** 2026-02-16  
**Zweck:** Schritt-für-Schritt-Entwicklung mit klaren Gates (Zwischentests).  
**Benutzung:** In allen Chats sagst du nur: **„Ich bin bei CP-XX.YY“**.

---

## 0) Prozess-Regeln (verbindlich)

### 0.1 Gate-Prinzip
- Jeder Checkpoint endet mit einem **Zwischentest (Gate)**.
- **Nur wenn das Gate „PASS“ ist**, geht es weiter zum nächsten Checkpoint.
- Gate-Resultate werden dokumentiert (kurz, aber eindeutig).

### 0.2 Definition „PASS“
PASS bedeutet:
- Feature funktioniert **wie spezifiziert**
- Keine Blocker-Bugs
- Keine Spec-Verletzungen (Server autoritativ, keine God-Remotes, keine Magic Numbers, etc.)
- Minimal-UX passt (keine fehlenden UI-Hinweise / Softlocks)

### 0.3 Test-Log (Mini-Template)
Kopiere bei jedem Gate in dein Dev-Log (oder in eine Notion-Seite):
- **Checkpoint:** CP-XX.YY  
- **Build/Commit:** (hash / version)  
- **Testdatum:** (YYYY-MM-DD)  
- **Tester:** (Name)  
- **Ergebnis:** PASS / FAIL  
- **Notizen:** (max. 5 Bulletpoints)  
- **Fix-Liste (nur bei FAIL):** (Bug-Tickets / Tasks)

### 0.4 Rollen- & Spec-Hierarchie (Kurzform)
1) Gameplay Spec (max 1 Elimination/Runde, Abort-Regeln, Ranked Eligibility, etc.)  
2) Gameplace Architecture (TeleportData, Match Start Lock, Orchestrator)  
3) Multi-Place Spec (Lobby vs Gameplace Verantwortung)  
4) Struktur / UI / Points / Rank / Localization  
5) Technical Specs (Remotes, Coding Style, Dev Tools)

---

## 1) Checkpoint-Index (schnell)
> **Du referenzierst immer nur diese ID.**

### Phase A — Projekt-Foundation
- **CP-00.00** Repo/Place-Setup & Struktur
- **CP-00.10** Remotes & SharedModules Skeleton
- **CP-00.20** Config (server-only) + Loader
- **CP-00.30** Dev-Tools Minimal (Studio/Admin gated)
- **CP-00.40** Baseline Logging & Error Handling

### Phase B — Lobby Place (Entry)
- **CP-10.00** Lobby Map + Spawn + UI shell
- **CP-10.10** TeleportPads + TeleportService + TeleportData Contract
- **CP-10.20** Lobby Leaderboard (Daily/All-Time) UI + Refresh
- **CP-10.30** Lobby Remotes (TeleportStatus/LobbyToast) + UX

### Phase C — Gameplace Core (MatchFlow & States)
- **CP-20.00** Gameplace Bootstrap + TeleportData Validation
- **CP-20.10** PlayerState Core (active/safe/out/spectator)
- **CP-20.20** Match Start Lock (expected players / join timeout)
- **CP-20.30** MatchFlow Round Loop (N Spieler → N-1 Runden)
- **CP-20.40** Abort/Forfeit Policy (Gameplay Spec)
- **CP-20.50** Ranked Eligibility (mind. 1 Challenge-Elimination)

### Phase D — Challenge System (Load/Unload + Interface)
- **CP-30.00** Challenge Loader (ServerStorage/Challenges)
- **CP-30.10** Challenge Interface Wiring (Init/Start/Stop + Signals)
- **CP-30.20** TeleportStart / Portal (UI Spec)
- **CP-30.30** Challenge Template + 1 Dummy-Challenge
- **CP-30.40** Challenge QA Gate (cross-platform basic)

### Phase E — UI (Gameplace)
- **CP-40.00** Mode/State Badges (Training/Match/Spectator)
- **CP-40.10** InfoBoard (Challenge-Intro)
- **CP-40.20** Countdown (Numbers + optional Bar)
- **CP-40.30** Persistent Challenge Info + Task-Frames
- **CP-40.40** SAFE UI (Pop, Rahmen, Liste, Crown)
- **CP-40.50** OUT/Lose UI + Spectator UI
- **CP-40.60** Leave UI (Active Leave Confirmation)

### Phase F — Training Mode
- **CP-50.00** TrainingLoop State Machine
- **CP-50.10** Training Picker/Random Toggle
- **CP-50.20** Training Result Screen (WIN/LOSE/TIMEOUT + Bestzeit)
- **CP-50.30** Training Persistence (Bestzeit + Datum)

### Phase G — Progression (Points/Rank/Leaderboards)
- **CP-60.00** Stats Model (RoundsSurvived/Wins/Kings)
- **CP-60.10** Match-End Persist (regulär vs abort vs forfeit)
- **CP-60.20** RankPoints (RP) + SeasonId handling
- **CP-60.30** Daily Reset Regeln (00:00 UTC) & Boards

### Phase H — Localization (Keys + Table)
- **CP-70.00** Localizer Modul (GetText + Fallback EN)
- **CP-70.10** Remotes senden nur Keys (Toast/InfoBoard/Countdown)
- **CP-70.20** Locked Terms Enforcement (SAFE/OUT/RoundKing/MatchWinner)
- **CP-70.30** Minimal-Set für Launch abgeschlossen

### Phase I — Content (Challenges)
- **CP-80.00** Challenge #1 (vollwertig) + Gate
- **CP-80.10** Challenge #2 (vollwertig) + Gate
- **CP-80.20** Challenge #3 (vollwertig) + Gate
- **CP-80.30** Challenge Pool Minimum (z. B. 6–10 Challenges) + Balance
- **CP-80.40** Exploit/Edge-Cases (AFK, Disconnect, Timing, etc.)

### Phase J — Release Gates
- **CP-90.00** Performance & Memory (Map load/unload, remotes spam)
- **CP-90.10** Cross-Platform Full Pass (PC/Mobile/Console)
- **CP-90.20** Teleport Reliability (Lobby↔Gameplace)
- **CP-90.30** Final Regression Suite + Launch Checklist

---

## 2) Checkpoints im Detail (mit Gates)

> Jede Sektion enthält: **Ziel**, **Deliverables**, **DoD (Definition of Done)**, **Gate-Test**, **Fail-Kriterien**, **Notes**.

---

# Phase A — Projekt-Foundation

## CP-00.00 — Repo/Places/Struktur
**Ziel:** Multi-Place Projekt (LOBBY + GAMEPLACE) steht, Ordnerstruktur entspricht Spec.  
**Deliverables**
- Zwei Places: **LOBBY** und **GAMEPLACE**
- Ordnerstruktur gemäß Project Structure
- Keine Gameplay-Logik in LOBBY

**DoD**
- LOBBY: Lobbyplace/Bootstrap/TeleportService/LobbyUi/Utils existiert
- GAMEPLACE: Gameplace/Bootstrap/MatchFlow/SafeSystem/TrainingLoop/PlayerState/Spectator/Utils existiert
- Workspace-Limits eingehalten (kein ActiveMap in Lobby)

**Gate-Test**
1. Studio öffnen: beide Places laden ohne Errors
2. Play Solo: Lobby spawnt korrekt, Gameplace startet nicht „versehentlich“

**FAIL wenn**
- Gameplay Code in Lobby
- Fehlende Kern-Ordner / falsche Benennungen

---

## CP-00.10 — Remotes & SharedModules Skeleton
**Ziel:** Remote-Struktur vollständig angelegt, SharedModules-Basis vorhanden.  
**Deliverables**
- ReplicatedStorage/Remotes/… komplett angelegt (Lobby + Gameplace)
- SharedModules Root (z. B. Localization, Utils, Types)

**DoD**
- Remotes exakt nach Remote Spec / Remote List
- Keine „God-Remote“; Ordner getrennt (Match/Safe/Ui/Elimination/Training/Debug + Lobby)

**Gate-Test**
1. Remote Tree wird in Studio geprüft (Ordner + Namen)
2. Ein Testskript kann alle Remotes `WaitForChild` ohne Timeout

**FAIL wenn**
- Remote-Namen abweichen
- Lobby nutzt Gameplace-Remotes oder umgekehrt

---

## CP-00.20 — Config (server-only) + Loader
**Ziel:** ServerStorage/config/gameconfig.lua existiert und wird serverseitig gelesen.  
**Deliverables**
- gameconfig.lua mit Bereichen: match/safe/training/ui/dev
- Helper zum Lesen (z. B. config = require(...))

**DoD**
- Keine Magic Numbers in zentralen Loops (Countdown, door delays, etc.)
- Config bleibt server-only, read-only

**Gate-Test**
1. Server startet: `require(gameconfig)` ohne Errors
2. Ein Wert (z. B. match.doorcountdown) steuert sichtbar ein Test-Countdown

**FAIL wenn**
- Config im Client liegt
- Code nutzt Hardcoded Timings statt Config

---

## CP-00.30 — Dev-Tools Minimal (Studio/Admin gated)
**Ziel:** Debug-Tools nach Dev Tools Spec (Console over UI).  
**Deliverables**
- Debug Command Handler (Server) + optional Client Listener
- Nur: ForceChallengeRequest + SkipCountdownRequest (optional)

**DoD**
- Aktiv nur in Studio oder config.dev.enabled
- Adminuserids enforced (server geprüft)
- Feedback sichtbar (Toast/Log)

**Gate-Test**
1. Nicht-Admin: Commands haben keine Wirkung
2. Admin in Studio: `force <id>` setzt Override sichtbar, `clear` entfernt

**FAIL wenn**
- Dev-Tools Gameplay manipulieren (SAFE, Winner, Eliminate, Teleport, etc.)
- UI Buttons statt Console

---

## CP-00.40 — Baseline Logging & Error Handling
**Ziel:** Fehler führen zu kontrolliertem Abbruch + Rückkehr in Lobby (wo nötig).  
**Deliverables**
- Zentraler Logger (Server) mit Prefixes (GAMEPLACE/LOBBY)
- Safe-Fallback: invalid TeleportData → Return to Lobby

**Gate-Test**
1. Simuliere invalid TeleportData (leere Felder): Spieler wird sauber zurückgeschickt
2. Keine unhandled errors in Output

**FAIL wenn**
- Spieler stuckt ohne Feedback
- Hard-crash im MatchFlow

---

# Phase B — LOBBY Place (Entry)

## CP-10.00 — Lobby Map + Spawn + UI Shell
**Ziel:** Lobby ist spielbar als Entry-Hub (ohne Gameplay).  
**Deliverables**
- LobbyMap + Spawnpoints
- Minimal UI Frame (Titel, Leaderboard Bereich, Teleport Hinweise)

**Gate-Test**
1. Join: Spawn korrekt, Kamera ok
2. Keine Gameplace-Systeme laufen

**FAIL wenn**
- Challenges/ActiveMap in Lobby

---

## CP-10.10 — TeleportPads + TeleportService + TeleportData Contract
**Ziel:** Spieler können Multiplayer/Training auswählen und werden teleportiert.  
**Deliverables**
- TeleportPads (2 Modi)
- Lobby serverseitig: TeleportService attach TeleportData
- TeleportData enthält mindestens: mode, timestamp (+ optional party/training)

**Gate-Test**
1. Klick/Interact: TeleportStatus zeigt waiting → teleporting
2. Ankunft in GAMEPLACE mit korrektem mode

**FAIL wenn**
- Teleport ohne TeleportData
- Mode mismatch

---

## CP-10.20 — Lobby Leaderboard UI + Refresh
**Ziel:** Anzeige Top 100 All-Time & Daily, Player Summary Bar.  
**Deliverables**
- UI gemäß Lobby Leaderboard Spec
- Refresh alle 5 Minuten
- Zahlenformat 1.000

**Gate-Test**
1. Login lädt Boards
2. Nach Force Refresh (Dev) aktualisiert sich UI
3. Daily Reset Logik vorbereitet (00:00 UTC)

**FAIL wenn**
- Sortierung falsch (Rounds, Wins, Kings)
- Player summary fehlt oder inkonsistent

---

## CP-10.30 — Lobby Remotes + UX
**Ziel:** Lobby nutzt nur Lobby-Remotes, klare Feedback Toaster.  
**Deliverables**
- Lobby/TeleportStatus, LobbyToast, optional QueueState
- Keine Nutzung von Match/Safe/Elimination Remotes in Lobby

**Gate-Test**
1. Teleportstatus Remote-Flow korrekt
2. LobbyToast via server sendbar (Key-basiert, wenn Localization schon aktiv)

**FAIL wenn**
- Cross-place Remote misuse

---

# Phase C — GAMEPLACE Core

## CP-20.00 — Gameplace Bootstrap + TeleportData Validation
**Ziel:** GAMEPLACE akzeptiert nur gültige TeleportData; invalid → zurück zur Lobby.  
**Deliverables**
- Bootstrap liest TeleportData, validiert mode + timestamp
- Fehlermeldung + Return to Lobby

**Gate-Test**
1. Valid teleport: startet korrekt
2. Invalid teleport: Return to lobby, Toast/Info

**FAIL wenn**
- Match startet ohne gültigen mode

---

## CP-20.10 — PlayerState Core
**Ziel:** Spielerzustände sind serverseitig authoritative und replizieren via Remote.  
**Deliverables**
- PlayerState System (server)
- Remote: Match/PlayerState { state = active|safe|out|spectator }
- SpectatorState Remote vorbereitet

**Gate-Test**
1. Server setzt testweise state transitions; Client UI reagiert (Badge/Debug)
2. State kann nicht clientseitig gefaked werden

**FAIL wenn**
- Client kann State setzen
- State drift zwischen server/client

---

## CP-20.20 — Match Start Lock
**Ziel:** Match startet erst, wenn alle erwarteten Spieler da sind oder Timeout.  
**Deliverables**
- Expected players aus TeleportData (Party) oder Single
- Join timeout aus config.match

**Gate-Test**
1. Multi-Join: Match startet erst nach Arrival oder Timeout
2. Kein Challenge Load bevor Lock erfüllt

**FAIL wenn**
- Challenge loaded während Spieler noch joinen (race)

---

## CP-20.30 — MatchFlow Round Loop
**Ziel:** N Spieler → N-1 Runden, pro Runde max 1 Elimination.  
**Deliverables**
- MatchState/RoundState Remotes
- Round index + alivecount/outcount korrekt
- Challenge lifecycle hooks

**Gate-Test**
1. 3 Spieler: 2 Runden; 4 Spieler: 3 Runden
2. In jeder Runde max 1 Elimination (enforced)

**FAIL wenn**
- Mehr als 1 Elimination in einer Runde möglich
- RoundState/MatchState inkonsistent

---

## CP-20.40 — Abort/Forfeit Policy
**Ziel:** Active Leave Regeln und Abort-Regel umgesetzt.  
**Deliverables**
- LeaveMatchRequest (Client → Server) nur nach Confirmation UI (später CP-40.60)
- Forfeit handling: stats/RP verworfen bei active leave
- Abort: forfeits_in_current_round / RoundStartPlayers > 0.40 → match endet ohne ranked rewards

**Gate-Test**
1. Active leave in Runde: Forfeit zählt, Player wird entfernt/out
2. >40% leave: Match abort + Rückkehr zur Lobby nach Countdown
3. 1v1 start: leave → abort, kein winner

**FAIL wenn**
- Rewards/RP trotzdem vergeben werden
- 1v1 Sonderfall falsch

---

## CP-20.50 — Ranked Eligibility
**Ziel:** RP/Wins/Kings nur wenn mind. 1 echte Challenge-Elimination passiert.  
**Deliverables**
- Tracking challenge-elimination happened bool
- Match-End Checks: rankedEligible

**Gate-Test**
1. Match nur mit forfeits: kein RP/Wins/Kings
2. Match mit 1 elimination: ranked eligible

**FAIL wenn**
- Forfeit zählt als elimination
- Ranked rewards ohne elimination

---

# Phase D — Challenge System

## CP-30.00 — Challenge Loader
**Ziel:** Challenges werden aus ServerStorage/Challenges geladen/unloaded in Workspace/ActiveMap.  
**Deliverables**
- Loader: clone Map → Workspace/ActiveMap
- Unload: cleanup ActiveMap + disconnect signals

**Gate-Test**
1. Load/Unload 10× ohne Memory-Leaks (basic)
2. ActiveMap ist nach unload leer

**FAIL wenn**
- Instances bleiben hängen
- Multiple ActiveMaps existieren

---

## CP-30.10 — Challenge Interface Wiring
**Ziel:** Logic.Init(context), Start, Stop(reason) + Signals funktionieren.  
**Deliverables**
- Context beinhaltet: players, config, remotes, services, utilities
- Signals: PlayerSafe, Eliminate, Ended (alle server-only)

**Gate-Test**
1. Dummy challenge: ruft PlayerSafe für 1 Spieler → SAFE flow triggert
2. Dummy elimination: Eliminate(player,"reason") → OUT flow triggert
3. Ended → round beendet sauber

**FAIL wenn**
- Challenge kann direkt MatchFlow umgehen
- Signale werden clientseitig ausgelöst

---

## CP-30.20 — TeleportStart / Portal
**Ziel:** Wenn TeleportStart existiert, wird Portal genutzt (UI Spec).  
**Deliverables**
- Portal spawn/visual
- Player durchläuft → Server teleportiert zum Startpunkt

**Gate-Test**
1. Challenge mit TeleportStart: Portal erscheint, Teleport klappt
2. Challenge ohne TeleportStart: kein Portal

**FAIL wenn**
- Client teleportiert sich selbst
- Portal bleibt nach unload bestehen

---

## CP-30.30 — Challenge Template + 1 Dummy-Challenge
**Ziel:** Standard-Template für neue Challenges + Dummy zur Pipelineprüfung.  
**Deliverables**
- Ordner: NNN_Name/Map/Logic/Description
- Dummy-Challenge: simple safe condition + timed end

**Gate-Test**
1. Challenge wird via Random/Force geladen
2. Runde endet deterministisch

**FAIL wenn**
- Template nicht spec-konform

---

## CP-30.40 — Challenge QA Gate (cross-platform basic)
**Ziel:** Mindest-Playability PC/Mobile/Console (Controls Spec).  
**Gate-Test**
- PC: Movement/Jump ok, UI nicht blockierend
- Mobile: Joystick nicht überlagert, Jump erreichbar
- Console: UI-navigierbar (wo relevant)

**FAIL wenn**
- Mobile/Console hard-blocked

---

# Phase E — UI (Gameplace)

## CP-40.00 — Mode/State Badges
**Ziel:** Badges Training/Match/Spectator gemäß UI Spec.  
**Gate-Test**
1. ModeUi remote setzt Badge korrekt
2. PlayerState spectator ersetzt Match Badge

**FAIL wenn**
- Badge falsch oder doppelt

---

## CP-40.10 — InfoBoard (Challenge Intro)
**Ziel:** Slide-In/Out Intro zeigt ROUND X, Challenge-Name, Description.  
**Gate-Test**
1. Round start: InfoBoard kommt, geht raus, entfernt
2. Kein UI-Leak bei mehreren Runden

---

## CP-40.20 — Countdown (Numbers + optional Bar)
**Ziel:** Zentraler 3-2-1-START + optional countdown bar.  
**Gate-Test**
1. 3-2-1-START ohne 0, START 1.5s sichtbar
2. Bar: nur 1 gleichzeitig, richtig ablaufend

---

## CP-40.30 — Persistent Challenge Info + Task-Frames
**Ziel:** Links UI: ChallengeName, Description, dynamische Tasks (0..N).  
**Gate-Test**
1. Tasks add/update/remove funktionieren
2. Tasks verschwinden bei Round/Challenge-Ende

---

## CP-40.40 — SAFE UI
**Ziel:** SAFE-Pop, Rahmen, Liste, Crown für RoundKing.  
**Gate-Test**
1. SafeAwarded zeigt Pop (einmalig), Rahmen bleibt bis Rundenende
2. SafeLeaderboard Reihenfolge = SafeIndex, Crown nur SafeIndex==1

---

## CP-40.50 — OUT/Lose UI + Spectator UI
**Ziel:** OUT-Impact + Glas-Overlay, Spectator Anzeige und Wechselhinweise.  
**Gate-Test**
1. Eliminated → OUT Impact triggert einmal, Glas bleibt bis spectator
2. SpectatorState zeigt „SPECTATING: Name“ korrekt

---

## CP-40.60 — Leave UI (Active Leave Confirmation)
**Ziel:** Modal bei active/safe, sofortiger leave bei out/spectator.  
**Gate-Test**
1. active: Leave öffnet Modal, erst Bestätigung sendet LeaveMatchRequest
2. spectator: Leave geht sofort ohne Modal

**FAIL wenn**
- Request wird ohne Bestätigung gesendet (active)

---

# Phase F — Training Mode

## CP-50.00 — TrainingLoop State Machine
**Ziel:** Training läuft isoliert, kein Spectator, bewusster Start.  
**Gate-Test**
1. TrainingUi zeigt Start
2. StartRequest startet challenge, Stop/Restart klappt

---

## CP-50.10 — Training Picker/Random Toggle
**Ziel:** Toggle zwischen Picker und Random, PickerSelectRequest.  
**Gate-Test**
1. ModeToggleRequest toggelt korrekt
2. Picker zeigt challengelist, select lädt richtige challenge

---

## CP-50.20 — Training Result Screen
**Ziel:** WIN/LOSE/TIMEOUT + Zeit + Bestzeit+Datum.  
**Gate-Test**
1. Result wird nach Ended gesetzt
2. Bestzeit wird korrekt aktualisiert

---

## CP-50.30 — Training Persistence
**Ziel:** Bestzeiten pro Challenge speichern.  
**Gate-Test**
1. Rejoin: Bestzeit bleibt erhalten
2. Datenformat stabil

---

# Phase G — Progression

## CP-60.00 — Stats Model
**Ziel:** RoundsSurvived, MatchWins, RoundKings existieren.  
**Gate-Test**
1. End-of-match berechnet Werte korrekt
2. Leaderboard kann daraus lesen

---

## CP-60.10 — Match-End Persist (regulär vs abort vs forfeit)
**Ziel:** Rewards folgen Gameplay Spec.  
**Gate-Test**
1. Regulär: RoundsSurvived + evtl Wins/Kings
2. Abort: nur abgeschlossene Runden, kein RP/Wins/Kings
3. Forfeit (active): nichts aus dem Match zählt

---

## CP-60.20 — RankPoints + Season
**Ziel:** RP nur bei rankedEligible und regulär beendet; Season reset.  
**Gate-Test**
1. rankedEligible false: kein RP
2. seasonId change: RP reset wie rules

---

## CP-60.30 — Daily Reset (00:00 UTC) & Boards
**Ziel:** Daily Board resettet 00:00 UTC, AllTime bleibt.  
**Gate-Test**
1. Simulierter reset: Daily = 0, AllTime bleibt
2. UI zeigt korrekt

---

# Phase H — Localization

## CP-70.00 — Localizer Modul
**Ziel:** GetText(key,args) + Fallback EN.  
**Gate-Test**
1. Missing key → fallback + debug warn (optional)
2. args substitution funktioniert

---

## CP-70.10 — Remotes senden nur Keys (Minimal)
**Ziel:** Toast/InfoBoard/Countdown payloads enthalten Keys statt Text.  
**Gate-Test**
1. Server sendet Key, Client rendert Text
2. Keine hardcoded server strings

---

## CP-70.20 — Locked Terms
**Ziel:** SAFE/OUT/RoundKing/MatchWinner bleiben Englisch.  
**Gate-Test**
1. DE locale: Locked Terms bleiben unverändert
2. Rest ist übersetzt/fallback

---

## CP-70.30 — Minimal-Set für Launch fertig
**Ziel:** Alle in Spec genannten Texte laufen über Keys.  
**Gate-Test**
1. Training + Match + Abort Meldungen sind keybasiert
2. No-Gos nicht vorhanden

---

# Phase I — Content (Challenges)

## CP-80.00 — Challenge #1 (vollwertig)
**Ziel:** Erste echte Challenge inkl. Safe/Eliminate/Ended korrekt.  
**Gate-Test**
- Pro Runde max 1 elimination
- Cross-platform basic pass
- Keine Exploit-Inputs

---

## CP-80.10 — Challenge #2 (vollwertig)
**Gleiche Gates** wie #1.

## CP-80.20 — Challenge #3 (vollwertig)
**Gleiche Gates** wie #1.

## CP-80.30 — Challenge Pool Minimum + Balance
**Ziel:** Genug Content für Launch (z. B. 6–10).  
**Gate-Test**
1. Random selection fair, keine repeats zu häufig (wenn implementiert)
2. Average round length im Zielbereich (design)

---

## CP-80.40 — Exploit/Edge-Cases
**Ziel:** Stabilität bei Disconnects, AFK, Delay spikes, teleports.  
**Gate-Test**
1. Disconnect während active → Forfeit korrekt
2. Disconnect während out/spectator → Stats behalten
3. Massive leave → abort

---

# Phase J — Release Gates

## CP-90.00 — Performance & Memory
**Gate-Test**
- Load/Unload cycles ohne leak
- Remote spam protection (basic rate-limit, server validated)

## CP-90.10 — Cross-Platform Full Pass
**Gate-Test**
- PC/Mobile/Console komplette Session durchspielbar
- UI controller-navigierbar wo nötig

## CP-90.20 — Teleport Reliability
**Gate-Test**
- 50 Teleports (batch tests) ohne stuck
- invalid TeleportData handling sauber

## CP-90.30 — Final Regression Suite
**Gate-Test**
- Voller Matchlauf mit:
  - 1 elimination
  - 1 forfeit
  - 1 abort scenario
  - winner determination
- Leaderboards/RP korrekt

---

## 3) „Was sag ich dir im Chat?“ (Standard)
Du schreibst einfach:
- **Checkpoint:** CP-20.40  
- **Status:** PASS/FAIL  
- **Kurznotiz:** (z. B. „Abort greift, aber 1v1-start-Sonderfall noch falsch“)

Dann arbeite ich exakt ab dem nächsten offenen Punkt weiter.

---

## 4) Referenz-Specs (Quelle der Wahrheit)
- Project Structure / Multi-Place / Gameplace Architecture
- Remote Events Spec + Remote List
- Gameplay Spec (Leave/Abort/Eligibility)
- UI Spec
- Input & Controls Spec
- Points / Lobby Leaderboard / Rank
- Localization
- Dev Tools
- Config
- Coding Style
