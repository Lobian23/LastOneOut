# LAST ONE OUT – Config Spec (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Zweck

Diese Spec definiert **welche Parameter konfigurierbar sind** und **wie Config im Projekt genutzt wird**.  
Sie enthält **keine konkreten Zahlenwerte**.

> **Single Source of Truth:** `ServerStorage/config/gameconfig.lua`

---

## 1) Grundregeln

- Alle Timings, Delays und UI-Dauern kommen aus der Config
- Keine Magic Numbers im Code
- Config ist **server-only** und **read-only**
- Challenges dürfen **keine globalen Timings** definieren

---

## 2) Struktur

Die Config ist in logisch getrennte Bereiche unterteilt:

- `config.match` – Match- & Rundenabläufe
- `config.safe` – SAFE-UI & Reihenfolge
- `config.training` – Training-Loop
- `config.ui` – rein visuelle Toggles
- `config.dev` – Debug & Overrides

---

## 3) Verantwortlichkeiten

### Gameplace darf:
- Config lesen
- Flow anhand der Config steuern

### Challenges dürfen:
- eigene, lokale Werte intern nutzen

### Challenges dürfen NICHT:
- globale Timings überschreiben
- Config verändern oder erwarten

---

## 4) Nutzung im Code

```lua
local serverstorage = game:GetService("ServerStorage")
local config = require(serverstorage.config.gameconfig)

function StartDoorCountdown()
	for i = config.match.doorcountdown, 1, -1 do
		UpdateCountdownUi(i)
		task.wait(1)
	end
	OpenDoor()
end
```

---

## 5) Versionierung

- Änderungen nur über neue Version
- Breaking Changes → neue Major Version
- Alte Versionen bleiben gültig

---

**ENDE – Config Spec v1.0 (FROZEN)**
