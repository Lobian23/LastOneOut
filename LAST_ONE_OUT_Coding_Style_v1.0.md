# LAST ONE OUT – Coding Style & Conventions (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Ziel

Diese Spec definiert **verbindliche Coding-Regeln** für das gesamte Projekt.

---

## 1) Naming

### Variablen
- immer lowercase
```lua
local countdown = 5
local safeindex = 1
```

### Funktionen
- beginnen immer mit Großbuchstaben
```lua
function StartMatch()
function ShowSafeUi(player)
```

---

## 2) Dateien & Ordner

- ordner: lowercase
- modulescripts: lowercase dateinamen
- klare thematische Trennung

---

## 3) Code-Struktur

- kurze, fokussierte Funktionen
- klare Responsibilities
- Sections mit Trennern

---

## 4) Module Pattern

- ModuleScripts geben **immer** eine Table zurück
- keine Globals
- explizite Public API

---

## 5) Server / Client

- Server entscheidet
- Client rendert
- Client validiert nichts

---

## 6) Architektur-Leitsatz

> *Gameplace orchestriert – Challenge entscheidet.*

---

**ENDE – Coding Style v1.0 (FROZEN)**
