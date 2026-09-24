# Changelog

Alle nennenswerten Änderungen an diesem Projekt werden in dieser Datei
dokumentiert. Das Format orientiert sich an
[Keep a Changelog](https://keepachangelog.com/de/1.1.0/). Die Abschnitte
sind datumsbasiert (JJJJ-MM-TT); die Box „ubuntu-2404-desktop" wird
separat versioniert (aktuell 26.09.11).

## [Unreleased]

### Vagrant
- VM-Name per Umgebungsvariable überschreibbar (`VM_NAME=… vagrant up`,
  Default bleibt `robotics`) — Parallel-VMs (z. B. eine Build-VM neben
  der Schul-VM) kollidieren nicht mehr im VirtualBox-Manager.

### Dokumentation
- README: Fehlerbehebung für Namenskollision „A VirtualBox machine with
  the name 'robotics' already exists" in „Häufige Probleme" ergänzt –
  per Kommandozeile und VirtualBox-GUI, inkl. Aufräumen fehlgeschlagener
  Import-Reste.
- README: Umgebungsvariable `DISK_SIZE` für die Disk-Größe dokumentiert.
- TODO-Board als Kanban angelegt: `todo.md` (Übersicht + Hinweise für
  Mitwirkende und KI-Agenten) mit den Spalten `todo/offen.md`,
  `todo/in_arbeit.md`, `todo/pruefung.md`, `todo/erledigt.md`.
- Diese `CHANGELOG.md` angelegt.
- `.gitignore`: lokales `.opencode/`-Verzeichnis ausgeschlossen.

## [2026-09-16]

### Vagrant
- Disk-Größe auf 64 GB erhöht; beim Start werden Partition und
  Dateisystem automatisch vergrößert (LVM- und ext4-Zweig).
- Disk-Größe per Umgebungsvariable überschreibbar
  (`DISK_SIZE=… vagrant up`).

## [2026-09-11]

### Dokumentation
- Unterrichtskonzept für projektorientierten IT-Unterricht ergänzt
  (Lab-VM, Raspberry Pi, STM32, ROS 2).
- Anleitung: SD-Karte per USB-Reader in die VM durchreichen.

## [2026-09-09]

Erste Fassung der Umgebung: Ubuntu 24.04 Desktop (deutsch) mit
ROS 2 Jazzy, code-server (Port 8080), JupyterLab (Port 8888) und
PlatformIO – ohne zusätzliches Provisioning.

### Vereinfachungen
- Ansible-Provisioning entfernt; `Vagrantfile` ins Repository-Root
  verschoben.
- Geteilte Ordner getrennt: `workspace/` (Teilnehmer-Arbeit, per
  `.gitignore` ausgeschlossen) von `notebooks/` und `examples/`
  (per Git verteiltes Material).

### Dokumentation
- README für Einsteiger ausgebaut; von ZIP-Download abraten, Git
  empfehlen; `INTERNET-ACCESS.md` ergänzt; MIT-Lizenz hinzugefügt.
