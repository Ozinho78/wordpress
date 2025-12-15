# WordPress Projekt - Checkliste

> Bitte erfülle alle Punkte auf dieser Liste, bevor du das Projekt einreichst. Solltest du weitere Extras eingebaut haben, erwähne das kurz, damit sich die Mentoren dies bei Bedarf anschauen können.

## Inhaltsverzeichnis

- [1. Repository](#1-repository)
  - [Vorhandene Dateien](#vorhandene-dateien)
  - [docker-compose.yaml](#docker-composeyaml)
  - [README.md](#readmemd)
- [2. Dokumentation](#2-dokumentation)
- [3. Hinweise](#3-hinweise)
  - [Sicherheitshinweise](#sicherheitshinweise)
  - [Code-Konventionen](#code-konventionen)
  - [Testing](#testing)

---

## 1. Repository

### Vorhandene Dateien

- [x] Es wurde eine `.gitignore` Datei angelegt, die alle irrelevanten Inhalte aus dem git repository ignoriert
- [x] Es gibt eine `docker-compose.yaml`, die den Anforderungen des nächsten Punkts genügt
- [x] Eine Datei namens `README.md` ist vorhanden und entsprechend der Kriterien unten erstellt worden
- [x] Es befinden sich keine weiteren Dateien im Repository, ohne dass diese explizit in der README.md benannt und beschrieben werden

### docker-compose.yaml

- [x] Es gibt zwei Services, die definiert werden: `wordpress` und `db`
- [x] Es gibt eine Env Konfiguration für den Wordpress Service bei der alle unkritischen Variablen konfiguriert werden (keine Auth!)
- [x] Es gibt Volumes-Konfigurationen für die Datenbank, sodass die Inhalte auf einem Dateisystem persistiert werden
- [x] Es muss sichergestellt sein, dass beide Services im selben Netzwerk betrieben werden

### README.md

- [x] Die README sollte ein Inhaltsverzeichnis a.k.a. eine Table-of-Contents (ToC) enthalten
- [x] Eine Sektion mit einer Beschreibung des Repositories muss vorhanden sein. In dieser Beschreibung sollte genannt werden was die wesentlichen Inhalte sind, was der Zweck des Repositories ist
- [x] Eine Sektion "Quickstart" sollte als Teil der README enthalten sein. Hier sollten kurz Voraussetzungen genannt und eine Schnellstart-Anleitung beschrieben sein
- [x] Es ausführliche Variante der vorgenannten Sektion so als "Usage" enthalten sein. Hier soll genauer auf die Konfiguration und Konfigurierbarkeit eingegangen werden, d.h. es soll auch erklärt werden wie relevante Passagen modifiziert werden können, um andere Resultate zu erzielen

---

## 2. Dokumentation

Die Dokumentation des Codes, sowie des Projektes soll im Repository in Form einer README Datei stattfinden.

**Wichtig:** Die Dokumentationssprache für alle Projekte (und zugehörige Unterlagen) ist **Englisch**.

> 💡 **Hinweis:** ChatGPT kann hier helfen 😉

---

## 3. Hinweise

### Sicherheitshinweise

- [x] Speichere keine SSH-Keys im Workspace deines Git-Repositories
- [x] Speichere keine Passwörter, Tokens, oder Benutzernamen in deinem Code. Verwende hierfür stattdessen Environment-Variablen
- [x] Speichere keine IP-Adressen, oder sonstigen sensiblen Informationen in einem Git Repository

### Code-Konventionen

- [x] Für build-args, environment Variablen und Shell-Variablen gilt folgende Namenskonvention: `UPPER_CASE_WITH_UNDERSCORE`
- [x] Bei einer Referenz auf eine Variable sollte immer die `{}`-Notation verwendet werden um Fehler in der Interpretation zu vermeiden: `${SOME_VAR_VALUE}`, statt: `$SOME_VAR_VALUE`
- [x] Es sollten für build-args, oder Environment Variablen "Default"-Werte konfiguriert werden, allerdings nur dann wenn dies Sinn ergibt
- [x] Kritische Konfiguration wie Tokens, Passwörter oder ähnliches sollte nicht im Code-Repository gespeichert sein, sondern bspw. durch die Verwendung eines `.env`-files in einen Container hineingegeben werden

### Testing

Bevor du dein Projekt einreichst, solltest du die folgenden Dinge sichergestellt und getestet haben:

- [x] Wordpress ist erreichbar unter der IP-Adresse deiner Cloud-VM auf Port 8080
- [x] Man kann sich mit den von Ihnen konfigurierten Admin-Benutzer-Credentials einloggen und Wordpress navigieren
- [x] Nach einem Neustart des Setups, sind die konfigurierten Daten noch vorhanden und werden nicht gelöscht oder überschrieben
- [x] Die Container der Services werden neugestartet, sobald ein Fehler passiert der zum Terminieren des Containers führt

