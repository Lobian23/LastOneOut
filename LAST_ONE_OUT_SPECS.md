# LAST ONE OUT --- SPECS

# LAST ONE OUT --- PROJECT INDEX

Status: ACTIVE\
Updated: 2026-02-23

------------------------------------------------------------------------

## 🔴 CORE SPECS (Foundational --- Highest Authority)

1.  LAST_ONE_OUT_Gameplay_Spec.md\
2.  LAST_ONE_OUT_Gameplace_Architecture_Spec.md\
3.  LAST_ONE_OUT_Multi_Place_Spec.md\
4.  LAST_ONE_OUT_Project_Structure_Spec.md

Hierarchy: Gameplay \> Gameplace Architecture \> Multi-Place \>
Structure

------------------------------------------------------------------------

## 🟠 SYSTEM SPECS

-   LAST_ONE_OUT_UI_Spec.md\
-   LAST_ONE_OUT_Lobby_System_Spec.md\
-   LAST_ONE_OUT_Leaderboard_Spec.md\
-   LAST_ONE_OUT_Points_Spec.md\
-   LAST_ONE_OUT_Rank_Spec.md\
-   LAST_ONE_OUT_Training_Spec.md\
-   LAST_ONE_OUT_Localization_Spec.md\
-   LAST_ONE_OUT_Challenge_Architecture_Spec.md

------------------------------------------------------------------------

## 🟡 TECHNICAL SPECS

-   LAST_ONE_OUT_Remote_Spec.md\
-   LAST_ONE_OUT_Config_Spec.md\
-   LAST_ONE_OUT_Coding_Style.md\
-   LAST_ONE_OUT_Dev_Tools_Spec.md\
-   LAST_ONE_OUT_Codefiles_Spec.md

------------------------------------------------------------------------

## 🔒 GLOBAL RULES (Reminder)

-   Server is authoritative.\
-   Client renders only.\
-   No cross-place remote usage.\
-   TeleportData required for Gameplace start.\
-   UI text is Localization key-based.\
-   No magic numbers in gameplay logic (Config enforced).\
-   Gameplace entry point is exactly:\
    ServerScriptService/Gameplace/Init/Bootstrap.server.lua

------------------------------------------------------------------------

END OF INDEX

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Gameplay_Spec.md

# LAST ONE OUT --- Gameplay Spec

**Status:** ACTIVE\
**Updated:** 2026-02-23

------------------------------------------------------------------------

## Core Rule

-   Max 1 elimination per round.\
-   TotalRounds = StartPlayers - 1.

------------------------------------------------------------------------

## Player States (Server Only)

-   ACTIVE\
-   SAFE\
-   OUT\
-   SPECTATOR

------------------------------------------------------------------------

## Forfeit

Leaving while ACTIVE = Forfeit.

-   No RoundsSurvived\
-   No MatchWin\
-   No RoundKing\
-   No RP

------------------------------------------------------------------------

## Abort Rule

If forfeits_in_round / RoundStartPlayers \> 0.40 → Match aborts.


------------------------------------------------------------------------

## Ranked Eligibility

Match counts only if at least 1 Challenge-Elimination occurred.\
Forfeits do not count.

------------------------------------------------------------------------

## Stats

-   RoundsSurvived\
-   MatchWins\
-   RoundKings

------------------------------------------------------------------------

### RoundKing Definition

RoundKing is awarded once per round.

RoundKing is the player who first fulfills the\
challenge condition in that round.

The determination must be fully deterministic:

-   earliest server-validated timestamp\
-   if equal: lowest UserId

RoundKing:

-   has no influence on elimination logic\
-   has no influence on match flow\
-   has no influence on SAFE / OUT states\
-   has no influence on RP calculation\
-   is a purely statistical round-based award

If a challenge has no explicit completion condition\
(e.g. survival-based challenge), RoundKing is the player\
with the best valid performance metric defined by that challenge,\
determined deterministically.

## SAFE Definition:

**SAFE = jeder Spieler, der die Challenge-Bedingung in der aktuellen
Round erfüllt.**

-   SAFE wird **server-autoritativ** gesetzt, sobald die
    Challenge-Completion für den Spieler **server-validiert** wurde.
-   SAFE ist **round-basiert**: gilt für die aktuelle Round und wird
    beim Start der nächsten Round für nicht-OUT Spieler wieder auf
    **ACTIVE** zurückgesetzt (bestehende OUT/SPECTATOR Regeln bleiben
    unverändert).

> Hinweis: Diese Ergänzung ändert keine Punkte/RP-Regeln. SAFE ist
> weiterhin ein Gameplay-State (Server Only).

------------------------------------------------------------------------

## OUT Definition:

**OUT = der letzte Spieler, der die Challenge-Bedingung noch nicht
erfüllt hat.**

Das bedeutet:

-   Sobald **alle bis auf einen** Spieler die Bedingung erfüllt haben
    (SAFE), bleibt genau **ein** Spieler übrig, der die Bedingung **noch
    nicht** erfüllt hat.
-   Dieser letzte nicht-SAFE Spieler wird **OUT** gesetzt.
-   Damit gilt am Round-Ende: **alle Spieler sind entweder SAFE oder
    OUT**.

**Konsequenz:** Pro Round entsteht deterministisch **genau 1 OUT** → die
bestehende Core Rule *„Max 1 elimination per round"* bleibt erfüllt.

------------------------------------------------------------------------

END OF SPEC v2 (patched)

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Gameplace_Architecture_v3.md

# LAST ONE OUT --- Gameplace Architecture v3 (Bootstrap Entry Point + Module Topology)

**Status:** ACTIVE\
**Updated:** 2026-02-19\
**Authority:** Core Spec (Foundational)

This spec extends the Gameplace Architecture with an explicit
**bootstrap entry point** and the **exact module topology** required for
CP 2.1.01.

------------------------------------------------------------------------

## 1. Authority Model

-   **Server authoritative**. Client renders only.
-   UI never controls gameplay logic.
-   Remotes are validated server-side.

------------------------------------------------------------------------

## 2. TeleportData Requirement

TeleportData is required and must contain:

-   `mode`
-   `timestamp`

Invalid or missing TeleportData → return player(s) to Lobby.

------------------------------------------------------------------------

## 3. Match Start Lock (Reminder)

Match starts only when:

-   all expected players arrived, OR
-   join timeout reached

------------------------------------------------------------------------

## 4. Boot Pipeline (NEW --- Exact Entry Point)

### 4.1 Entry Point Script (Required)

Gameplace initialization MUST begin at exactly:

    ServerScriptService/Gameplace/Init/Bootstrap.server.lua

Rules:

-   MUST be a **ServerScript**
-   MUST run automatically on server start
-   MUST be a thin entry point: it **delegates** to Core modules
-   MUST NOT contain gameplay logic

### 4.2 Controller Module (Required)

Bootstrap delegates to:

    ServerScriptService/Gameplace/Core/GameplaceController.lua

Controller responsibilities:

-   initialize core services & dependencies
-   validate TeleportData (CP 2.1.03)
-   enforce Match Start Lock setup (CP 2.2.x)
-   wire StateMachine + MatchFlow + Systems
-   own the high-level lifecycle (Start/Shutdown hooks if used)

### 4.3 Required Module Skeletons (CP 2.1.01)

These modules MUST exist (even as stubs) to formalize topology:

    ServerScriptService/Gameplace/State/StateMachine.lua
    ServerScriptService/Gameplace/Match/MatchFlow.lua

------------------------------------------------------------------------

## 5. Module Responsibilities (High Level)

### 5.1 Bootstrap.server.lua (Init)

-   load shared config (`ServerStorage/Config/Config.lua`)
-   create controller
-   call controller `Start()` (or equivalent)

### 5.2 GameplaceController.lua (Core)

-   owns boot sequencing
-   owns dependency wiring
-   owns error boundaries and fallbacks (CP 2.1.04)

### 5.3 StateMachine.lua (State)

-   server-only state machine implementation
-   UPPERCASE state constants
-   transition validation & logging (Phase 3)

### 5.4 MatchFlow.lua (Match)

-   round lifecycle orchestration (Phase 4)
-   challenge lifecycle integration (Phase 4/5)

### 5.5 Systems/\* (Systems)

-   Elimination, Forfeit, Rank/Persistence, Training, etc.
-   server-only
-   never directly controlled by client

------------------------------------------------------------------------

## 6. Non-Goals for CP 2.1.01

CP 2.1.01 is structure-only. It MUST NOT include:

-   countdown logic
-   round logic
-   elimination logic
-   persistence writes
-   client-driven state changes

------------------------------------------------------------------------

END OF SPEC v3

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Multi_Place_Spec_v3.md

# LAST ONE OUT -- Multi-Place Spec v3 (Config Clarification)

Status: ACTIVE\
Updated: 2026-02-18

Two Places: - LOBBY - GAMEPLACE

------------------------------------------------------------------------

## Config Rule

There is a single shared config file:

ServerStorage/Config/Config.lua

Logical separation is enforced through scopes:

-   Config.lobby
-   Config.gameplace
-   Config.dev

No cross-scope config access allowed.

TeleportData remains the only cross-place communication method.

END OF SPEC v3

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Project_Structure_v4.md

# LAST ONE OUT --- Project Structure v4 (Gameplace Core Architecture Entry Point)

**Status:** ACTIVE\
**Updated:** 2026-02-19\
**Authority:** Core Spec (Foundational)

This document defines the **exact folder structure and file naming** for
the experience, including the **Gameplace Core Architecture entry
point** introduced for CP 2.1.01.

------------------------------------------------------------------------

## 0. Global Naming Rules (Enforced)

-   **PascalCase**: folders, scripts, functions, remotes
-   **lowercase**: variables
-   **UPPERCASE**: STATES constants
-   **Entry points** (boot scripts) are **ServerScripts** and must end
    with `.server.lua`

(See Coding Style Spec.)

------------------------------------------------------------------------

## 1. Single Config Architecture (Shared)

There is exactly ONE config file:

    ServerStorage/Config/Config.lua

Scopes inside Config.lua:

-   `Config.lobby`
-   `Config.gameplace`
-   `Config.dev`

Strict access:

-   Lobby code may only read `Config.lobby` (+ optional `Config.dev`)
-   Gameplace code may only read `Config.gameplace` (+ optional
    `Config.dev`)

------------------------------------------------------------------------

## 2. LOBBY --- Required Structure

    ServerStorage/
    └── Config/
        └── Config.lua

    ServerScriptService/
    └── Lobbyplace/
        └── (Lobby server scripts & modules)

    ReplicatedStorage/
    └── Remotes/
        └── Lobby/
            ├── TeleportRequest (RemoteEvent/RemoteFunction per implementation)
            ├── TeleportStatus
            └── LobbyToast

Lobby contains **no gameplay logic**.

------------------------------------------------------------------------

## 3. GAMEPLACE --- Required Structure (Exact)

### 3.1 Root

    ServerStorage/
    └── Config/
        └── Config.lua

    ServerScriptService/
    └── Gameplace/
        ├── Init/
        ├── Core/
        ├── Match/
        ├── State/
        └── Systems/

    ReplicatedStorage/
    └── Remotes/
        ├── Match/
        ├── Safe/
        ├── Elimination/
        ├── Ui/
        ├── Training/
        └── Debug/

### 3.2 Init (Entry Point)

**Exact file name (required):**

    ServerScriptService/Gameplace/Init/Bootstrap.server.lua

Rules:

-   MUST be a **ServerScript** (`.server.lua`)
-   MUST be the **only automatic entry point** for Gameplace
    initialization
-   MUST NOT contain gameplay logic (delegates to Core modules)

### 3.3 Core

**Exact file name (required):**

    ServerScriptService/Gameplace/Core/GameplaceController.lua

Responsibilities:

-   orchestrates initialization flow
-   owns top-level lifecycle hooks
-   delegates to Match / State / Systems

### 3.4 State

**Exact file name (required):**

    ServerScriptService/Gameplace/State/StateMachine.lua

Responsibilities:

-   states definition & transitions (Phase 3)
-   server authoritative only

### 3.5 Match

**Exact file name (required):**

    ServerScriptService/Gameplace/Match/MatchFlow.lua

Responsibilities:

-   match lifecycle orchestration (Phase 4+)
-   round lifecycle integration

### 3.6 Systems

Folder reserved for server-side subsystems (examples):

    ServerScriptService/Gameplace/Systems/
        EliminationSystem.lua
        ForfeitSystem.lua
        RankSystem.lua
        TrainingSystem.lua

These names are illustrative; **only the folder is mandated** at this
stage.

------------------------------------------------------------------------

## 4. Prohibitions (Hard Rules)

-   No scripts/modules in `ServerScriptService/Gameplace/` root (must
    live in subfolders)
-   No client-side gameplay authority
-   No cross-place remotes
-   No cross-scope config access
-   No hardcoded UI text (Localization keys only)

------------------------------------------------------------------------

END OF SPEC v4

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_UI_Spec_v2.md

# LAST ONE OUT -- UI Spec v2

Status: ACTIVE Updated: 2026-02-17

UI explains state --- never controls logic.

Includes: - Mode Badge - Round Counter - InfoBoard Intro - 3-2-1-START
Countdown - Persistent Challenge Info - SAFE UI (Pop, Frame, List,
Crown) - OUT Impact + Glass Overlay - Spectator UI - Leave Confirmation
Modal

All texts via Localization keys.

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Lobby_System_Spec_v2.md

# LAST ONE OUT -- Lobby System Spec v2

Status: FROZEN Updated: 2026-02-17

## Pads

-   3 Match Pads (min 3, max 10)
-   1 Training Pad
-   Independent

Countdown: 20s Full pad = instant STARTING

Glow intensity = players / 10 Pulse frequency constant.

## Leaderboards

-   ALL-TIME
-   DAILY Top 100 Refresh: 5 min No gameplay impact.
-   Stats: RoundsSurvived, MatchWins (tie-breaker),
    RoundKings(tie-breaker)

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Leaderboard_Spec_v2.md

# LAST ONE OUT -- Leaderboard Spec v2

Status: ACTIVE Updated: 2026-02-17

Sorting: 1. RoundsSurvived 2. MatchWins 3. RoundKings

DAILY: Resets 00:00 UTC Countdown Bar: \>60% Green 30--60% Yellow \<30%
Red

Hard color switches only.

Sorting defined in Points Spec (single source of truth).

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Points_Spec_v2.md

# LAST ONE OUT -- Points Spec v2

Status: ACTIVE Updated: 2026-02-17

Leaderboard based on: - RoundsSurvived - MatchWins (tie-breaker) -
RoundKings (tie-breaker)

No artificial point formula. No gameplay influence.

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Rank_Spec_v2.md

# LAST ONE OUT -- Rank Spec v2

Status: FROZEN Updated: 2026-02-17

RP granted only on: - Ranked-eligible matches - Regularly finished
matches

Season-based: - seasonId - rp

Legend division has RP floor.

\## Definitions

Regularly finished = Match completed without Abort.
RP granted only on matches that are:

\- Ranked-eligible

\- Regularly finished (no Abort)

Ranked-eligible is defined in Gameplay Spec.

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Training_Spec_v2.md

# LAST ONE OUT -- Training Spec v2

Status: ACTIVE Updated: 2026-02-17

-   Conscious start
-   No spectator
-   Picker or Random mode
-   Best time persistence
-   WIN / LOSE / TIMEOUT result
-   Training never modifies ranked stats.

------------------------------------------------------------------------

# SOURCE FILE MISSING: LAST_ONE_OUT_Localization_Spec_v3.1.md

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Challenge_Architecture_Spec_v2.md

# LAST ONE OUT --- Challenge Architecture Spec v1 (DE)

Status: DRAFT\
Authority: System Spec (Gameplay-konform)\
Ziel: Jede Challenge ist 1:1 austauschbar, ohne den MatchFlow zu
verändern.

------------------------------------------------------------------------

# 1. Grundprinzip

1.1 Eine Challenge ist ein server-autoritatives Gameplay-Modul.\
1.2 Eine Challenge darf den globalen Gameflow niemals kontrollieren.\
1.3 Eine Challenge darf ausschließlich ein Ergebnis melden.\
1.4 Jede Challenge muss vollständig austauschbar sein, ohne: -
MatchFlow-Code zu ändern - StateMachine-Code zu ändern -
EliminationSystem-Code zu ändern\
1.5 Es gilt immer: Maximal 1 Elimination pro Round.

------------------------------------------------------------------------

# 2. Absolute Verbote

Eine Challenge darf niemals:

2.1 Die StateMachine direkt aufrufen oder verändern.\
2.2 MatchFlow direkt manipulieren.\
2.3 SAFE vergeben.\
2.4 OUT setzen.\
2.5 Einen MatchWinner festlegen.\
2.6 Stats oder RP verändern.\
2.7 DataStores schreiben.\
2.8 Hardcoded UI-Strings senden.\
2.9 Globale States verändern.

------------------------------------------------------------------------

# 3. Erlaubte Aufgaben einer Challenge

3.1 Eigene interne Logik besitzen.\
3.2 Interne Timer verwenden.\
3.3 Server-seitige Validierung durchführen.\
3.4 Ereignisse erkennen (Fehler, Zeitablauf, Sturz etc.).\
3.5 Genau EIN Ergebnis melden.

------------------------------------------------------------------------

# 4. Ergebnisvertrag (ChallengeResult)

Eine Challenge darf exakt einmal folgendes Ergebnis liefern:

``` lua
{
    eliminatedUserId = number?, 
    reasonKey = string?,        
    reasonArgs = table?         
}
```

Regeln:

4.1 eliminatedUserId darf nur eine einzelne UserId enthalten.\
4.2 eliminatedUserId darf nil sein (keine Elimination).\
4.3 Mehrere Eliminations sind verboten.\
4.4 Bei Gleichzeitigkeit muss deterministisch entschieden werden.

------------------------------------------------------------------------

# 5. Pflicht-Interface

``` lua
Challenge = {}

function Challenge.Init(ctx) end
function Challenge.Start() end
function Challenge.Stop() end
function Challenge.OnResolved(callback) end
function Challenge.Destroy() end

return Challenge
```

------------------------------------------------------------------------

# 6. Methoden-Regeln

6.1 Init(ctx) - Wird einmal pro Round aufgerufen. - Initialisiert
interne Logik.

6.2 Start() - Startet die Challenge. - Wird genau einmal aufgerufen.

6.3 OnResolved(callback) - Registriert genau einen Callback. - Callback
MUSS exakt einmal aufgerufen werden.

6.4 Stop() - Muss jederzeit sicher sein. - Muss idempotent sein.

6.5 Destroy() - Muss alle Ressourcen aufräumen. - Muss auch nach Stop
sicher funktionieren.

------------------------------------------------------------------------

# 7. Kontext (ctx)

``` lua
ctx = {
    mode = "Training" | "Multiplayer",
    config = Config.gameplace,
    dev = Config.dev,
    roundIndex = number,
}
```

Regeln:

7.1 Nur Config.gameplace darf gelesen werden.\
7.2 Kein Cross-Scope-Zugriff.\
7.3 Kein Gameplay-State im ctx.

------------------------------------------------------------------------

# 8. Ablauf einer Challenge

## 8.1 Vorbereitung

1.  MatchFlow → ROUND_STARTING\
2.  Challenge wird ausgewählt\
3.  Neue Instanz wird erstellt\
4.  Init(ctx) + OnResolved(handler)\
5.  Countdown läuft

## 8.2 Aktivierung

6.  StateMachine → ROUND_ACTIVE\
7.  Start() wird aufgerufen\
8.  Challenge läuft

## 8.3 Auflösung

9.  Challenge ruft OnResolved mit ChallengeResult\
10. MatchFlow verarbeitet Ergebnis\
11. EliminationSystem setzt ggf. OUT\
12. StateMachine → ROUND_FINISHED

## 8.4 Cleanup

13. Stop()\
14. Destroy()\
15. MatchFlow entscheidet nächste Round oder Match-Ende

------------------------------------------------------------------------

# 9. Frühes Beenden

Eine Challenge muss stoppbar sein durch:

-   Match Abort\
-   Forfeit-Threshold\
-   Dev SkipCountdown\
-   Server Shutdown\
-   Training-Abbruch

Nach Stop darf kein Resolve mehr erfolgen.

------------------------------------------------------------------------

# 10. Determinismus-Regel

Bei mehreren gleichzeitigen Fehlern ist deterministische Auswahl
Pflicht:

-   Frühester Server-Timestamp\
-   Kleinste UserId\
-   Stabile Server-Reihenfolge

------------------------------------------------------------------------

# 11. UI-Regel

Challenge darf nur reasonKey und reasonArgs liefern.\
Keine Texte formatieren.\
Keine UI-States kontrollieren.

------------------------------------------------------------------------

# 12. Austauschbarkeits-Test

Eine Challenge ist korrekt, wenn nur die Registry geändert werden muss.\
Keine Änderungen an MatchFlow, StateMachine oder Systemen.

------------------------------------------------------------------------

# 13. Compliance Checkliste

☐ Interface vollständig\
☐ Kein StateMachine-Zugriff\
☐ Kein MatchFlow-Zugriff\
☐ Genau ein Resolve\
☐ Maximal eine Elimination\
☐ Stop sicher\
☐ Destroy sicher\
☐ Keine UI-Strings\
☐ Server-autoritativ

------------------------------------------------------------------------

END OF SPEC v1

=====================================================================
INTEGRATED UPDATE -- Challenge Structure & Naming Standard (v2)
=====================================================================

This version fully replaces the previous specification file. No previous
rules are removed. The following sections extend the architecture and
are now considered mandatory.

  ----------------------------------------------------
  1\. Challenge Folder Naming Convention (MANDATORY)
  ----------------------------------------------------

Each Challenge folder MUST follow this format:

    NNN ChallengeName

Where:

-   NNN = 3-digit zero-padded numeric ID
-   ChallengeName = human-readable developer title

Examples:

    001 Lava Floor
    002 Falling Platforms
    010 Moving Walls
    105 Boss Arena

Technical Rules:

-   The first three digits (NNN) are the ONLY authoritative identifier.
-   Game systems MUST identify challenges by ID only.
-   Systems MUST NOT rely on the full folder name.
-   The visible name is decorative only.

  --------------------------------
  2\. Mandatory Folder Structure
  --------------------------------

Each Challenge MUST contain:

-   A Folder named: Map
-   A StringValue named: Description

Optional:

-   A Part named: Start (outside of Map folder)

Structure Example:

    001 Lava Floor
        ├── Map (Folder)                [REQUIRED]
        ├── Description (StringValue)   [REQUIRED]
        ├── Start (Part)                [OPTIONAL]

  ---------------------
  3\. Map Folder Rule
  ---------------------

The Map folder MUST contain all Parts and Models required for the
Challenge.

The system assumes:

-   Everything inside Map belongs to the challenge environment.
-   No gameplay-critical parts exist outside Map, except the optional
    Start part.

  ---------------------
  4\. Start Part Rule
  ---------------------

If a Part named "Start" exists (outside Map):

-   Players are teleported to this Part
-   The Challenge begins at this location
-   The Starthouse is bypassed

If Start does not exist:

-   Default Starthouse spawn behavior applies

  ----------------------------------
  5\. Description StringValue Rule
  ----------------------------------

A StringValue named "Description" is mandatory.

Important:

-   The Value MUST contain a Localization Key
-   It MUST NOT contain raw text

Example:

    Description.Value = "CHALLENGE_001_DESC"

UI must resolve:

    CHALLENGE_001_NAME
    CHALLENGE_001_DESC

The UI MUST NOT read the folder name directly.

  -----------------------------
  6\. Loader Responsibilities
  -----------------------------

The Challenge Loader MUST:

1.  Extract first 3 characters from folder name
2.  Validate that they are numeric
3.  Ensure ID uniqueness
4.  Ensure Map folder exists
5.  Ensure Description exists
6.  Preload challenge BEFORE doorCountdown reaches 0

The challenge must be loaded during ROUND_STARTING so the Infoboard
above the Starthouse door can display:

-   Challenge Name
-   Challenge Description

The challenge logic itself must only activate when ROUND_ACTIVE begins.

  --------------------------
  7\. Stability Guarantees
  --------------------------

Because the ID is authoritative:

-   Names may change safely
-   Localization may change safely
-   Folder ordering may change safely
-   Leaderboards remain stable
-   Rewards remain stable
-   Unlock systems remain stable
-   Statistics remain stable

8)  

-   Eine Challenge wird nach Workspace/Challenge geklont um sie in den
    Gameplace zu bringen.

-   Bevor eine neue Challenge gewählt wird, muss kontrolliert werden, ob
    diese schon einmal in diesem Match gespielt wurde.

-   KEINE CHALLENGE DARF 2x gespielt werden!

      ------------------------------
      END OF UPDATED SPECIFICATION
      ------------------------------

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Remote_Spec_v2.md

# LAST ONE OUT -- Remote Spec v2

Status: ACTIVE Updated: 2026-02-17

## Lobby Remotes

ReplicatedStorage/Remotes/Lobby/

Client → Server: TeleportRequest { mode }

Server → Client: TeleportStatus { state } LobbyToast { key, args }

Server behavior:

\- On TeleportRequest { mode }, the Lobby server constructs
TeleportData:

  { mode, timestamp }

\- The Lobby server uses TeleportData for the place teleport into
GAMEPLACE.

## Gameplace Remotes

Folders: Match / Safe / Elimination / Ui / Training / Debug

UI remotes send KEYS only. Client sends requests only. Server validates
all.

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Config_Spec_v3.md

# LAST ONE OUT -- Config Spec v3

Status: ACTIVE\
Updated: 2026-02-18

## Single Config Architecture

There is exactly ONE config file:

ServerStorage/Config/Config.lua

No additional config files are allowed.

------------------------------------------------------------------------

## Structure (Mandatory)

The Config.lua must be structured as follows:

-   Config.lobby
-   Config.gameplace
-   Config.dev

Example structure:

    local Config = {

        lobby = {
            -- Lobby specific values
        },

        gameplace = {
            -- Gameplace specific values
        },

        dev = {
            enabled = false
        }
    }

    return Config

------------------------------------------------------------------------

## Access Rules (Strict)

-   Lobby code may only read: Config.lobby (+ optional Config.dev)
-   Gameplace code may only read: Config.gameplace (+ optional
    Config.dev)
-   Cross-scope access is forbidden.

------------------------------------------------------------------------

## Global Rules

-   Server-only
-   Read-only
-   No magic numbers in gameplay logic
-   Config must never contain gameplay state

END OF SPEC v3

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Coding_Style_v3.md

# LAST ONE OUT --- Coding Style v3 (Entry Points & File Naming)

**Status:** ACTIVE\
**Updated:** 2026-02-19\
**Authority:** Technical Spec (Enforced)

This spec defines naming and style enforcement across the codebase,
including **entry point conventions** for the Gameplace bootstrap.

------------------------------------------------------------------------

## 1. Naming Conventions (Mandatory)

### 1.1 PascalCase

Use **PascalCase** for:

-   Folders
-   Scripts (ServerScripts & LocalScripts)
-   ModuleScripts
-   Functions
-   Remotes (Instances and folders)

### 1.2 Variables

Use **lowercase** for variables.

### 1.3 STATES

Use **UPPERCASE** for state constants and state keys.

------------------------------------------------------------------------

## 2. Authority Rule (Mandatory)

-   **Server decides. Client renders.**
-   Client may only:
    -   request actions
    -   render UI
-   Server must validate all requests.

------------------------------------------------------------------------

## 3. Entry Point Rule (NEW --- Mandatory)

### 3.1 What is an Entry Point?

An entry point is a script that must **run automatically** on server
start to bootstrap a place.

### 3.2 Entry Point Type

-   Entry points MUST be **ServerScripts**
-   Entry points MUST NOT return a module table (no `return` needed)

### 3.3 Required Gameplace Entry Point Name

The Gameplace entry point MUST be exactly:

    Bootstrap

at:

    ServerScriptService/Gameplace/Init/Bootstrap

------------------------------------------------------------------------

## 4. Module Pattern (Recommended)

-   ModuleScripts return a table of functions.
-   ServerScripts are thin and delegate to modules.

------------------------------------------------------------------------

END OF SPEC v3

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Dev_Tools_Spec_v2.md

# LAST ONE OUT -- Dev Tools Spec v2

Status: ACTIVE Updated: 2026-02-17

Active only in: - Studio OR - config.dev.enabled

Console-based only.

Allowed: - ForceChallenge - SkipCountdown

Never allowed: - Grant SAFE - Eliminate player - Set winner - Modify
gameplay states

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_Codefiles_Spec.md

# LAST ONE OUT --- Codefiles Spec (Fully Self-Contained Canon)

Status: ACTIVE\
Authority: Technical Spec (Absolute Source of Truth)\
Self-Contained: YES (MANDATORY)

------------------------------------------------------------------------

# 0. CANONICAL FILE INDEX (EXPLICIT & NUMBERED)

## SHARED --- Localization (2)

1.  ReplicatedStorage/Localization/LocalizationKeys.lua FREEZED
2.  ReplicatedStorage/Localization/Localizer.lua FREEZED
3.  ReplicatedStorage/Localization/EN.lua FREEZED
4.  ReplicatedStorage/Localization/DE.lua FREEZED

## GAMEPLACE --- Entry & Core (4)

5.  ServerScriptService/Gameplace/Init/Bootstrap.server.lua FREEZED

6.  ServerScriptService/Gameplace/Core/GameplaceController.lua FREEZED

7.  ServerScriptService/Gameplace/State/StateMachine.lua FREEZED

8.  ServerScriptService/Gameplace/Match/MatchFlow.lua FREEZED \##
    GAMEPLACE --- Systems (2)

9.  ServerScriptService/Gameplace/Systems/RemoteValidationService.lua
    FREEZED

10. ServerScriptService/Gameplace/Systems/TeleportDataService.lua
    FREEZED

## SHARED --- Config (1)

11. ServerStorage/Config/Config.lua FREEZED

TOTAL: 11 CANONICAL FILES

------------------------------------------------------------------------

# 1. SHARED PACKAGE --- Localization

## 1.1 ReplicatedStorage/Localization/LocalizationKeys.lua

``` lua
-- ReplicatedStorage/Localization/LocalizationKeys.lua
-- Single source of truth for ALL localization keys.

local LocalizationKeys = {}

LocalizationKeys.Keys = {

    -- LOCKED TERMS (never translated)
    LOCK_SAFE = "LOCK_SAFE",
    LOCK_OUT = "LOCK_OUT",
    LOCK_ROUND_KING = "LOCK_ROUND_KING",

    -- GLOBAL / UI
    UI_MODE_RANKED = "UI_MODE_RANKED",
    UI_MODE_CASUAL = "UI_MODE_CASUAL",
    UI_MODE_TRAINING = "UI_MODE_TRAINING",

    UI_YES = "UI_YES",
    UI_NO = "UI_NO",
    UI_CONFIRM = "UI_CONFIRM",
    UI_CANCEL = "UI_CANCEL",
    UI_CLOSE = "UI_CLOSE",
    UI_BACK = "UI_BACK",
    UI_CONTINUE = "UI_CONTINUE",

    -- MATCH FLOW
    MATCH_WAITING_FOR_PLAYERS = "MATCH_WAITING_FOR_PLAYERS",
    MATCH_STARTING = "MATCH_STARTING",
    MATCH_STARTED = "MATCH_STARTED",
    MATCH_ABORTED = "MATCH_ABORTED",
    MATCH_FINISHED = "MATCH_FINISHED",

    MATCH_ABORT_REASON_FORFEIT_RATIO = "MATCH_ABORT_REASON_FORFEIT_RATIO",
    MATCH_ABORT_REASON_INVALID_TELEPORT = "MATCH_ABORT_REASON_INVALID_TELEPORT",

    MATCH_WINNER_ANNOUNCEMENT = "MATCH_WINNER_ANNOUNCEMENT",
    MATCH_ROUND_KING_ANNOUNCEMENT = "MATCH_ROUND_KING_ANNOUNCEMENT",

    -- ROUND SYSTEM
    ROUND_COUNTER = "ROUND_COUNTER",
    ROUND_STARTING = "ROUND_STARTING",
    ROUND_ACTIVE = "ROUND_ACTIVE",
    ROUND_FINISHED = "ROUND_FINISHED",
    ROUND_ADVANCING = "ROUND_ADVANCING",

    COUNTDOWN_3 = "COUNTDOWN_3",
    COUNTDOWN_2 = "COUNTDOWN_2",
    COUNTDOWN_1 = "COUNTDOWN_1",
    COUNTDOWN_START = "COUNTDOWN_START",

    -- SAFE / OUT / SPECTATOR
    PLAYER_SAFE = "PLAYER_SAFE",
    PLAYER_OUT = "PLAYER_OUT",
    PLAYER_SPECTATOR = "PLAYER_SPECTATOR",

    SAFE_POPUP = "SAFE_POPUP",
    SAFE_CROWN = "SAFE_CROWN",
    SAFE_LIST_ENTRY = "SAFE_LIST_ENTRY",

    OUT_IMPACT = "OUT_IMPACT",
    OUT_SPECTATING = "OUT_SPECTATING",

    SPECTATOR_MODE_ACTIVE = "SPECTATOR_MODE_ACTIVE",
    SPECTATOR_WAITING_NEXT_ROUND = "SPECTATOR_WAITING_NEXT_ROUND",

    -- LEAVE / FORFEIT
    LEAVE_CONFIRM_TITLE = "LEAVE_CONFIRM_TITLE",
    LEAVE_CONFIRM_BODY = "LEAVE_CONFIRM_BODY",
    LEAVE_CONFIRM_ACTIVE_WARNING = "LEAVE_CONFIRM_ACTIVE_WARNING",
    LEAVE_CONFIRM_SPECTATOR = "LEAVE_CONFIRM_SPECTATOR",
    LEAVE_BUTTON = "LEAVE_BUTTON",

    FORFEIT_NOTICE = "FORFEIT_NOTICE",
    FORFEIT_NO_REWARD = "FORFEIT_NO_REWARD",

    -- STATS / LEADERBOARD
    STATS_ROUNDS_SURVIVED = "STATS_ROUNDS_SURVIVED",
    STATS_MATCH_WINS = "STATS_MATCH_WINS",
    STATS_ROUND_KINGS = "STATS_ROUND_KINGS",

    LEADERBOARD_ALL_TIME = "LEADERBOARD_ALL_TIME",
    LEADERBOARD_DAILY = "LEADERBOARD_DAILY",
    LEADERBOARD_REFRESHING = "LEADERBOARD_REFRESHING",
    LEADERBOARD_POSITION = "LEADERBOARD_POSITION",
    LEADERBOARD_EMPTY = "LEADERBOARD_EMPTY",

    -- RANK SYSTEM
    RANK_CURRENT = "RANK_CURRENT",
    RANK_RP = "RANK_RP",
    RANK_SEASON = "RANK_SEASON",

    RANK_RP_GAINED = "RANK_RP_GAINED",
    RANK_RP_LOST = "RANK_RP_LOST",
    RANK_RP_NOT_ELIGIBLE = "RANK_RP_NOT_ELIGIBLE",
    RANK_LEGEND_FLOOR = "RANK_LEGEND_FLOOR",

    -- TRAINING
    TRAINING_MODE_BADGE = "TRAINING_MODE_BADGE",
    TRAINING_START = "TRAINING_START",
    TRAINING_PICKER_MODE = "TRAINING_PICKER_MODE",
    TRAINING_RANDOM_MODE = "TRAINING_RANDOM_MODE",

    TRAINING_RESULT_WIN = "TRAINING_RESULT_WIN",
    TRAINING_RESULT_LOSE = "TRAINING_RESULT_LOSE",
    TRAINING_RESULT_TIMEOUT = "TRAINING_RESULT_TIMEOUT",

    TRAINING_BEST_TIME = "TRAINING_BEST_TIME",
    TRAINING_NEW_BEST_TIME = "TRAINING_NEW_BEST_TIME",

    -- LOBBY
    LOBBY_WELCOME = "LOBBY_WELCOME",
    LOBBY_MATCH_PAD = "LOBBY_MATCH_PAD",
    LOBBY_TRAINING_PAD = "LOBBY_TRAINING_PAD",

    LOBBY_PAD_PLAYERS = "LOBBY_PAD_PLAYERS",
    LOBBY_PAD_COUNTDOWN = "LOBBY_PAD_COUNTDOWN",
    LOBBY_PAD_FULL_STARTING = "LOBBY_PAD_FULL_STARTING",

    LOBBY_TELEPORTING = "LOBBY_TELEPORTING",
    LOBBY_TELEPORT_FAILED = "LOBBY_TELEPORT_FAILED",
    LOBBY_RETURNED_FROM_GAME = "LOBBY_RETURNED_FROM_GAME",

    -- SYSTEM
    SYSTEM_TELEPORT_INVALID = "SYSTEM_TELEPORT_INVALID",
    SYSTEM_JOIN_TIMEOUT = "SYSTEM_JOIN_TIMEOUT",
    SYSTEM_EXPECTED_PLAYERS_MISSING = "SYSTEM_EXPECTED_PLAYERS_MISSING",
    SYSTEM_SERVER_ERROR = "SYSTEM_SERVER_ERROR",

    -- DEV
    DEV_FORCE_CHALLENGE = "DEV_FORCE_CHALLENGE",
    DEV_SKIP_COUNTDOWN = "DEV_SKIP_COUNTDOWN",
    DEV_MODE_ACTIVE = "DEV_MODE_ACTIVE",
    DEV_COMMAND_INVALID = "DEV_COMMAND_INVALID",
    DEV_NOT_ALLOWED = "DEV_NOT_ALLOWED",

    -- TOAST
    TOAST_MATCH_STARTING = "TOAST_MATCH_STARTING",
    TOAST_MATCH_ABORTED = "TOAST_MATCH_ABORTED",
    TOAST_RANK_NOT_ELIGIBLE = "TOAST_RANK_NOT_ELIGIBLE",
    TOAST_TELEPORT_ERROR = "TOAST_TELEPORT_ERROR",

    -- FLAVOR
    FLAVOR_LAST_ONE_STANDING = "FLAVOR_LAST_ONE_STANDING",
    FLAVOR_TRY_AGAIN = "FLAVOR_TRY_AGAIN",
    FLAVOR_OOF = "FLAVOR_OOF",
    FLAVOR_THAT_WAS_CLOSE = "FLAVOR_THAT_WAS_CLOSE",
}

return LocalizationKeys
```

## 1.2 ReplicatedStorage/Localization/Localizer.lua

``` lua
-- ReplicatedStorage/Localization/Localizer.lua

-- ANWENDUNGSBEISPIEL:
-- print(Localizer.Translate("MATCH_WINNER_ANNOUNCEMENT", { player = "Valentina" }))

local Players = game:GetService("Players")

local EN = require(script.Parent.EN)
local DE = require(script.Parent.DE)

local Localizer = {}

Localizer.Supported = {
    EN = true,
    DE = true,
}

Localizer.FallbackLanguage = "EN"

-- Simple per-client language (you can wire this to player settings later)
local currentLanguage = Localizer.FallbackLanguage

local function GetDictForLanguage(language)
    if language == "DE" then
        return DE
    end
    return EN
end

local function FormatPlaceholders(text, args)
    if type(text) ~= "string" then
        return ""
    end

    if type(args) ~= "table" then
        return text
    end

    -- Replace {name} with args.name
    return (text:gsub("{([%w_]+)}", function(token)
        local value = args[token]
        if value == nil then
            return "{" .. token .. "}"
        end
        return tostring(value)
    end))
end

function Localizer.SetLanguage(language)
    if type(language) ~= "string" then
        currentLanguage = Localizer.FallbackLanguage
        return
    end

    language = string.upper(language)

    if Localizer.Supported[language] then
        currentLanguage = language
    else
        currentLanguage = Localizer.FallbackLanguage
    end
end

function Localizer.GetLanguage()
    return currentLanguage
end

-- Returns localized string for a given key.
-- key: string
-- args: optional table for placeholders e.g. { player = "Alex", amount = 10 }
function Localizer.Translate(key, args)
    if type(key) ~= "string" or key == "" then
        return ""
    end

    local dict = GetDictForLanguage(currentLanguage)
    local fallback = GetDictForLanguage(Localizer.FallbackLanguage)

    local text = dict[key]
    if text == nil then
        text = fallback[key]
    end

    -- If missing in both, return the key itself (useful for debugging)
    if text == nil then
        text = key
    end

    return FormatPlaceholders(text, args)
end

-- Convenience: picks language from Roblox locale (optional usage)
-- If you want STRICT manual selection, just never call this.
function Localizer.AutoSetFromRobloxLocale()
    local player = Players.LocalPlayer
    if not player then
        return
    end

    -- Roblox locale example: "de-de", "en-us"
    local localeId = player.LocaleId or ""
    localeId = string.lower(localeId)

    if localeId:sub(1, 2) == "de" then
        Localizer.SetLanguage("DE")
    else
        Localizer.SetLanguage("EN")
    end
end

return Localizer
```

## 1.3 ReplicatedStorage/Localization/EN.lua

``` lua
local EN = {}
return EN
```

## 1.4 ReplicatedStorage/Localization/DE.lua

``` lua
local DE = {}
return DE
```

------------------------------------------------------------------------

# 2. GAMEPLACE --- Entry & Core

## 2.1 ServerScriptService/Gameplace/Init/Bootstrap.server.lua

``` lua
local ServerStorage = game:GetService("ServerStorage")
local Config = require(ServerStorage.Config.Config)
local GameplaceController = require(script.Parent.Parent.Core.GameplaceController)

local deps = { config = Config }

GameplaceController.Start(deps)
```

## 2.2 ServerScriptService/Gameplace/Core/GameplaceController.lua

``` lua
--!strict

local Players = game:GetService("Players")

local TeleportDataService = require(script.Parent.Parent.Systems.TeleportDataService)
local StateMachine = require(script.Parent.Parent.State.StateMachine)
local MatchFlow = require(script.Parent.Parent.Match.MatchFlow)

local GameplaceController = {}

local function ValidateDeps(deps: any)
    if deps == nil then
        error("Missing deps")
    end

    if typeof(deps) ~= "table" then
        error("Deps is not a table")
    end

    if deps.config == nil then
        error("Missing config")
    end

    if typeof(deps.config) ~= "table" then
        error("Config is not a table")
    end

    if deps.config.gameplace == nil then
        error("Missing config.gameplace")
    end

    if typeof(deps.config.gameplace) ~= "table" then
        error("Config.gameplace is not a table")
    end
end

local function WaitForFirstPlayer(): Player
    local firstPlayer = Players:GetPlayers()[1]
    if firstPlayer then
        return firstPlayer
    end
    return Players.PlayerAdded:Wait()
end

local function CreateMatchStartLock(ctx: any, config: any)
    local joinTimeoutSeconds = 10
    if config and config.matchStartLock and typeof(config.matchStartLock) == "table" then
        if typeof(config.matchStartLock.joinTimeoutSeconds) == "number" then
            joinTimeoutSeconds = config.matchStartLock.joinTimeoutSeconds
        end
    end

    local startLock = {
        mode = ctx.mode,
        createdAt = os.clock(),
        arrivedUserIds = {} :: { [number]: boolean },
        unlocked = false,
        reason = nil :: (string?),
        joinTimeoutSeconds = joinTimeoutSeconds,
    }

    local function MarkArrived(player: Player)
        startLock.arrivedUserIds[player.UserId] = true
    end

    local function TimeoutReached(): boolean
        return (os.clock() - startLock.createdAt) >= startLock.joinTimeoutSeconds
    end

    print("[MatchStartLock] Waiting for players. Timeout:", startLock.joinTimeoutSeconds, "Mode:", tostring(startLock.mode))

    -- Track arrivals until unlock.
    local playerAddedConn = Players.PlayerAdded:Connect(function(p)
        MarkArrived(p)
        print("[MatchStartLock] Player arrived:", p.UserId, p.Name)

        -- If already unlocked, this is a late joiner (handled later via config.safety.kickLateJoiners, etc.)
        if startLock.unlocked then
            print("[MatchStartLock] Late joiner:", p.UserId, p.Name)
        end
    end)

    -- Mark currently present players (including first player).
    for _, p in ipairs(Players:GetPlayers()) do
        MarkArrived(p)
    end

    -- Unlock rules:
    -- Training: unlock immediately once we have the first player (already true here)
    -- Multiplayer: unlock on timeout (since TeleportData currently doesn't include an explicit expected player list)
    while not startLock.unlocked do
        if startLock.mode == "Training" then
            startLock.unlocked = true
            startLock.reason = "ALL_ARRIVED"
            break
        end

        if TimeoutReached() then
            startLock.unlocked = true
            startLock.reason = "TIMEOUT"
            break
        end

        task.wait(0.1)
    end

    playerAddedConn:Disconnect()

    print("[MatchStartLock] Unlocked. Reason:", tostring(startLock.reason))
    return startLock
end

function GameplaceController.Start(deps: any)
    local pcallAny = (pcall :: any)

    local ok, result = pcallAny(function()
        GameplaceController._StartInternal(deps)
    end)

    if not ok then
        warn("[GameplaceController] FATAL START ERROR: " .. tostring(result))
    end
end

function GameplaceController._StartInternal(deps: any)
    ValidateDeps(deps)

    print("[GameplaceController] Starting...")

    local config = deps.config.gameplace
    local devConfig = deps.config.dev or {} -- nil-safe

    -- Wait for first player
    local firstPlayer = WaitForFirstPlayer()

    -- Resolve TeleportData mode (or Studio fallback)
    local ok, resolveResultOrErr = TeleportDataService.ResolveMode(firstPlayer)
    if not ok then
        error("[GameplaceController] Invalid TeleportData: " .. tostring(resolveResultOrErr))
    end

    local result = (resolveResultOrErr :: any)

    local ctx = {
        config = config,
        dev = devConfig,
        mode = result.mode,
        teleportData = result.teleportData,
        isStudioFallback = result.isStudioFallback,
    }

    -- CP 2.2: Match Start Lock
    CreateMatchStartLock(ctx, config)

    -- Init downstream modules (stubs safe)
    if typeof(StateMachine.Init) == "function" then
        StateMachine.Init(ctx)
    end

    if typeof(MatchFlow.Init) == "function" then
        MatchFlow.Init(ctx)
    end

    print("[GameplaceController] Started in mode:", tostring(ctx.mode))
end

return GameplaceController
```

------------------------------------------------------------------------

## 2.3 ServerScriptService/Gameplace/State/StateMachine.lua

``` lua
--!strict

local StateMachine = {}

type StateMap = { [string]: string }

local STATES: StateMap = {
    WAITING_FOR_PLAYERS = "WAITING_FOR_PLAYERS",
    MATCH_STARTING = "MATCH_STARTING",

    ROUND_STARTING = "ROUND_STARTING",
    ROUND_ACTIVE = "ROUND_ACTIVE",
    ROUND_FINISHED = "ROUND_FINISHED",

    MATCH_FINISHED = "MATCH_FINISHED",
    MATCH_ABORTED = "MATCH_ABORTED",
}

StateMachine.STATES = table.freeze(STATES)

type LogData = { [string]: any }
type LoggerFn = (eventName: string, data: LogData) -> ()

local loggingEnabled: boolean = false
local loggerFn: LoggerFn? = nil

-- IMPORTANT:
-- TrySetState is ALWAYS soft (never asserts).
-- SetState can be strict depending on this flag.
local strictSetState: boolean = false

local function _log(eventName: string, data: LogData)
    if not loggingEnabled then
        return
    end

    if loggerFn ~= nil then
        loggerFn(eventName, data)
        return
    end

    print(("[StateMachine] %s"):format(eventName), data)
end

function StateMachine.SetLoggingEnabled(enabled: boolean)
    loggingEnabled = enabled
end

function StateMachine.SetLogger(fn: LoggerFn?)
    loggerFn = fn
end

-- Optional: allow strict SetState in Studio/dev, without affecting TrySetState
function StateMachine.SetStrictSetStateEnabled(enabled: boolean)
    strictSetState = enabled
end

-- =========================
-- Validation helpers
-- =========================

local STATE_SET: { [string]: boolean } = {}
do
    for _, stateValue in pairs(StateMachine.STATES) do
        STATE_SET[stateValue] = true
    end
end

function StateMachine.IsValidState(state: string?): boolean
    if state == nil then
        return false
    end
    return STATE_SET[state] == true
end

function StateMachine.AssertValidState(state: string, context: string?)
    if not StateMachine.IsValidState(state) then
        _log("invalid_state", {
            state = state,
            context = context,
            currentState = StateMachine.GetState(),
        })

        assert(false, ("Invalid state value%s: %s"):format(context and (" (" .. context .. ")") or "", tostring(state)))
    end
end

function StateMachine.GetAllStates(): { string }
    local list = {}
    for stateValue in pairs(STATE_SET) do
        table.insert(list, stateValue)
    end
    table.sort(list)
    return list
end

-- =========================
-- Transition rules
-- =========================

type TransitionMap = { [string]: { [string]: boolean } }

local ALLOWED_TRANSITIONS: TransitionMap = {
    [StateMachine.STATES.WAITING_FOR_PLAYERS] = {
        [StateMachine.STATES.MATCH_STARTING] = true,
        [StateMachine.STATES.MATCH_ABORTED] = true,
    },

    [StateMachine.STATES.MATCH_STARTING] = {
        [StateMachine.STATES.ROUND_STARTING] = true,
        [StateMachine.STATES.MATCH_ABORTED] = true,
    },

    [StateMachine.STATES.ROUND_STARTING] = {
        [StateMachine.STATES.ROUND_ACTIVE] = true,
        [StateMachine.STATES.MATCH_ABORTED] = true,
    },

    [StateMachine.STATES.ROUND_ACTIVE] = {
        [StateMachine.STATES.ROUND_FINISHED] = true,
        [StateMachine.STATES.MATCH_ABORTED] = true,
    },

    [StateMachine.STATES.ROUND_FINISHED] = {
        [StateMachine.STATES.ROUND_STARTING] = true,
        [StateMachine.STATES.MATCH_FINISHED] = true,
        [StateMachine.STATES.MATCH_ABORTED] = true,
    },

    -- terminal
    [StateMachine.STATES.MATCH_FINISHED] = {},
    [StateMachine.STATES.MATCH_ABORTED] = {},
}

for fromState, toMap in pairs(ALLOWED_TRANSITIONS) do
    ALLOWED_TRANSITIONS[fromState] = table.freeze(toMap)
end

StateMachine.ALLOWED_TRANSITIONS = table.freeze(ALLOWED_TRANSITIONS)

-- =========================
-- Runtime state
-- =========================

local currentState: string? = nil
local previousState: string? = nil
local enteredAt: number? = nil
local ctxRef: any = nil

local changedEvent: BindableEvent = Instance.new("BindableEvent")

function StateMachine.Init(ctx: any)
    ctxRef = ctx

    local runService = game:GetService("RunService")
    if runService:IsStudio() then
        loggingEnabled = true
    end

    -- Default: do NOT force strict in Studio.
    -- You can enable strict SetState manually if you want, but TrySetState stays soft always.
    -- strictSetState = true  -- optional

    StateMachine.SetState(StateMachine.STATES.WAITING_FOR_PLAYERS)
end

function StateMachine.GetState(): string?
    return currentState
end

function StateMachine.GetPreviousState(): string?
    return previousState
end

function StateMachine.GetEnteredAt(): number?
    return enteredAt
end

function StateMachine.CanTransition(fromState: string?, toState: string): boolean
    if not StateMachine.IsValidState(toState) then
        return false
    end

    if fromState == nil then
        return toState == StateMachine.STATES.WAITING_FOR_PLAYERS
    end

    if not StateMachine.IsValidState(fromState) then
        return false
    end

    local allowedFrom = StateMachine.ALLOWED_TRANSITIONS[fromState]
    if allowedFrom == nil then
        return false
    end

    return allowedFrom[toState] == true
end

-- =========================
-- CP 3.2.01+: Soft setter (never asserts on invalid transitions)
-- =========================

function StateMachine.TrySetState(nextState: string, payload: any?): (boolean, string?)
    StateMachine.AssertValidState(nextState, "TrySetState(nextState)")

    -- Idempotent no-op
    if currentState == nextState then
        _log("state_noop", {
            state = nextState,
            payload = payload,
        })
        return true, nil
    end

    if not StateMachine.CanTransition(currentState, nextState) then
        _log("invalid_transition", {
            fromState = currentState,
            toState = nextState,
            payload = payload,
        })

        local reason = ("Invalid state transition: %s -> %s"):format(tostring(currentState), tostring(nextState))
        return false, reason
    end

    previousState = currentState
    currentState = nextState
    enteredAt = os.clock()

    _log("state_change", {
        fromState = previousState,
        toState = currentState,
        enteredAt = enteredAt,
        payload = payload,
    })

    changedEvent:Fire(currentState, previousState, payload)
    return true, nil
end

-- Strict wrapper (optional strict)
function StateMachine.SetState(nextState: string, payload: any?)
    local ok, err = StateMachine.TrySetState(nextState, payload)
    if not ok then
        if strictSetState then
            assert(false, tostring(err))
        end
        return
    end
end

function StateMachine.OnChanged(callback: (string?, string?, any?) -> ()): () -> ()
    local conn = changedEvent.Event:Connect(function(newState, oldState, payload)
        callback(newState, oldState, payload)
    end)
    

    return function()
        conn:Disconnect()
    end
end

function StateMachine.Destroy()
    changedEvent:Destroy()
    currentState = nil
    previousState = nil
    enteredAt = nil
    ctxRef = nil
end



return StateMachine
```

------------------------------------------------------------------------

## 2.4 ServerScriptService/Gameplace/Match/MatchFlow.lua

``` lua
--!strict

local Players = game:GetService("Players")

local StateMachine = require(script.Parent.Parent.State.StateMachine)

local MatchFlow = {}

-- Localization Keys
local REASON_FORFEIT_RATIO = "MATCH_ABORT_REASON_FORFEIT_RATIO"
local REASON_INVALID_TELEPORT = "MATCH_ABORT_REASON_INVALID_TELEPORT"

local WINNER_ANNOUNCEMENT_KEY = "MATCH_WINNER_ANNOUNCEMENT"

type Disconnectable = RBXScriptConnection | (() -> ())
local conns: { Disconnectable } = {}

local aborted = false
local finished = false

local roundStartPlayers = 0
local forfeitsInRound = 0
local matchStartPlayersSnapshot: number? = nil

local roundIndex = 0
local totalRounds = 0
local startPlayersSnapshot = 0

local ctxRef = nil


local function disconnectAll()
    for _, c in ipairs(conns) do
        if typeof(c) == "RBXScriptConnection" then
            c:Disconnect()
        elseif typeof(c) == "function" then
            c()
        end
    end
    table.clear(conns)
end

local function safeSetState(targetState: string, payload: any)
    if typeof((StateMachine :: any).TrySetState) == "function" then
        ((StateMachine :: any).TrySetState)(targetState, payload)
    else
        StateMachine.SetState(targetState, payload)
    end
end

local function getCountdownSeconds()
    if ctxRef and typeof(ctxRef) == "table" and ctxRef.config and typeof(ctxRef.config) == "table" then
        local roundCfg = ctxRef.config.round
        if typeof(roundCfg) == "table" and typeof(roundCfg.doorCountdown) == "number" then
            return math.max(0, math.floor(roundCfg.doorCountdown))
        end
    end
    return 3
end

local function countdownKeyForNumber(n: number): string
    if n == 3 then return "COUNTDOWN_3" end
    if n == 2 then return "COUNTDOWN_2" end
    if n == 1 then return "COUNTDOWN_1" end
    return "COUNTDOWN_START"
end

local function runCountdown(scope: string)
    local seconds = getCountdownSeconds()

    -- Dev/Studio shortcut (optional, harmless)
    if ctxRef and typeof(ctxRef) == "table" and ctxRef.dev and typeof(ctxRef.dev) == "table" then
        if ctxRef.dev.enabled == true and ctxRef.dev.skipCountdown == true then
            seconds = 0
        end
    end

    -- Informational payload only (UI later)
    print(("[Countdown] scope=%s seconds=%d"):format(scope, seconds))

    for i = seconds, 1, -1 do
        if aborted or finished then return false end

        local key = countdownKeyForNumber(i)
        -- Later: fire UI remote with { key = key }
        print(("[Countdown] key=%s"):format(key))

        task.wait(1)
    end

    if aborted or finished then return false end

    -- START
    print("[Countdown] key=COUNTDOWN_START")
    return true
end

-- =========================
-- ABORT
-- =========================

function MatchFlow.Abort(reasonKey: string, details: any?)
    if aborted or finished then
        return
    end

    local state = StateMachine.GetState()

    if state == StateMachine.STATES.MATCH_ABORTED
        or state == StateMachine.STATES.MATCH_FINISHED then
        return
    end

    aborted = true

    local payload = {
        reasonKey = reasonKey,
        details = details,
        at = os.clock(),
    }

    disconnectAll()
    safeSetState(StateMachine.STATES.MATCH_ABORTED, payload)
end

-- =========================
-- FINISH
-- =========================

function MatchFlow.Finish(winnerUserId: number, details: any?)
    if aborted or finished then
        return
    end

    local state = StateMachine.GetState()

    -- Only allow finish from ROUND_FINISHED
    if state ~= StateMachine.STATES.ROUND_FINISHED then
        return
    end

    if typeof(winnerUserId) ~= "number" or winnerUserId <= 0 then
        warn("[MatchFlow] Invalid winnerUserId")
        return
    end

    finished = true

    local payload = {
        winnerUserId = winnerUserId,
        announcement = {
            key = WINNER_ANNOUNCEMENT_KEY,
            args = { player = tostring(winnerUserId) },
        },
        details = details,
        at = os.clock(),
    }

    disconnectAll()
    safeSetState(StateMachine.STATES.MATCH_FINISHED, payload)
end

-- =========================
-- ROUND HOOKS
-- =========================

function MatchFlow.BeginNextRound()
    if aborted or finished then
        return
    end

    if roundIndex >= totalRounds then
        return
    end

    roundIndex += 1

    local payload = {
        roundIndex = roundIndex,
        totalRounds = totalRounds,
        at = os.clock(),
    }

    safeSetState(StateMachine.STATES.ROUND_STARTING, payload)
end

function MatchFlow.RoundBegin(activePlayersCount: number)
    if aborted or finished then
        return
    end

    roundStartPlayers = math.max(0, activePlayersCount)
    forfeitsInRound = 0

    -- Edge: no players → abort
    if roundStartPlayers <= 0 then
        MatchFlow.Abort(REASON_FORFEIT_RATIO, { reason = "No players at round start" })
    end
end

function MatchFlow.RegisterForfeit(player: Player)
    if aborted or finished then
        return
    end

    forfeitsInRound += 1

    if roundStartPlayers > 0 then
        local ratio = forfeitsInRound / roundStartPlayers
        if ratio > 0.40 then
            MatchFlow.Abort(REASON_FORFEIT_RATIO, {
                ratio = ratio,
                player = player.UserId,
            })
        end
    end
end

-- =========================
-- INIT
-- =========================

local function isActiveState(state: string?): boolean
    return state == StateMachine.STATES.ROUND_ACTIVE
        or state == StateMachine.STATES.ROUND_STARTING
        or state == StateMachine.STATES.MATCH_STARTING
end

function MatchFlow.Init(ctx)
    ctxRef = ctx
    aborted = false
    finished = false
    roundStartPlayers = 0
    forfeitsInRound = 0
    matchStartPlayersSnapshot = nil
    
    roundIndex = 0
    startPlayersSnapshot = #Players:GetPlayers()
    totalRounds = math.max(0, startPlayersSnapshot-1)

    table.insert(conns, StateMachine.OnChanged(function(newState)

        if newState == StateMachine.STATES.MATCH_STARTING then
            matchStartPlayersSnapshot = #Players:GetPlayers()
        end

        if newState == StateMachine.STATES.ROUND_STARTING then
            task.spawn(function()
                local ok = runCountdown("ROUND")
                if not ok then
                    return
                end

                -- After countdown -> ROUND_ACTIVE
                safeSetState(StateMachine.STATES.ROUND_ACTIVE, {
                    at = os.clock(),
                    roundIndex = roundIndex,
                })
            end)
        end

        if newState == StateMachine.STATES.ROUND_ACTIVE then
            MatchFlow.RoundBegin(#Players:GetPlayers())
        end

        if newState == StateMachine.STATES.ROUND_FINISHED then
            if roundIndex >= totalRounds then
                return
            end
            MatchFlow.BeginNextRound()
        end
    end))

    table.insert(conns, Players.PlayerRemoving:Connect(function(player)
        if aborted or finished then
            return
        end

        local state = StateMachine.GetState()
        -- Forfeit
        if state == StateMachine.STATES.ROUND_ACTIVE then
            MatchFlow.RegisterForfeit(player)
        end
    end))
    
    
end

return MatchFlow
```

------------------------------------------------------------------------

# 3. GAMEPLACE --- Systems

## 3.1 ServerScriptService/Gameplace/Systems/RemoteValidationService.lua

``` lua
--!strict

local Players = game:GetService("Players")

local RemoteValidationService = {}

function RemoteValidationService.ValidatePlayer(player)
    if not player then return false end
    if player.Parent ~= Players then return false end
    if player.UserId <= 0 then return false end
    return true
end

return RemoteValidationService
```

------------------------------------------------------------------------

## 3.2 ServerScriptService/Gameplace/Systems/TeleportDataService.lua

``` lua
--!strict

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local TeleportDataService = {}

export type Mode = "Training" | "Multiplayer"

export type ResolveResult = {
    mode: Mode,
    teleportData: any?,
    isStudioFallback: boolean
}

function TeleportDataService.ReadJoinData(player: Player): any?
    local joinData = player:GetJoinData()
    if joinData and joinData.TeleportData then
        return joinData.TeleportData
    end
    return nil
end

function TeleportDataService.ValidateTeleportData(data: any): (boolean, any)
    if typeof(data) ~= "table" then
        return false, "TeleportData not a table"
    end

    if typeof(data.mode) ~= "string" then
        return false, "Missing mode"
    end

    if data.mode ~= "Training" and data.mode ~= "Multiplayer" then
        return false, "Invalid mode"
    end

    if typeof(data.timestamp) ~= "number" then
        return false, "Missing timestamp"
    end

    return true, data
end

local function ResolveStudioModeWithGraceWindow(graceSeconds: number): Mode
    local startTime = os.clock()

    while os.clock() - startTime < graceSeconds do
        if #Players:GetPlayers() > 1 then
            return "Multiplayer"
        end
        task.wait(0.1)
    end

    if #Players:GetPlayers() > 1 then
        return "Multiplayer"
    end

    return "Training"
end

function TeleportDataService.ResolveMode(player: Player): (boolean, ResolveResult | string)
    if RunService:IsStudio() then
        local resolvedMode: Mode = ResolveStudioModeWithGraceWindow(2.0)

        return true, {
            mode = resolvedMode,
            teleportData = nil,
            isStudioFallback = true
        }
    end

    local rawTeleportData = TeleportDataService.ReadJoinData(player)
    if not rawTeleportData then
        return false, "Missing TeleportData"
    end

    local ok, result = TeleportDataService.ValidateTeleportData(rawTeleportData)
    if not ok then
        return false, result
    end

    return true, {
        mode = result.mode,
        teleportData = result,
        isStudioFallback = false
    }
end

return TeleportDataService
```

# 4. SHARED CONFIG

## 4.1 ServerStorage/Config/Config.lua

``` lua
--!strict
-- ServerStorage/Config/Config.lua

local function DeepFreeze(t)
    table.freeze(t)
    for _, v in pairs(t) do
        if type(v) == "table" and not table.isfrozen(v) then
            DeepFreeze(v)
        end
    end
    return t
end

local Config = {

        
    ---- LOBBY ----
    lobby = {
        teleport = { gameplacePlaceId = 138269723080466},
    
        -- 🟢 Match Pads (Lobby Spec)
        pads = {
            matchPads = {
                minPlayers = 3,
                maxPlayers = 10,
                countdownSeconds = 20,
                fullPadInstantStart = true,
            },

        },


        -- 🏆 Leaderboards
        leaderboards = {
            allTime = { enabled = true },

            daily = {
                enabled = true,
                topN = 100,
            },

            refreshSeconds = 300, -- 5 Minuten
        },
        
    },
    
    ---- GAMEPLACE ----
    gameplace = {

        -- TELEPORT INFOS
        teleport = { lobbyPlaceId = 116324448444836 },
        
        -- MATCH START LOCK
        matchStartLock = {
            joinTimeoutSeconds = 10,
        },
        
        -- ROUND SYSTEM
        round = {
            doorCountdown = 3,
        },
        
        -- 🔒 Safety / Locks
        safety = {
            serverLockOnMatchStart = true,
            kickLateJoiners = false,
        },
    },
    
    ---- DEV ----
    dev = {
        enabled = false
    }   
    
}

return DeepFreeze(Config)
```

------------------------------------------------------------------------

END OF CODEFILES SPEC v3.3 (SELF-CONTAINED)

------------------------------------------------------------------------

# SOURCE FILE: LAST_ONE_OUT_ROADMAP_v6_FULL_Checkbox.md

# LAST ONE OUT --- ROADMAP v6 (FULL CHECKBOX EDITION)

Architecture: Multi-Place (Lobby + Gameplace) System: CP P.C.S
(Phase.Kapitel.Step) Total: 200+ structured checkpoints

------------------------------------------------------------------------

# 🔴 PHASE 1 --- PROJECT & SETUP

## Kapitel 1.1 --- Repository & Versionierung

-   [ ] CP 1.1.01 --- Git Repository initialisieren
-   [ ] CP 1.1.02 --- Branching-Strategie definieren
-   [ ] CP 1.1.03 --- Commit-Konvention definieren
-   [ ] CP 1.1.04 --- CP-Tag-System etablieren
-   [ ] CP 1.1.05 --- Projektstruktur-Dokumentation anlegen

## Kapitel 1.2 --- Multi-Place Setup

-   [ ] CP 1.2.01 --- Lobby Place erstellen
-   [ ] CP 1.2.02 --- Gameplace erstellen
-   [ ] CP 1.2.03 --- Experience-Verknüpfung prüfen
-   [ ] CP 1.2.04 --- PlaceId dokumentieren
-   [ ] CP 1.2.05 --- Teleport Basistest durchführen

## Kapitel 1.3 --- Struktur-Implementierung

-   [ ] CP 1.3.01 --- ServerStorage Config Struktur
-   [ ] CP 1.3.02 --- ServerScriptService Struktur
-   [ ] CP 1.3.03 --- ReplicatedStorage Remotes Struktur
-   [ ] CP 1.3.04 --- PascalCase Validierung
-   [ ] CP 1.3.05 --- Struktur-Test

## Kapitel 1.4 --- Config Framework

-   [ ] CP 1.4.01 --- LobbyConfig.lua
-   [ ] CP 1.4.02 --- GameplaceConfig.lua
-   [ ] CP 1.4.03 --- Read-Only Pattern
-   [ ] CP 1.4.04 --- Magic-Number Audit
-   [ ] CP 1.4.05 --- Config-Test

## Kapitel 1.5 --- Remote Framework

-   [ ] CP 1.5.01 --- Lobby Remotes
-   [ ] CP 1.5.02 --- Gameplace Remote Ordner
-   [ ] CP 1.5.03 --- Naming Audit
-   [ ] CP 1.5.04 --- Server Validation Pattern
-   [ ] CP 1.5.05 --- Dummy Remote Test

## Kapitel 1.6 --- Localization

-   [ ] CP 1.6.01 --- Key-System definieren
-   [ ] CP 1.6.02 --- EN Dictionary
-   [ ] CP 1.6.03 --- Fallback Mechanismus
-   [ ] CP 1.6.04 --- Locked Terms sichern
-   [ ] CP 1.6.05 --- Localizer Stub

## Kapitel 1.7 --- Dev Tools Setup

-   [ ] CP 1.7.01 --- DevConfig Flag
-   [ ] CP 1.7.02 --- Debug Ordner
-   [ ] CP 1.7.03 --- Console Command Pattern
-   [ ] CP 1.7.04 --- Studio Restriction
-   [ ] CP 1.7.05 --- Security Check

## Kapitel 1.8 --- Coding Style Enforcement

-   [ ] CP 1.8.01 --- Naming Audit
-   [ ] CP 1.8.02 --- STATES vorbereiten
-   [ ] CP 1.8.03 --- Server Authority Check
-   [ ] CP 1.8.04 --- Client Render Only Check
-   [ ] CP 1.8.05 --- Final Style Audit

## Kapitel 1.9 --- Stabilitätstest

-   [ ] CP 1.9.01 --- Reload Test
-   [ ] CP 1.9.02 --- Boot Test
-   [ ] CP 1.9.03 --- Remote Registration Test
-   [ ] CP 1.9.04 --- Config Load Test
-   [ ] CP 1.9.05 --- Phase Abschlussprüfung

------------------------------------------------------------------------

# 🟠 PHASE 2 --- GAMEPLACE ARCHITECTURE

## Kapitel 2.1 --- Core Architektur

-   [ ] CP 2.1.01 --- Grundstruktur
-   [ ] CP 2.1.02 --- Initialisierung
-   [ ] CP 2.1.03 --- TeleportData Validierung
-   [ ] CP 2.1.04 --- Fehlerbehandlung
-   [ ] CP 2.1.05 --- Server Authority Enforcement

## Kapitel 2.2 --- Match Start Lock

-   [ ] CP 2.2.01 --- Expected Players Tracking
-   [ ] CP 2.2.02 --- Join Timeout
-   [ ] CP 2.2.03 --- Start Condition Check
-   [ ] CP 2.2.04 --- Edge Case Handling
-   [ ] CP 2.2.05 --- Logging

------------------------------------------------------------------------

# 🟡 PHASE 3 --- STATE MACHINE

## Kapitel 3.1 --- Core State Structure

-   [ ] CP 3.1.01 --- STATES Definition
-   [ ] CP 3.1.02 --- State Container
-   [ ] CP 3.1.03 --- Transition Rules
-   [ ] CP 3.1.04 --- Validation
-   [ ] CP 3.1.05 --- Logging

## Kapitel 3.2 --- Transition Safety

-   [ ] CP 3.2.01 --- Invalid Transition Handling
-   [ ] CP 3.2.02 --- Abort State
-   [ ] CP 3.2.03 --- Finish State
-   [ ] CP 3.2.04 --- Edge Case Handling
-   [ ] CP 3.2.05 --- Stability Test

------------------------------------------------------------------------

# 🟢 PHASE 4 --- MATCH FLOW CORE

## Kapitel 4.1 --- Round Lifecycle

-   [ ] CP 4.1.01 --- Round Counter
-   [ ] CP 4.1.02 --- Countdown Logic
-   [ ] CP 4.1.03 --- Challenge Start
-   [ ] CP 4.1.04 --- Challenge End
-   [ ] CP 4.1.05 --- Round Advance

------------------------------------------------------------------------

# 🔵 PHASE 5 --- ELIMINATION SYSTEM

## Kapitel 5.1 --- Elimination Core

-   [ ] CP 5.1.01 --- Elimination Validation
-   [ ] CP 5.1.02 --- SAFE Handling
-   [ ] CP 5.1.03 --- OUT Handling
-   [ ] CP 5.1.04 --- Spectator State
-   [ ] CP 5.1.05 --- Max 1 Elimination Rule

------------------------------------------------------------------------

# 🟣 PHASE 6 --- FORFEIT & ABORT

## Kapitel 6.1 --- Forfeit Logic

-   [ ] CP 6.1.01 --- ACTIVE Leave Detection
-   [ ] CP 6.1.02 --- Forfeit Handling
-   [ ] CP 6.1.03 --- Abort Threshold
-   [ ] CP 6.1.05 --- Match Abort Handling

------------------------------------------------------------------------

# ⚫ PHASE 7 --- UI SYSTEM

## Kapitel 7.1 --- UI Rendering

-   [ ] CP 7.1.01 --- Mode Badge
-   [ ] CP 7.1.02 --- Round Counter UI
-   [ ] CP 7.1.03 --- Countdown UI
-   [ ] CP 7.1.04 --- SAFE UI
-   [ ] CP 7.1.05 --- OUT Overlay

------------------------------------------------------------------------

# 🟠 PHASE 8 --- STATS & PERSISTENCE

## Kapitel 8.1 --- Stats Core

-   [ ] CP 8.1.01 --- RoundsSurvived
-   [ ] CP 8.1.02 --- MatchWins
-   [ ] CP 8.1.03 --- RoundKings
-   [ ] CP 8.1.04 --- Ranked Eligibility Check
-   [ ] CP 8.1.05 --- DataStore Save

------------------------------------------------------------------------

# 🟠 PHASE 9 --- RANK SYSTEM

## Kapitel 9.1 --- RP System

-   [ ] CP 9.1.01 --- RP Calculation
-   [ ] CP 9.1.02 --- Season Handling
-   [ ] CP 9.1.03 --- Legend Floor
-   [ ] CP 9.1.04 --- Rank Update
-   [ ] CP 9.1.05 --- Rank Persistence

------------------------------------------------------------------------

# 🔷 PHASE 10 --- LOBBY SYSTEM

## Kapitel 10.1 --- Lobby Pads

-   [ ] CP 10.1.01 --- Pad Detection
-   [ ] CP 10.1.02 --- Countdown
-   [ ] CP 10.1.03 --- Full Pad Instant Start
-   [ ] CP 10.1.04 --- Glow Intensity
-   [ ] CP 10.1.05 --- Leaderboard Display

------------------------------------------------------------------------

# 🔶 PHASE 11 --- TELEPORT & MULTI-PLACE

## Kapitel 11.1 --- Teleport System

-   [ ] CP 11.1.01 --- TeleportRequest
-   [ ] CP 11.1.02 --- TeleportStatus
-   [ ] CP 11.1.03 --- TeleportData Validation
-   [ ] CP 11.1.04 --- Invalid Data Return
-   [ ] CP 11.1.05 --- Integration Test

------------------------------------------------------------------------

# 🟩 PHASE 12 --- TRAINING MODE

## Kapitel 12.1 --- Training Core

-   [ ] CP 12.1.01 --- Conscious Start
-   [ ] CP 12.1.02 --- Picker/Random Mode
-   [ ] CP 12.1.03 --- Best Time Tracking
-   [ ] CP 12.1.04 --- WIN/LOSE/TIMEOUT
-   [ ] CP 12.1.05 --- No Ranked Influence

------------------------------------------------------------------------

# 🟫 PHASE 13 --- DEV TOOLS

## Kapitel 13.1 --- Dev Commands

-   [ ] CP 13.1.01 --- ForceChallenge
-   [ ] CP 13.1.02 --- SkipCountdown
-   [ ] CP 13.1.03 --- Dev Mode Restriction
-   [ ] CP 13.1.04 --- Validation
-   [ ] CP 13.1.05 --- Security Audit

------------------------------------------------------------------------

END OF ROADMAP v6 --- FULL CHECKBOX VERSION

------------------------------------------------------------------------

# SOURCE FILE: game_data.md

# LAST ONE OUT - Personal Game Data

## PLACE IDs:

LOBBY: 116324448444836 GAMEPLACE: 138269723080466

#PLAYER ID (DEV)

id: 5384430161

------------------------------------------------------------------------


---
# 🔒 1v1 PREVENTION EXTENSION (NON-DESTRUCTIVE ADDITION)

## 🚫 1v1 Matches dürfen nicht starten

Ein Match darf NICHT starten, wenn weniger als 3 Spieler im Teleport-Pad / MatchStartSnapshot vorhanden sind.

### Neue Regel:

- Mindestspielerzahl für Teleport: > 2 Spieler
- Falls Spieleranzahl <= 2 → Match darf nicht initialisiert werden
- Teleport wird verhindert

Pseudo-Implementierung:

```lua
local MIN_PLAYERS_REQUIRED = 3

if matchStartPlayersSnapshot < MIN_PLAYERS_REQUIRED then
    returnToLobby("Zu wenig Spieler")
    return
end
```

---

## ⏳ Sonderfall: Nach Teleport bleiben nur 2 Spieler übrig

Falls während des Teleport-Timeouts Spieler leaven und nach Ablauf des Timeouts nur noch 2 Spieler im Match vorhanden sind:

### Verhalten:

- Match startet NICHT
- Kein Abort
- Kein Ranked-Check
- Keine Stats
- Keine RP

Stattdessen:

Anzeige für alle verbleibenden Spieler:

> "Leider zu wenig Spieler, zurück zur Lobby"

Danach:

- Automatischer Return zur Lobby
- MatchState wird sauber zurückgesetzt
- Kein MatchResult wird gespeichert

Pseudo-Implementierung:

```lua
local MIN_PLAYERS_REQUIRED = 3

if activePlayersCount < MIN_PLAYERS_REQUIRED then
    broadcastMessage("Leider zu wenig Spieler, zurück zur Lobby")
    resetMatchState()
    teleportPlayersToLobby()
    return
end
```

---

## ✅ Auswirkungen auf bestehende Logik

- Keine bestehende Ranked-Logik wird verändert
- Kein Abort-System wird entfernt
- Kein RP-System wird verändert
- Keine Stats werden gelöscht
- Keine bestehenden Regeln werden überschrieben

Diese Erweiterung ergänzt ausschließlich eine Mindestspielerprüfung vor Matchstart sowie eine Nach-Teleport-Sicherheitsprüfung.


---
# 🧩 ENGINE-SAFE INTEGRATION (ADD-ONLY) — MatchFlow / StateMachine

> Ziel: 1v1 darf **niemals** starten.  
> Umsetzung: **2 Checks** (vor Teleport + nach Teleport/Timeout), die sich sauber in den bestehenden MatchFlow einfügen.

## 1) Konstanten & Helper (oben bei den anderen Config/Consts hinzufügen)

```lua
-- 1v1 Prevention (ADD)
local MIN_PLAYERS_REQUIRED_FOR_MATCH = 3

local function getActivePlayerCount(playersTableOrService)
    -- Implementierung je nach Codebasis:
    -- Option A: playersTableOrService ist ein Array/Map der aktiven Match-Spieler
    -- Option B: Players:GetPlayers()
    -- Bitte an eure Struktur anpassen, ohne bestehende Logik zu entfernen.
    local c = 0
    for _ in pairs(playersTableOrService) do
        c += 1
    end
    return c
end

local function lobbyReturnTooFewPlayers(messageOpt)
    local msg = messageOpt or "Leider zu wenig Spieler, zurück zur Lobby"
    -- 1) an alle verbleibenden Spieler broadcasten
    -- 2) MatchState sauber resetten
    -- 3) Spieler zurück in Lobby teleportieren
    broadcastMessage(msg)
    resetMatchState()
    teleportPlayersToLobby()
end
```

✅ **Add-only:** Diese Helfer fassen die neue Logik zusammen und ändern nichts Bestehendes.

---

## 2) Check A — Vor Teleport (Pad >2 Player)

### Einfügepunkt (ADD):
Direkt bevor der Teleport ausgelöst wird (z.B. am Ende von START/QUEUE, kurz bevor ihr in TELEPORT wechselt).

```lua
-- ADD: Prevent 1v1 start (before TELEPORT)
local startCount = matchStartPlayersSnapshot or getActivePlayerCount(currentMatchPlayers)
if startCount < MIN_PLAYERS_REQUIRED_FOR_MATCH then
    lobbyReturnTooFewPlayers("Leider zu wenig Spieler, zurück zur Lobby")
    return
end

-- existing teleport code continues below
startTeleportToArena()
StateMachine.SetState(STATE_TELEPORT)
```

**Wichtig:**  
- "Pad >2 player für teleport" bedeutet hier: Teleport wird **nur** gestartet, wenn mindestens 3 Spieler bereit sind.

---

## 3) Check B — Nach Teleport (Timeout) nur 2 Spieler übrig

### Einfügepunkt (ADD):
Dort, wo ihr nach Teleport/Timeout die Runde/ACTIVE initialisiert.  
Also: **nachdem** Teleport abgeschlossen ist, aber **bevor** ihr ACTIVE startet.

```lua
-- ADD: Post-teleport safety check (after teleport timeout)
local activeCount = getActivePlayerCount(currentMatchPlayers)
if activeCount < MIN_PLAYERS_REQUIRED_FOR_MATCH then
    lobbyReturnTooFewPlayers("Leider zu wenig Spieler, zurück zur Lobby")
    return
end

-- existing round/ACTIVE init continues below
initRound()
StateMachine.SetState(STATE_ACTIVE)
```

Damit ist der Fall abgedeckt:
- Spieler starten mit 3+
- während/kurz nach Teleport leaven welche
- nach Timeout sind nur 2 da  
➡️ **kein Matchstart**, sondern Message + Lobby return

---

## 4) UI / Localization (ADD)

Falls ihr eine Localization-Tabelle habt, fügt (zusätzlich) einen Key hinzu:

```lua
-- ADD
MATCH_TOO_FEW_PLAYERS_RETURN_LOBBY = "Leider zu wenig Spieler, zurück zur Lobby"
```

Dann könnt ihr statt Hardcode nutzen:

```lua
lobbyReturnTooFewPlayers(L("MATCH_TOO_FEW_PLAYERS_RETURN_LOBBY"))
```

---

## 5) Ranked / Stats Sicherheit (ADD)

Damit es garantiert **keine** Ranked/Stats Nebenwirkungen gibt, empfehle ich (add-only) am Punkt der Result-Speicherung:

```lua
-- ADD: Guard against result saving when returned-to-lobby due to too few players
if matchEndedByTooFewPlayersReturn then
    -- do not save match result, stats, rp
    return
end
```

Setzt dazu beim Lobby-Return-Helper:

```lua
-- ADD inside lobbyReturnTooFewPlayers()
matchEndedByTooFewPlayersReturn = true
```

---

## 6) Warum das besser ist als "1v1 Abort" (ADD Erklärung)

- Kein "Abort"-State nötig
- Kein Winner-Fake
- Kein Ranked-Eligible Check
- Spieler bekommen klare Meldung + landen sauber in Lobby
- Funktioniert für: 2 Spieler Start **und** 3+ Start → danach Drop auf 2

---
