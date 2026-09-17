# Offen

Angenommene Karten, die noch nicht begonnen wurden.

## [K-01] Start der VM prüfen

Betrieb (kein Repo-Änderung). Die verwaiste VM „robotics" wurde am
17.09.2026 entfernt; der Import-Rest
`ubuntu-2404-desktop 1_1789626271467_98177` (UUID ec1663f1) wird von
`.vagrant/machines/default/virtualbox/id` referenziert und von Vagrant
beim nächsten Start übernommen und umbenannt.

- [ ] `vagrant up` ausführen; Info-Banner erscheint, VM heißt „robotics"
- [ ] code-server (192.168.33.10:8080) und JupyterLab (:8888) prüfen

## [K-02] Import-Rest vom 11.09. löschen

Betrieb (kein Repo-Änderung). Fehlgeschlagener Import vom 11.09.2026,
ohne Bezug zu einem laufenden Vagrant-Environment.

- [ ] `VBoxManage unregistervm "ubuntu-2404-desktop 1_1789120975273_22785" --delete`
- [ ] `vagrant global-status --prune` gegen tote Einträge

## [K-03] Vagrantfile: Info-Banner über `post_up_message`

Vorschlag aus der Projektprüfung. Das Zugangsdaten-Banner kommt aktuell
aus einem Shell-Provisioner (`run: "always"`); Vagrant bietet dafür
`config.vm.post_up_message` an. Abzuwägen: verlorene Box-Darstellung,
keine doppelte Ausgabe.

- [ ] Entscheidung: ersetzen oder beibehalten
- [ ] Umsetzung + `vagrant validate` + Changelog-Eintrag

## [K-04] CONTRIBUTING.md anlegen

Fehlende Datei. Dokumentieren: wie die Box (Hetzner Object Storage,
`metadata.json`) gebaut, versioniert und veröffentlicht wird; wie
Vagrantfile-/Doku-Änderungen beigesteuert werden (Commit-Stil,
`vagrant validate`, Changelog-Pflege).

- [ ] Aufbau entwerfen (Beitrag, Box-Build, Release, Doku)
- [ ] Anlegen + Verlinkung im README („Weitere Dokumentation")

## [K-05] `.editorconfig` anlegen

Fehlende Datei. Grundregeln für einheitliche Dateien über Editoren und
KI-Agenten hinweg: UTF-8, `lf`, final newline, trailing whitespace
entfernen, Einrückung je Dateityp (Ruby 2, Python/YAML/Markdown 2–4).

- [ ] Anlegen
- [ ] Vorhandene Dateien prüfen (kein Massen-Reformat)

## [K-06] `.gitattributes` anlegen

Fehlende Datei. Zeilenenden fixieren (`* text=auto` plus `eol=lf` für
Shell/Code) – wichtig für Windows-Nutzer (Git Bash); ggf.
GitHub/Forgejo-Sprachhervorhebung.

- [ ] Anlegen und mit typischen Dateien testen

## [K-07] Codeberg/Forgejo: Issue-Templates ergänzen

`.forgejo/ISSUE_TEMPLATES` für „Problem melden" und „Verbesserung".
Hilft Schülerinnen/Lehrkräften, Fehler strukturiert zu melden.

- [ ] Templates entwerfen und anlegen
- [ ] Aktivierung im Codeberg-Repo prüfen

## [K-08] Forgejo-CI: `vagrant validate` bei Push

Leichte Pipeline (Forgejo Actions): Syntax-Check des Vagrantfiles bei
jedem Push/PR, optional Markdown-Link-Check.

- [ ] Runner-Voraussetzungen klären (Vagrant im Runner installierbar?)
- [ ] Workflow-Datei anlegen

## [K-09] SECURITY.md anlegen

Fehlende Datei. Kurz halten: Meldeweg für Schwachstellen, Verweis auf
Härtung (code-server-Passwort ändern, JupyterLab ohne Token,
`INTERNET-ACCESS.md`).

- [ ] Anlegen und im README verlinken

## [K-10] Git-Tags passend zum Changelog

Changelog nutzt datumsbasierte Abschnitte. Schema entscheiden
(`2026-09-09` … oder `v0.x`) und Tags setzen; künftig je Release taggen.

- [ ] Schema festlegen, bestehende Abschnitte taggen

## [K-11] README: ZIP-Download-Absatz schärfen

Commit bcdfb47 wollte vom ZIP-Download abraten; in „Installation" wird
der Weg aber weiterhin angeboten (nur mit Warnhinweis). Entscheidung
treffen: Absatz entfernen oder klar als „nicht empfohlen" belassen.

- [ ] Entscheidung fällen und umsetzen
