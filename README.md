---

# NitroMC.NET – Developer Bewerbung

## Velocity Netzwerk-Bansystem

---

## Übersicht

Im Rahmen dieser Bewerbungsaufgabe entwickelst du ein netzwerkweites Moderationssystem für einen Velocity-Proxy.
Das System soll in einer realistischen Serverumgebung funktionieren und typische Anforderungen eines produktiven Minecraft-Netzwerks abbilden.

Der Fokus liegt nicht nur auf der Funktionalität, sondern insbesondere auf Architektur, Datenmodellierung, Stabilität und Erweiterbarkeit.

---

## Ziel der Aufgabe

Implementiere ein zentrales Bansystem, das folgende Moderationsmaßnahmen unterstützt:

* Ban / TempBan
* Mute / TempMute
* Kick
* Warn

Das System soll:

* netzwerkweit funktionieren
* persistent sein
* sauber strukturiert implementiert werden
* unter realistischen Bedingungen stabil laufen

---

## Technische Rahmenbedingungen

| Bereich            | Anforderung           |
| ------------------ | --------------------- |
| Plattform          | Velocity              |
| Programmiersprache | Java                  |
| Datenbank          | Pflicht (SQL-basiert) |
| Identifikation     | UUID                  |
| Architektur        | Proxy-zentriert       |

---

## Mindestanforderungen

### Sanktionen erstellen

```
/ban <spieler> <grund>
/tempban <spieler> <dauer> <grund>
/mute <spieler> <grund>
/tempmute <spieler> <dauer> <grund>
/kick <spieler> <grund>
/warn <spieler> <grund>
```

### Sanktionen aufheben

```
/unban <spieler>
/unmute <spieler>
```

### Informationen abrufen

```
/history <spieler>
/check <spieler>
```

---

## Funktionale Anforderungen

### Ban-System

* Login wird bei aktivem Ban verhindert
* Anzeige von Grund, Moderator und Dauer
* automatische Entsperrung bei Ablauf
* keine Namensbasierte Logik – ausschließlich UUID

---

### Mute-System

* Chat wird bei aktivem Mute blockiert
* verständliche Rückmeldung an den Spieler
* automatische Berücksichtigung von Ablaufzeiten

---

### Kick-System

* sofortige Trennung vom Netzwerk
* persistente Speicherung als Moderationsaktion

---

### Warn-System

* Speicherung von Verwarnungen
* vollständige Einsicht in die Historie
* keine automatische Eskalation erforderlich

---

## Datenbank & Persistenz

### Pflicht

* Verwendung einer relationalen Datenbank
* keine reine Datei-basierte Lösung

---

### Erwartetes Datenmodell

Das System sollte mindestens folgende Daten abbilden:

* Sanktions-ID
* Typ
* Spieler-UUID
* Spielername (optional, empfohlen)
* Staff-UUID
* Staff-Name
* Grund
* Erstellungszeitpunkt
* Ablaufzeitpunkt
* Status
* Aufhebungsdaten

---

### Statusmodell (Beispiel)

```
ACTIVE
EXPIRED
REVOKED
EXECUTED
```

---

### Technische Anforderungen

* asynchrone Datenbankzugriffe
* klare Trennung von Repository und Business-Logik
* keine SQL-Statements in Commands oder Listenern

---

## Architektur

Eine saubere Struktur wird erwartet.

Beispiel:

```
command/
service/
repository/
model/
listener/
permission/
message/
```

---

### Empfohlene Services

* SanctionService
* BanCheckService
* HistoryService
* MuteService

---

### Technischer Hinweis

Commands und Listener sollten möglichst wenig Logik enthalten.
Die eigentliche Fachlogik gehört in Services.

---

## Nebenläufigkeit & Konsistenz

Dein System muss korrekt mit parallelen Aktionen umgehen.

Typische Szenarien:

* Zwei Moderatoren bannen gleichzeitig denselben Spieler
* Ein Ban läuft während eines Login-Versuchs ab
* Ein Mute endet während einer Chatnachricht

---

### Erwartung

* keine doppelten Sanktionen
* keine Race Conditions
* konsistente Zustände

---

### Tipp

Validiere den aktuellen Zustand immer erneut, bevor du Änderungen durchführst.

---

## Zeitverarbeitung

Das System muss mit temporären Sanktionen korrekt umgehen.

### Anforderungen

* Unterstützung von Dauerformaten wie:

  * `30m`
  * `12h`
  * `7d`
* korrekte Ablaufberechnung
* zuverlässige Aktivitätsprüfung

---

### Empfehlung

Nutze Java Time APIs:

* Instant
* Duration
* OffsetDateTime

---

## UUID-Handling

* Sanktionen basieren ausschließlich auf UUID
* Namen dienen nur der Anzeige
* Namensänderungen dürfen keine Probleme verursachen

---

## Permissions

Beispielstruktur:

```
nitromc.punish.ban
nitromc.punish.tempban
nitromc.punish.unban
nitromc.punish.mute
nitromc.punish.tempmute
nitromc.punish.unmute
nitromc.punish.kick
nitromc.punish.warn
nitromc.punish.history
nitromc.punish.check
```

---

## Login- und Chat-Integration

### Login

* Prüfung erfolgt direkt beim Verbindungsaufbau
* keine unnötigen Verzögerungen

### Chat

* aktive Mutes blockieren Nachrichten
* klare Fehlermeldungen

---

## History-System

* aktive und vergangene Sanktionen einsehbar
* klare Statusanzeige
* vollständige Informationen

---

## Performance

* keine blockierenden Operationen
* effiziente Datenbankzugriffe
* optionales Caching möglich

---

### Hinweis

Ein falscher Cache ist schlimmer als kein Cache.

---

## Fehlerbehandlung

Das System muss robust sein.

### Beispiele

* Datenbank nicht erreichbar
* ungültige Dauer
* Spieler nicht gefunden

---

### Erwartung

* sinnvolle Logs
* keine stillen Fehler
* stabile Laufzeit

---

## Git-Anforderungen

### Pflicht

* Nutzung von Git
* saubere Commit-Historie

---

### Gute Beispiele

```
Add sanction model and status system
Implement async punishment repository
Add login ban check via Velocity event
Implement duration parser for temp bans
```

---

### Schlechte Beispiele

```
fix
update
final
test
```

---

## Codequalität

Wichtig:

* klare Struktur
* lesbare Namen
* keine God Classes
* keine unnötige Komplexität

---

## Dokumentation

Deine README muss enthalten:

* Projektbeschreibung
* Setup-Anleitung
* Datenbank-Konfiguration
* Command-Übersicht
* Permission-Übersicht

---

## Optionale Features

* IP-Bans
* Case-System
* Staff-Notizen
* Eskalationslogik
* GUI oder Pagination
* REST-/API-Vorbereitung

---

## Bewertung

Wir achten besonders auf:

* Architektur
* Datenmodell
* Stabilität
* Codequalität
* Git-Workflow
* Praxistauglichkeit

---

## Abgabe

* Git Repository
* vollständiger Code
* funktionierende Konfiguration
* README
