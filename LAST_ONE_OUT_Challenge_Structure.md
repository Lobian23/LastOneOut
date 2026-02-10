# LAST ONE OUT – Challenge Structure

## Speicherort
ServerStorage/Challenges/

---

## Ordnerformat
NNN_ChallengeName

---

## Pflichtstruktur
NNN_ChallengeName
├─ Map
├─ Logic
├─ Description

---

## Optional
TeleportStart (Part)

---

## TeleportStart
Optionaler Startpunkt.
Wenn vorhanden:
- Start über Portal
- Teleport beim Durchlaufen

---

## Logic Interface

Logic.Init(context)  
Logic.Start()  
Logic.Stop(reason)

Logic.Signals.PlayerSafe(player)  
Logic.Signals.Eliminate(player, reason)  
Logic.Signals.Ended(reason)

---

## Leitsatz
Challenge ist Gameplay – Gameplace ist Orchestrator.