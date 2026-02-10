# LAST ONE OUT – Dev Tools Spec (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Gültig für:** Gameplace (Server & Client)  
**Zweck:** Dev- & Debug-Werkzeuge für Entwicklung und Live-Tests  
**Scope:** Multiplayer & Training (klar getrennt)

---

## 0) Zweck

Diese Spec definiert **verbindlich**, wie **Dev-Tools** im Projekt *LAST ONE OUT* umgesetzt werden dürfen.

Dev-Tools dienen ausschließlich:
- Entwicklung
- Debugging
- Live-Tests durch Entwickler/Admins

Dev-Tools sind **kein Gameplay**, **keine Features** und **niemals erforderlich**, um das Spiel zu spielen.

---

## 1) Grundprinzipien

- Dev-Tools sind **optional**
- Dev-Tools dürfen **Gameplay nie beeinflussen**
- Dev-Tools dürfen **nur Flow / Auswahl / Debug steuern**
- **Server ist immer autoritativ**
- Client darf **nur Requests senden**

> *Dev-Tools sind Werkzeuge – keine Systeme.*

---

## 2) Aktivierung & Zugriff

Dev-Tools sind **nur aktiv**, wenn mindestens eine Bedingung erfüllt ist:

- `config.dev.enabled == true`
- **ODER** `RunService:IsStudio() == true`

Zusätzlich gilt:
- Zugriff **nur** für UserIds in `config.dev.adminuserids`
- **Server prüft immer** (Client ist nicht vertrauenswürdig)

---

## 3) Eingabemethode (verbindlich)

### ✅ Erlaubt
- **Console-Eingabe (Text Commands)**
- z. B. Studio Command Bar, Developer Console, minimaler Client-Listener

### ❌ Verboten
- GUI-Buttons
- versteckte UI-Panels
- Explorer-Values als Steuerzentrale
- direkte Code-Hooks im Gameplay

> *Dev-Tools werden getippt, nicht geklickt.*

---

## 4) Speicherorte

### Server
```
ServerScriptService/Gameplace/Debug/
```

### Client (optional)
```
StarterPlayer/StarterPlayerScripts/Debug/
```

### Networking
```
ReplicatedStorage/Remotes/Debug/
```

Der `Debug`-Remote-Ordner ist **optional**, aber **ausschließlich** für Dev-Tools reserviert.

---

## 5) Networking-Regeln

- Dev-Tools nutzen **nur Debug-Remotes**
- Keine Wiederverwendung von Match / Ui / Training Remotes
- Client → Server = **Request**
- Server → Client = **Feedback / Status**
- Payloads:
  - klein
  - validiert
  - keine Instances

---

## 6) Erlaubte Dev-Funktionen

### 6.1 Force Challenge (MP-only)

**Zweck:**  
Erzwingt für Multiplayer die Auswahl **einer bestimmten Challenge**.

**Regeln:**
- wirkt **nur Multiplayer**
- überschreibt Random / Picker
- wirkt **nur auf Challenge-Auswahl**
- Gameplay bleibt unverändert
- optional:
  - auto-clear nach Load
  - oder manuell per Command

**Beispiel-Commands:**
```
force 10
clear
status
```

---

## 7) Verbotene Dev-Funktionen (No-Gos)

Dev-Tools dürfen **niemals**:

- SAFE vergeben
- Spieler eliminieren
- Winner setzen
- Match beenden
- Spieler teleportieren
- Game-States direkt mutieren
- Gameplay-Logik umgehen

> *Dev-Tools debuggen den Flow – nicht das Spiel.*

---

## 8) Sichtbarkeit & Feedback

- Jeder aktive Override **muss sichtbar sein**
  - z. B. Console-Output, Toast, Log
- Kein „stiller“ Dev-State

Beispiel:
```
[DEV] Forced Challenge active: 010_LaserMaze
```

---

## 9) Trennung von Specs

- Dev Spec ergänzt:
  - Config Spec
  - Remote Events Spec
  - Gameplace Architecture
- Dev Spec **überschreibt keine Gameplay- oder Architektur-Regeln**

Im Konfliktfall gelten:
1. Gameplay Spec  
2. Architecture Spec  
3. Structure Specs  
4. Dev Spec  

---

## 10) Leitsätze

- *Dev-Tools sind niemals Voraussetzung*
- *Server entscheidet immer*
- *Console over UI*
- *Debug darf sichtbar sein, aber nie mächtig*

---

**ENDE – Dev Tools Spec v1.0 (FROZEN)**
