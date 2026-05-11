# NitroMC – Developer Bewerbungsaufgabe

## Aufgabe: Mojang API Integration Plugin

Entwickle ein Minecraft-Plugin für Paper, das die Mojang API verwendet, um Spielerinformationen abzurufen und im Spiel darzustellen.

---

## Funktionsumfang

### `/mcinfo <spielername>`

Zeigt folgende Informationen zu einem Spieler an:

* UUID
* aktueller Name
* Skin- oder Profil-Link
* Zeitpunkt der Abfrage
* Fehlermeldung, falls der Spieler nicht existiert

### `/namehistory <spielername>`

Zeigt bekannte Namensdaten eines Spielers an.

---

## Technische Anforderungen

* Java 17 oder höher
* Paper API
* Gradle oder Maven als Build-Tool
* HTTP-Requests müssen asynchron ausgeführt werden
* Keine API-Abfragen im Main Thread
* JSON-Daten müssen korrekt verarbeitet werden
* Saubere und nachvollziehbare Code-Struktur
* Die vorgegebene `MessageUtil`-Klasse muss für alle Chat-Ausgaben verwendet werden

---

## Empfohlene Dependencies

### `build.gradle`

```gradle
plugins {
    id 'java'
}

group = 'de.nitromc'
version = '1.0.0'

repositories {
    mavenCentral()
    maven {
        name = 'papermc'
        url = 'https://repo.papermc.io/repository/maven-public/'
    }
}

dependencies {
    compileOnly 'io.papermc.paper:paper-api:1.20.4-R0.1-SNAPSHOT'
    implementation 'com.google.code.gson:gson:2.10.1'
}

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}
```

---

## Erwartete Projektstruktur

```text
src/main/java/de/nitromc/api/
├── NitroApiPlugin.java
├── command/
│   ├── McInfoCommand.java
│   └── NameHistoryCommand.java
├── service/
│   └── MojangApiService.java
├── model/
│   └── MojangProfile.java
└── util/
    └── MessageUtil.java
```

---

## Vorgegebene Utility-Klasse

Die folgende Klasse muss im Projekt unter `de.nitromc.api.util.MessageUtil` verwendet werden.

```java
package de.nitromc.api.util;

import java.util.regex.Matcher;
import java.util.regex.Pattern;
import net.md_5.bungee.api.ChatColor;
import org.bukkit.command.CommandSender;

public final class MessageUtil {

    private static final Pattern HEX_PATTERN = Pattern.compile("(#[a-fA-F0-9]{6})");
    private static final String PREFIX = "#9500DD&lN#7F1DE1&lI#693BE5&lT#5358E9&lR#3C75EC&lO#2693F0&lM#10B0F4&lC &8» &7";

    private MessageUtil() {
    }

    public static void send(CommandSender sender, String message) {
        sender.sendMessage(colorize(PREFIX + message));
    }

    public static String prefixed(String message) {
        return colorize(PREFIX + message);
    }

    public static String colorize(String input) {
        Matcher matcher = HEX_PATTERN.matcher(input);
        StringBuilder builder = new StringBuilder();

        while (matcher.find()) {
            matcher.appendReplacement(builder, ChatColor.of(matcher.group(1)).toString());
        }

        matcher.appendTail(builder);
        return ChatColor.translateAlternateColorCodes('&', builder.toString());
    }

    public static String stripColors(String input) {
        String colorized = colorize(input);
        String stripped = ChatColor.stripColor(colorized);
        return stripped == null ? "" : stripped;
    }
}
```

Regeln zur Nutzung:

* Alle Chat-Ausgaben müssen über `MessageUtil.send(...)` oder `MessageUtil.prefixed(...)` laufen.
* Eigene Prefix-Systeme außerhalb dieser Klasse sind nicht erwünscht.
* Farbcodes sollen über `MessageUtil.colorize(...)` verarbeitet werden.

---

## Permissions

```text
nitromc.command.mcinfo
nitromc.command.namehistory
```

---

## `plugin.yml`

```yaml
name: NitroApiPlugin
version: 1.0.0
main: de.nitromc.api.NitroApiPlugin
api-version: '1.20'

commands:
  mcinfo:
    description: Zeigt Mojang-Spielerinformationen an
    usage: /mcinfo <spielername>
    permission: nitromc.mcinfo

  namehistory:
    description: Zeigt Namensinformationen eines Spielers an
    usage: /namehistory <spielername>
    permission: nitromc.namehistory

permissions:
  nitromc.mcinfo:
    default: op
  nitromc.namehistory:
    default: op
```

---

## Hinweise zur API-Nutzung

Das Plugin soll Daten über die Mojang API abrufen. Erwartet wird eine saubere Service-Klasse, zum Beispiel `MojangApiService`, welche die API-Kommunikation kapselt.

Beispiele für sinnvolle Aufgaben der Service-Klasse:

* Spielerprofil anhand eines Spielernamens abrufen
* UUID eines Spielers ermitteln
* Fehlerhafte oder leere API-Antworten behandeln
* Timeouts setzen
* Ergebnisse als eigenes Model, z. B. `MojangProfile`, zurückgeben

---

## Optionale Erweiterungen

* Cache-System zur Reduzierung von API-Anfragen
* Konfigurierbare Nachrichten über eine Config-Datei
* Klickbare Chat-Nachrichten, z. B. Profil-Links
* Sauberes Error-Handling bei Timeouts, ungültigen Antworten oder API-Ausfällen
* Strukturierte Model- und Service-Klassen
* Unit-Tests für API-Service oder Utility-Methoden

---

## Abgabe

Das GitHub-Repository muss enthalten:

* vollständigen Source Code
* `README.md`
* `build.gradle` oder `pom.xml`
* `plugin.yml`
* kurze Erklärung der API-Nutzung
* Installationsanleitung
* Beispielausgaben oder Screenshots

---

## Bewertungskriterien

Bewertet wird:

* Verständnis für externe APIs
* sauberes Async-Handling
* Codequalität und Struktur
* Fehlerbehandlung
* Lesbarkeit des GitHub-Repositories
* sinnvolle Nutzung der vorgegebenen `MessageUtil`-Klasse
