# Eine Robotics-Lab-VM als Grundlage für projektorientierten IT-Unterricht

## Einleitung

Projektarbeiten im zweiten Schuljahr verlangen mehr als einzelne Programmierkenntnisse. Die Schülerinnen und Schüler müssen technische Systeme planen, Komponenten auswählen, Software installieren, Schnittstellen verstehen, Fehler suchen und ihre Ergebnisse dokumentieren.

Ein Unterrichtsbeispiel mit **Linux, ROS 2, Raspberry Pi, STM32 und einem Roboter-Board** kann diese Fähigkeiten praxisnah vorbereiten. Dabei sollte jedoch nicht der fertige Roboter im Mittelpunkt stehen. Entscheidend ist der Weg vom einzelnen Systembestandteil zur funktionierenden, dokumentierten Projektlösung.

Die Robotics-Lab-VM bildet dafür eine einheitliche Entwicklungsumgebung.

---

## Vom Lerngebiet zur Projektarbeit

Das Lerngebiet „Computersysteme konfigurieren und in Betrieb nehmen“ behandelt unter anderem:

- Hardware- und Softwarekomponenten,
- Betriebssysteme,
- Dateisysteme,
- Schnittstellen,
- Datensicherung,
- Fehlermeldungen,
- Updates und Systemtests.

Eine Roboterplattform verbindet diese Inhalte in einem konkreten technischen Zusammenhang:

```text
Lab-VM
  ↓
Entwicklung, Tests und Dokumentation

Raspberry Pi
  ↓
Linux und ROS 2 auf dem Zielsystem

USB-Seriell-Verbindung
  ↓
Kommunikation mit dem Controller

STM32-Board
  ↓
Motoren, Sensoren und weitere Schnittstellen
```

Die Schülerinnen und Schüler erkennen dadurch, dass ein technisches System aus mehreren Ebenen besteht. Eine Fehlfunktion kann beispielsweise am Programm, am Betriebssystem, an der seriellen Verbindung, an der Firmware oder an der Stromversorgung liegen.

---

## Die Robotics-Lab-VM als gemeinsamer Projektstandard

Die Lab-VM sollte nicht nur eine Sammlung installierter Programme sein. Sie sollte einen definierten Ausgangspunkt für alle Projektgruppen schaffen.

Dazu gehören beispielsweise:

- ein festgelegtes Linux-System,
- Python und grundlegende Entwicklungswerkzeuge,
- ROS 2 in einer definierten Version,
- Git und der Zugriff auf Codeberg,
- vorbereitete Projektverzeichnisse,
- Beispielprogramme,
- Testskripte,
- Dokumentationsvorlagen,
- Werkzeuge zur Diagnose und Protokollierung.

Die VM übernimmt damit die vorbereitende Infrastruktur. Die Lernenden müssen nicht jede Abhängigkeit selbst installieren und verlieren weniger Zeit durch unterschiedliche Rechnerkonfigurationen.

Gleichzeitig darf die VM keine vollständige Lösung liefern. Die Schülerinnen und Schüler sollen weiterhin selbst:

- Programme verändern,
- Fehler analysieren,
- Tests durchführen,
- Schnittstellen beschreiben,
- Ergebnisse dokumentieren,
- eigene Erweiterungen planen.

Die VM reduziert also technische Zufälle, aber nicht die eigentliche Denkarbeit.

---

## Die drei zentralen Systemebenen

### 1. Die virtuelle Entwicklungsumgebung

In der VM entwickeln und testen die Schülerinnen und Schüler ihre Programme. Sie lernen dort:

- Linux-Befehle,
- Dateisysteme und Rechte,
- Projektstrukturen,
- Git und Versionsverwaltung,
- Python,
- ROS-2-Grundlagen,
- Dokumentation und Datensicherung.

Die VM ist außerdem ein sicherer Ort für erste Versuche. Fehler können gemacht und zurückgesetzt werden, ohne sofort reale Hardware zu gefährden.

### 2. Der Raspberry Pi als Zielsystem

Der Raspberry Pi stellt die Verbindung zwischen Entwicklungsumgebung und realer Hardware her. Auf ihm laufen Linux, ROS 2 und die für den Roboter notwendigen Programme.

Damit wird ein wichtiger Unterschied sichtbar:

> Ein Programm, das in der VM funktioniert, ist noch nicht automatisch auf dem Zielsystem einsatzbereit.

Die Lernenden müssen Software übertragen, Abhängigkeiten prüfen, Netzwerkverbindungen einrichten und das Verhalten auf dem Raspberry Pi testen. Diese Erfahrung ist für spätere Projektarbeiten besonders wichtig.

### 3. Das Controller-Board mit STM32

Das Roboter-Board stellt die hardwarenahe Ebene dar. Der STM32 verarbeitet beispielsweise Signale von Sensoren und steuert Motoren oder andere Ausgänge.

Im ersten Ausbildungsabschnitt sollte der Mikrocontroller nicht zwingend selbst programmiert werden. Er kann zunächst als fertige, aber untersuchbare Systemkomponente dienen.

Die Lernenden können beispielsweise prüfen:

- Wird das USB-Gerät unter Linux erkannt?
- Welche Schnittstelle wird verwendet?
- Welche Daten werden übertragen?
- Wie lässt sich eine Statusmeldung auslesen?
- Wie wird ein Sensorwert weitergegeben?
- Wie kann ein ungefährlicher Motortest durchgeführt werden?

So lernen sie die Funktion des Controllers kennen, ohne dass die Komplexität einer vollständigen STM32-Entwicklung den gesamten Unterricht bestimmt.

---

## ROS 2 als Strukturierungsmittel

ROS 2 sollte nicht als Selbstzweck eingeführt werden. Die Schülerinnen und Schüler müssen nicht möglichst viele ROS-Befehle auswendig lernen. Sie sollen verstehen, wie Softwarekomponenten miteinander kommunizieren.

Dazu gehören grundlegende Begriffe wie:

- **Node:** ein eigenständiges Programm oder eine funktionale Softwarekomponente,
- **Topic:** ein benannter Kommunikationskanal,
- **Message:** die übertragene Nachricht,
- **Launch-Datei:** eine Beschreibung zum gemeinsamen Start mehrerer Komponenten.

Ein einfacher Datenfluss könnte so aussehen:

```text
Sensor
  ↓
STM32
  ↓ USB-Seriell
Raspberry Pi
  ↓
ROS-2-Node
  ↓
Topic
  ↓
Anzeige, Protokoll oder Steuerung
```

Wichtig ist, dass die technische Ursache vor der ROS-Struktur betrachtet wird:

1. Welcher Sensor erzeugt die Daten?
2. Wie werden die Daten übertragen?
3. Welches Programm empfängt sie?
4. In welchem Format liegen sie vor?
5. Welche Anwendung verarbeitet sie?
6. Wie wird das Ergebnis geprüft?

Dadurch bleibt ROS 2 ein Werkzeug zur Strukturierung und nicht ein undurchsichtiges Zusatzsystem.

---

## Vom Roboterprodukt zum Projektsystem

Ein fahrender Roboter wirkt zunächst wie ein eindeutiges Erfolgserlebnis. Für die Vorbereitung auf Projektarbeiten ist das allein jedoch kein ausreichendes Ziel.

Ein System kann sich bewegen und trotzdem schlecht geplant sein. Mögliche Schwächen sind:

- unklare Zuständigkeiten,
- fehlende Dokumentation,
- manuelle und nicht reproduzierbare Installationsschritte,
- nicht getestete Software,
- ungesicherte Konfigurationen,
- fehlende Fehlerbehandlung,
- unklare Hardwarezuordnung.

Deshalb sollten nicht nur sichtbare Funktionen bewertet werden. Wichtiger sind die Prozesse, die zu einer stabilen Lösung führen.

Geeignete Fragen sind:

- Ist die Systemarchitektur verständlich dargestellt?
- Sind Hardware und Software eindeutig zugeordnet?
- Kann eine andere Person die Installation nachvollziehen?
- Gibt es einen Testplan?
- Werden Fehler systematisch eingegrenzt?
- Sind Änderungen über Git nachvollziehbar?
- Wurden Risiken und Grenzen dokumentiert?
- Kann das System in einen bekannten Ausgangszustand zurückgesetzt werden?

---

## Mögliche Unterrichtsstruktur

Für ein Lerngebiet mit etwa 40 Stunden bietet sich eine schrittweise Struktur an.

### Phase 1: Systemüberblick

Die Lernenden untersuchen:

- die Aufgaben von VM, Raspberry Pi und Controller,
- Hardware- und Softwarekomponenten,
- Datenflüsse,
- Schnittstellen,
- mögliche Fehlerquellen.

### Phase 2: Arbeiten mit der Lab-VM

Schwerpunkte sind:

- Linux-Dateisystem,
- Rechte und Verzeichnisse,
- Python,
- Git,
- Codeberg,
- Projektstruktur,
- Datensicherung.

### Phase 3: Raspberry Pi in Betrieb nehmen

Die Lernenden:

- richten das Linux-Zielsystem ein,
- stellen eine Netzwerkverbindung her,
- verwenden SSH,
- prüfen angeschlossene Geräte,
- untersuchen Logs und Fehlermeldungen.

### Phase 4: Hardware testen

Mögliche Aufgaben:

- serielle Schnittstelle erkennen,
- Statusdaten auslesen,
- einen Sensorwert anzeigen,
- eine LED ansteuern,
- einen sicheren Motortest durchführen,
- Testergebnisse protokollieren.

### Phase 5: ROS 2 einführen

Die Lernenden erstellen oder untersuchen:

- einen einfachen Node,
- ein Topic,
- eine Nachricht,
- eine Startdatei,
- einen kleinen Datenfluss zwischen zwei Komponenten.

### Phase 6: Kleine Projektaufgabe

Zum Abschluss bearbeiten die Gruppen eine begrenzte Aufgabe, zum Beispiel:

- Sensorwerte protokollieren,
- eine Diagnosefunktion erstellen,
- einen Motorsteuerungsablauf absichern,
- einen ROS-2-Node erweitern,
- einen automatisierten Systemtest entwickeln.

---

## Grenzen und Risiken

Die Plattform ist leistungsfähig, aber nicht automatisch einfach.

### Technische Komplexität

VM, Raspberry Pi, ROS 2, USB-Kommunikation und STM32 bilden mehrere voneinander abhängige Ebenen. Fehler können dadurch schwer zuzuordnen sein.

### Versionsabhängigkeiten

Linux-Version, ROS-2-Version, Python-Pakete, Firmware und Herstellerbeispiele müssen zusammenpassen. Die Lab-VM sollte deshalb eine eindeutig dokumentierte Version enthalten.

### Herstellerabhängigkeit

Die Beispiele des Board-Herstellers können unterschiedlich aufgebaut sein und verschiedene Entwicklungswege enthalten. Für den Unterricht muss daraus ein klar begrenzter, geprüfter Pfad ausgewählt werden.

### Sicherheit

Motoren und Stromversorgung dürfen nicht als reine Softwarethemen behandelt werden. Ersttests sollten mit angehobenen Rädern, klarer Abschaltmöglichkeit und geprüfter Verkabelung erfolgen.

### Überforderung

Wenn alle Funktionen des Boards gleichzeitig behandelt werden, verschiebt sich der Unterricht von Systemverständnis zu Fehlersuche in fremden Beispielen. Deshalb sollten nur wenige Kernfunktionen verbindlich sein.

---

## Fazit

Die Robotics-Lab-VM kann eine tragfähige Grundlage für die Vorbereitung auf Projektarbeiten im zweiten Schuljahr bilden. Ihr besonderer Wert liegt nicht in der Robotik allein, sondern in der Verbindung mehrerer technischer Ebenen:

```text
Entwicklungsumgebung
→ Versionsverwaltung
→ Linux-Zielsystem
→ Hardware-Schnittstelle
→ Mikrocontroller
→ ROS-2-Software
→ Tests und Dokumentation
```

Der Unterricht sollte dabei nicht das Ziel verfolgen, einen möglichst umfangreichen autonomen Roboter zu bauen. Sinnvoller ist eine begrenzte, nachvollziehbare Systemaufgabe, an der die Lernenden Planung, Konfiguration, Inbetriebnahme, Fehlersuche und Dokumentation üben.

Die zentrale Leitidee lautet:

> Nicht der fertige Roboter ist das eigentliche Lernziel, sondern die Fähigkeit, ein technisches System schrittweise zu verstehen, aufzubauen, zu prüfen und als Projekt weiterzuentwickeln.
