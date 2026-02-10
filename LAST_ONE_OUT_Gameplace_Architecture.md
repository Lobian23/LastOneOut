# LAST ONE OUT – Gameplace Architecture

## Grundprinzip
Der Gameplace ist ein **Orchestrator**.
Er steuert Ablauf, Zustände und Übergänge, entscheidet aber keine Gameplay-Regeln.

---

## Match-Start-Lock
Ein Match startet erst, wenn:
- alle erwarteten Spieler im Gameplace angekommen sind
- oder ein Join-Timeout abgelaufen ist (Problemspieler werden entfernt)

Erst danach:
1. Challenge laden
2. InfoBoard
3. Countdown
4. Start

---

## Start-Typen

### Run-Start
- kein TeleportStart
- Spieler laufen nach Countdown los

### Teleport-Start (Portal)
- TeleportStart existiert
- Tür wird Portal
- Spieler laufen durch und werden teleportiert

---

## Countdown & Start
- Countdown mittig
- Nach „1“ erscheint Start-Text (z. B. START!)
- Danach beginnt die Challenge

---

## Respawn
Respawn-Punkt:
- TeleportStart (falls vorhanden)
- sonst normaler Map-Start

---

## UI-Grundsatz
Server entscheidet, Client rendert.
UI erklärt Zustand – nicht Logik.