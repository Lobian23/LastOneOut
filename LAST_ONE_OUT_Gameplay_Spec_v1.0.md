# LAST ONE OUT – Gameplay Spec (FROZEN)

**Status:** FINAL / FROZEN  
**Version:** v1.0  
**Änderungen:** nur über neue Version (v1.1, v2.0, …)

---

## 0) Zweck

Diese Spec beschreibt **das Gameplay** von *LAST ONE OUT* aus Spieler- und Regel-Sicht.

Sie definiert:
- Kernziel & Grundregeln
- Bedeutung von SAFE, OUT, Winner
- Training vs Multiplayer aus Gameplay-Perspektive
- verbindliche Begriffe

Sie definiert **nicht**:
- Architektur/Code-Zuständigkeiten (siehe *Architecture Spec*)
- konkrete Mechaniken einzelner Challenges (jede Challenge hat eigene Regeln)

---

## 1) Kernkonzept

### Spielidee
Mehrere Spieler treten in **aufeinanderfolgenden Challenges** gegeneinander an.  
In jeder Challenge können Spieler ausscheiden.  
**Der letzte verbleibende Spieler gewinnt das Match.**

### Grundregel
Ein Match endet **nur**, wenn **genau 1 Spieler** übrig ist.

---

## 2) Begriffe & Zustände

### Active
Spieler ist im Match aktiv und kann:
- Challenge spielen
- SAFE werden
- ausscheiden

### SAFE
Ein Spieler ist **SAFE**, wenn er die Challenge-Bedingung erfüllt hat.

SAFE bedeutet:
- Spieler ist für diese Runde „durch“
- SAFE gilt **nur für die aktuelle Runde**
- SAFE-Spieler können kosmetisch markiert werden

### OUT
Ein Spieler ist **OUT**, wenn er in irgendeiner Challenge eliminiert wurde oder das Match als Active verlässt.

OUT bedeutet:
- Spieler spielt nicht mehr aktiv mit
- Spieler ist Spectator (Zuschauer)

### Spectator
Zuschauer können:
- das Match beobachten
- jederzeit das Spiel verlassen

---

## 3) Multiplayer Gameplay

### Match-Struktur
- Ein Multiplayer-Match besteht aus mehreren Runden (Challenges)
- Jede Runde hat:
  - eine Challenge
  - 0..N Eliminierungen
  - 0..N SAFE-Spieler

### Eliminierungen
- Eine Challenge kann:
  - niemanden eliminieren (Draw / keiner OUT)
  - einen Spieler eliminieren
  - mehrere Spieler eliminieren

### Kein Unentschieden
Falls nach einer Runde **0 Spieler** übrig sind:
- das Match läuft weiter
- es startet eine neue Challenge
- das Match darf **nicht** unentschieden enden

---

## 4) SAFE & Round Winner (Gameplay)

### SAFE-Reihenfolge
SAFE hat eine **Reihenfolge** (1,2,3, …), basierend auf dem Zeitpunkt, wann Spieler SAFE werden.

### Round Winner
- **Round Winner** ist der Spieler, der in einer Runde als **erstes SAFE** wird (SAFE #1).
- Falls in einer Runde niemand SAFE wird:
  - der letzte gültige Round Winner bleibt bestehen (kosmetische Markierung in der Folgerunde möglich)

---

## 5) Challenge-Regeln (Gameplay-Ebene)

### Challenge als „Mini-Spiel“
Jede Challenge ist ein eigenes Mini-Spiel mit eigenen Regeln.

Eine Challenge muss:
- klar definieren, wie Spieler SAFE werden
- klar definieren, wie Spieler OUT werden
- optional: ein Zeitlimit haben

### Mögliche Challenge-Typen (Beispiele)
- Race (Ziel erreichen)
- Survival (überleben bis Ende)
- Puzzle (Bedingung erfüllen)
- Dodge/Obstacle (nicht getroffen werden)

**Hinweis:** Diese Liste ist nicht vollständig und nicht bindend.

---

## 6) Zeitlimit & Timeout

- Eine Challenge kann ein Zeitlimit besitzen.
- Bei Ablauf des Zeitlimits kann:
  - die Challenge enden (Timeout)
  - oder Regeln greifen, die OUT/SAFE definieren

Im Training zählt `timeout` als **lose**.

---

## 7) Leave-Regel (Gameplay-Folge)

- Verlässt ein Active-Spieler das Match:
  - zählt er als OUT
  - erhält 0 Punkte (Punkte gelten nur im Multiplayer Meta)

Spectators dürfen jederzeit leaven.

---

## 8) Training Gameplay

Training ist ein **Singleplayer-Gameplay-Modus**, ohne Match-Struktur.

### Ziel
- üben & wiederholen
- Win/Lose Feedback
- persönliche Bestzeit verbessern

### Start
Training startet **bewusst** (per UI-Start).

### Ergebnis
- `win`: Challenge geschafft
- `lose`: Challenge nicht geschafft
- `timeout`: zählt als lose

### Fortschritt
- Wins / Loses werden gezählt
- Bestzeit wird bei `win` gespeichert (mit Datum)

---

## 9) Punkte (Gameplay-Relevanz)

Punkte existieren **nur im Multiplayer** und haben:
- keinen Einfluss auf Gameplay-Regeln
- keine Auswirkungen auf Challenge-Ergebnisse

Punkte sind rein fürs Leaderboard / Meta.

---

## 10) Spieler-Feedback (UI / Feedback-Ebene)

Spieler müssen klar erkennen:
- welche Challenge als Nächstes kommt (Name + Beschreibung)
- wann die Runde startet (Tür/Countdown)
- ob sie SAFE sind (SAFE-Feedback)
- ob sie OUT sind (LOSE-Feedback)
- wer Round Winner / Match Winner ist

Die konkrete UI-Implementierung ist nicht Teil dieser Spec.

---

## 11) Verbindliche Leitsätze

- **Last One Out:** Match endet nur mit genau 1 verbleibenden Spieler
- **SAFE ist pro Runde:** SAFE resetet jede Runde
- **Challenge ist Gameplay:** Challenge definiert SAFE/OUT
- **Training ist Singleplayer:** ohne Match & ohne Punkte
- **Punkte sind Meta:** keine Gameplay-Auswirkung

---

**ENDE – Gameplay Spec v1.0 (FROZEN)**
