# Robotik-Entwicklungsumgebung

Eine fertig eingerichtete virtuelle Maschine (VM) für Robotik-Software.
Sie enthält Ubuntu, ROS 2, eine Entwicklungsumgebung im Browser und
Werkzeuge zum Programmieren von Arduino/ESP32-Boards.

Diese Anleitung richtet sich an Personen **ohne Vorerfahrung** mit Git,
Vagrant oder ROS 2. Du brauchst nur die Schritte weiter unten zu befolgen.

---

## Was du am Ende hast

Nach der Einrichtung läuft auf deinem Rechner eine virtuelle Maschine mit:

| Werkzeug | Zweck | Adresse |
|---|---|---|
| Ubuntu 24.04 Desktop | Grafische Arbeitsumgebung (deutsch) | im VM-Fenster |
| ROS 2 Jazzy | Robotik-Framework, Befehle `ros2`, `colcon` | Terminal in der VM |
| code-server | VS Code im Webbrowser | `http://192.168.33.10:8080` |
| JupyterLab | Python-/ROS-Notebooks im Webbrowser | `http://192.168.33.10:8888` |
| PlatformIO | Programmieren & Flashen von Arduino/ESP32/STM32 | in code-server integriert |

Alle Werkzeuge sind bereits in der VM-Vorlage (der „Box") enthalten.
Es findet keine zusätzliche Software-Installation statt – nach dem
Start ist alles einsatzbereit.

Eine **virtuelle Maschine (VM)** ist ein simulierter Computer, der als
Programm auf deinem echten Rechner läuft. Du kannst sie starten, benutzen
und wieder löschen, ohne dass sich etwas an deinem eigentlichen System
ändert.

---

## Voraussetzungen

Du brauchst drei Programme auf deinem Rechner. Alle drei sind kostenlos
und laufen unter Windows, macOS und Linux.

1. **[VirtualBox](https://www.virtualbox.org/wiki/Downloads)**
   Führt die virtuelle Maschine aus.

2. **[Vagrant](https://developer.hashicorp.com/vagrant/downloads)**
   Startet und konfiguriert die VM automatisch anhand einer Textdatei
   (dem sogenannten `Vagrantfile`, das in diesem Repository liegt).

3. **[Git](https://git-scm.com/downloads)**
   Lädt dieses Repository herunter (Vorgang: „klonen").

4. **Optional: VirtualBox Extension Pack** (gleiche Version wie
   VirtualBox!) – nur nötig, wenn du Arduino/ESP32-Boards per USB an
   die VM durchreichen willst, um sie zu flashen. Für den normalen
   Betrieb ist es nicht erforderlich.

**Systemvoraussetzungen des Rechners:** mindestens 8–10 GB freier
Arbeitsspeicher und ausreichend Festplattenplatz (mehrere zehn GB),
da die VM eine komplette Ubuntu-Installation enthält.

---

## Installation

### 1. Repository klonen

Öffne ein Terminal (Windows: „Git Bash", das mit Git installiert wurde)
und gib ein:

```bash
git clone <URL-dieses-Repositories>
cd <repo-ordner>
```

Falls du Git nicht per Kommandozeile nutzen möchtest: Auf der
Repository-Seite gibt es einen Button „Code" → „Download ZIP". Entpacke
die ZIP-Datei anschließend an einem Ort deiner Wahl.

### 2. VM starten

```bash
vagrant up
```

Beim ersten Start passiert Folgendes automatisch:

- Die VM-Vorlage („Box") wird heruntergeladen (mehrere GB, je nach
  Internetverbindung dauert das etwas).
- Die VM wird erstellt und gestartet.

Das kann bei der ersten Ausführung **einige Minuten** dauern, je nach
Internetverbindung. Anschließend öffnet sich ein Fenster mit dem
Ubuntu-Desktop.

### 3. Zugangsdaten ablesen

Am Ende jedes Starts zeigt das Terminal eine Übersicht mit den
Zugangsadressen für den Browser:

```txt
╔═══════════════════════════════════════════════════════════╗
║   code-server (VS Code im Browser)                        ║
║     Adresse:  http://192.168.33.10:8080                   ║
║     Passwort: change_me                                   ║
║                                                           ║
║   JupyterLab (Python-/ROS-Notebooks)                      ║
║     Adresse:  http://192.168.33.10:8888                   ║
║     Login:    ohne Passwort (nur im Heimnetz nutzen!)     ║
╚═══════════════════════════════════════════════════════════╝
```

Diese Übersicht erscheint bei **jedem** Start der VM erneut, nicht nur
beim ersten Mal.

---

## Ergebnis prüfen

- Im geöffneten VM-Fenster siehst du einen Ubuntu-Desktop.
- Öffne im Browser deines **Host-Rechners** (nicht in der VM!) die
  Adresse `http://192.168.33.10:8080` → code-server sollte laden und
  nach dem Passwort `change_me` fragen.
- Öffne zusätzlich `http://192.168.33.10:8888` → JupyterLab sollte
  ohne Anmeldung laden.

---

## Erste Schritte in der VM

Öffne in code-server (`http://192.168.33.10:8080`) ein Terminal
(Menü → Terminal → New Terminal) und probiere aus:

**ROS 2 testen:**

```bash
ros2 topic list                  # aktive ROS-Themen anzeigen
ros2 run demo_nodes_cpp talker   # Demo-Sender starten
ros2 topic echo /chatter         # Nachrichten live mitlesen (neues Terminal)
```

**Eigenes ROS-Paket anlegen:**

```bash
mkdir -p ~/ros2_ws/src && cd ~/ros2_ws/src
ros2 pkg create mein_paket --build-type ament_python
cd ~/ros2_ws && colcon build && source install/setup.bash
```

`colcon` ist das Build-Werkzeug von ROS 2 – es übersetzt und verlinkt
deine Pakete.

**Mikrocontroller programmieren (PlatformIO):**

- In code-server: Seitenleiste → PlatformIO-Symbol → „New Project"
- Oder im Terminal: `pio init --board uno && pio run`
- Zum Flashen muss das Board per USB an die VM durchgereicht werden
  (siehe Abschnitt „USB-Geräte durchreichen" unten).

---

## Nützliche Befehle

Alle Befehle werden im Terminal deines **Host-Rechners** im
Repository-Ordner ausgeführt (nicht in der VM):

| Befehl | Wirkung |
|---|---|
| `vagrant up` | VM starten (oder fortsetzen) |
| `vagrant ssh` | Terminal-Zugriff auf die VM per Kommandozeile |
| `vagrant suspend` | VM einfrieren – schnelles Fortsetzen später |
| `vagrant halt` | VM sauber herunterfahren |
| `vagrant destroy` | VM vollständig löschen (die Box selbst bleibt erhalten) |
| `vagrant reload` | VM neu starten (z. B. nach Änderungen am Vagrantfile) |

---

## USB-Geräte durchreichen (Arduino, ESP32, ST-Link)

Um ein Board zu flashen, muss es der VM als USB-Gerät zugewiesen
werden:

1. VirtualBox Extension Pack installieren (gleiche Version wie
   VirtualBox, siehe oben).
2. VM-Fenster öffnen → Menü „Geräte" → „USB" → dein Board auswählen.
3. In der VM sollte das Board nun z. B. unter `/dev/ttyUSB0` oder
   `/dev/ttyACM0` erscheinen.

Falls das Board nicht erscheint: prüfe, ob das Extension Pack korrekt
installiert ist (`vagrant halt` und `vagrant up` nach der Installation
wiederholen).

---

## Rückgängigmachen / Aufräumen

- **VM anhalten, Daten bleiben erhalten:** `vagrant halt`
- **VM vollständig entfernen:** `vagrant destroy` (danach mit
  `vagrant up` wieder neu erstellbar)
- **Auch die heruntergeladene Box entfernen:**
  `vagrant box remove ubuntu-2404-desktop`
- **Repository entfernen:** Ordner einfach löschen, nachdem die VM
  zerstört wurde.

---

## Häufige Probleme

- **`vagrant up` bricht mit einem Netzwerkfehler ab:** meist liegt es
  am Download der Box – Internetverbindung prüfen und erneut
  versuchen.
- **VM-Fenster bleibt schwarz oder friert ein:** in den
  BIOS/UEFI-Einstellungen deines Rechners prüfen, ob
  Virtualisierung (Intel VT-x / AMD-V) aktiviert ist.
- **Browser-Adressen sind nicht erreichbar:** prüfen, ob die VM
  vollständig gestartet ist (Zugangsdaten-Übersicht im Terminal muss
  erschienen sein), und ob eine Firewall den Zugriff blockiert.

---

## Sicherheitshinweis

Diese Umgebung ist für den Einsatz im **eigenen, vertrauenswürdigen
Netzwerk** (z. B. zu Hause oder im Schulnetz) gedacht:

- code-server nutzt das Standardpasswort `change_me` – bitte ändern.
- JupyterLab läuft ganz ohne Passwort.

Bevor du die VM in einem Netzwerk mit fremdem Zugriff (z. B. offenes
WLAN, Internet) betreibst, lies unbedingt die Datei
`INTERNET-ACCESS.md` im Repository.
