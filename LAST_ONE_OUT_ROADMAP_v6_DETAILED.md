# LAST ONE OUT --- ROADMAP v6 (DETAILED)

Architecture: Multi-Place (Lobby + Gameplace) System: CP P.C.S
(Phase.Kapitel.Step) Total: 200+ structured checkpoints

------------------------------------------------------------------------

# 🔴 PHASE 1 --- PROJECT & SETUP

## Kapitel 1.1 --- Repository & Versionierung

CP 1.1.01 --- Git Repository initialisieren\
CP 1.1.02 --- Branching-Strategie definieren\
CP 1.1.03 --- Commit-Konvention definieren\
CP 1.1.04 --- CP-Tag-System etablieren\
CP 1.1.05 --- Projektstruktur-Dokumentation anlegen

## Kapitel 1.2 --- Multi-Place Setup

CP 1.2.01 --- Lobby Place erstellen\
CP 1.2.02 --- Gameplace erstellen\
CP 1.2.03 --- Experience-Verknüpfung prüfen\
CP 1.2.04 --- PlaceId dokumentieren\
CP 1.2.05 --- Teleport Basistest durchführen

## Kapitel 1.3 --- Struktur-Implementierung

CP 1.3.01 --- ServerStorage Config Struktur\
CP 1.3.02 --- ServerScriptService Struktur\
CP 1.3.03 --- ReplicatedStorage Remotes Struktur\
CP 1.3.04 --- PascalCase Validierung\
CP 1.3.05 --- Struktur-Test

## Kapitel 1.4 --- Config Framework

CP 1.4.01 --- LobbyConfig.lua\
CP 1.4.02 --- GameplaceConfig.lua\
CP 1.4.03 --- Read-Only Pattern\
CP 1.4.04 --- Magic-Number Audit\
CP 1.4.05 --- Config-Test

## Kapitel 1.5 --- Remote Framework

CP 1.5.01 --- Lobby Remotes\
CP 1.5.02 --- Gameplace Remote Ordner\
CP 1.5.03 --- Naming Audit\
CP 1.5.04 --- Server Validation Pattern\
CP 1.5.05 --- Dummy Remote Test

## Kapitel 1.6 --- Localization

CP 1.6.01 --- Key-System definieren\
CP 1.6.02 --- EN Dictionary\
CP 1.6.03 --- Fallback Mechanismus\
CP 1.6.04 --- Locked Terms sichern\
CP 1.6.05 --- Localizer Stub

## Kapitel 1.7 --- Dev Tools Setup

CP 1.7.01 --- DevConfig Flag\
CP 1.7.02 --- Debug Ordner\
CP 1.7.03 --- Console Command Pattern\
CP 1.7.04 --- Studio Restriction\
CP 1.7.05 --- Security Check

## Kapitel 1.8 --- Coding Style Enforcement

CP 1.8.01 --- Naming Audit\
CP 1.8.02 --- STATES vorbereiten\
CP 1.8.03 --- Server Authority Check\
CP 1.8.04 --- Client Render Only Check\
CP 1.8.05 --- Final Style Audit

## Kapitel 1.9 --- Stabilitätstest

CP 1.9.01 --- Reload Test\
CP 1.9.02 --- Boot Test\
CP 1.9.03 --- Remote Registration Test\
CP 1.9.04 --- Config Load Test\
CP 1.9.05 --- Phase Abschlussprüfung

------------------------------------------------------------------------

# 🟠 PHASE 2 --- GAMEPLACE ARCHITECTURE

## Kapitel 2.1 --- Core Architektur

CP 2.1.01 --- Grundstruktur\
CP 2.1.02 --- Initialisierung\
CP 2.1.03 --- TeleportData Validierung\
CP 2.1.04 --- Fehlerbehandlung\
CP 2.1.05 --- Server Authority Enforcement

## Kapitel 2.2 --- Match Start Lock

CP 2.2.01 --- Expected Players Tracking\
CP 2.2.02 --- Join Timeout\
CP 2.2.03 --- Start Condition Check\
CP 2.2.04 --- Edge Case Handling\
CP 2.2.05 --- Logging

------------------------------------------------------------------------

# 🟡 PHASE 3 --- STATE MACHINE

## Kapitel 3.1 --- Core State Structure

CP 3.1.01 --- STATES Definition\
CP 3.1.02 --- State Container\
CP 3.1.03 --- Transition Rules\
CP 3.1.04 --- Validation\
CP 3.1.05 --- Logging

## Kapitel 3.2 --- Transition Safety

CP 3.2.01 --- Invalid Transition Handling\
CP 3.2.02 --- Abort State\
CP 3.2.03 --- Finish State\
CP 3.2.04 --- Edge Case Handling\
CP 3.2.05 --- Stability Test

------------------------------------------------------------------------

# 🟢 PHASE 4 --- MATCH FLOW CORE

## Kapitel 4.1 --- Round Lifecycle

CP 4.1.01 --- Round Counter\
CP 4.1.02 --- Countdown Logic\
CP 4.1.03 --- Challenge Start\
CP 4.1.04 --- Challenge End\
CP 4.1.05 --- Round Advance

------------------------------------------------------------------------

# 🔵 PHASE 5 --- ELIMINATION SYSTEM

## Kapitel 5.1 --- Elimination Core

CP 5.1.01 --- Elimination Validation\
CP 5.1.02 --- SAFE Handling\
CP 5.1.03 --- OUT Handling\
CP 5.1.04 --- Spectator State\
CP 5.1.05 --- Max 1 Elimination Rule

------------------------------------------------------------------------

# 🟣 PHASE 6 --- FORFEIT & ABORT

## Kapitel 6.1 --- Forfeit Logic

CP 6.1.01 --- ACTIVE Leave Detection\
CP 6.1.02 --- Forfeit Handling\
CP 6.1.03 --- Abort Threshold\
CP 6.1.04 --- 1v1 Abort\
CP 6.1.05 --- Match Abort Handling

------------------------------------------------------------------------

# 🟤 PHASE 7 --- UI SYSTEM

## Kapitel 7.1 --- UI Rendering

CP 7.1.01 --- Mode Badge\
CP 7.1.02 --- Round Counter UI\
CP 7.1.03 --- Countdown UI\
CP 7.1.04 --- SAFE UI\
CP 7.1.05 --- OUT Overlay

------------------------------------------------------------------------

# ⚫ PHASE 8 --- STATS & PERSISTENCE

## Kapitel 8.1 --- Stats Core

CP 8.1.01 --- RoundsSurvived\
CP 8.1.02 --- MatchWins\
CP 8.1.03 --- RoundKings\
CP 8.1.04 --- Ranked Eligibility Check\
CP 8.1.05 --- DataStore Save

------------------------------------------------------------------------

# 🟠 PHASE 9 --- RANK SYSTEM

## Kapitel 9.1 --- RP System

CP 9.1.01 --- RP Calculation\
CP 9.1.02 --- Season Handling\
CP 9.1.03 --- Legend Floor\
CP 9.1.04 --- Rank Update\
CP 9.1.05 --- Rank Persistence

------------------------------------------------------------------------

# 🔷 PHASE 10 --- LOBBY SYSTEM

## Kapitel 10.1 --- Lobby Pads

CP 10.1.01 --- Pad Detection\
CP 10.1.02 --- Countdown\
CP 10.1.03 --- Full Pad Instant Start\
CP 10.1.04 --- Glow Intensity\
CP 10.1.05 --- Leaderboard Display

------------------------------------------------------------------------

# 🔶 PHASE 11 --- TELEPORT & MULTI-PLACE

## Kapitel 11.1 --- Teleport System

CP 11.1.01 --- TeleportRequest\
CP 11.1.02 --- TeleportStatus\
CP 11.1.03 --- TeleportData Validation\
CP 11.1.04 --- Invalid Data Return\
CP 11.1.05 --- Integration Test

------------------------------------------------------------------------

# 🟩 PHASE 12 --- TRAINING MODE

## Kapitel 12.1 --- Training Core

CP 12.1.01 --- Conscious Start\
CP 12.1.02 --- Picker/Random Mode\
CP 12.1.03 --- Best Time Tracking\
CP 12.1.04 --- WIN/LOSE/TIMEOUT\
CP 12.1.05 --- No Ranked Influence

------------------------------------------------------------------------

# 🟫 PHASE 13 --- DEV TOOLS

## Kapitel 13.1 --- Dev Commands

CP 13.1.01 --- ForceChallenge\
CP 13.1.02 --- SkipCountdown\
CP 13.1.03 --- Dev Mode Restriction\
CP 13.1.04 --- Validation\
CP 13.1.05 --- Security Audit

------------------------------------------------------------------------

END OF ROADMAP v6
