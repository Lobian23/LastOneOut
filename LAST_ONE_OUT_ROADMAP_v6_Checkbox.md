# LAST ONE OUT --- ROADMAP v6 (Checkbox Edition)

Architecture: Multi-Place (Lobby + Gameplace) System: CP P.C.S
(Phase.Kapitel.Step)

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

(Phasen 4--13 sind strukturell identisch fortgeführt wie im
Originaldokument. Diese Datei kann beliebig erweitert werden.)
