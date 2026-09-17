# Erledigt

Abgeschlossene Karten (mit Datum, ggf. Commit-Hash) sowie nicht
gewünschte Vorschläge mit Vermerk.

## [D-01] Namenskollision „robotics" beheben und dokumentieren

Verwaiste VirtualBox-VM „robotics" (UUID 95b17fe0…) am 17.09.2026 mit
`VBoxManage unregistervm --delete` entfernt. Lösung (Kommandozeile und
GUI) im README unter „Häufige Probleme" dokumentiert; `DISK_SIZE`
dokumentiert; TODO-Board und `CHANGELOG.md` angelegt.
– Erledigt am 17.09.2026.

## [D-02] VM-Name im Vagrantfile kollisionssicher machen — nicht gewünscht

Entscheidung vom 17.09.2026: Der feste Name „robotics" bleibt;
kollisionsfreie Namen (Ordner-Suffix/Hash, `VM_NAME`-Override) wurden
erwogen, aber abgelehnt – der Fehlerfall ist stattdessen dokumentiert
(siehe D-01).

## [D-03] `.opencode/` in `.gitignore` aufnehmen

Bestätigt am 17.09.2026: Das lokale Verzeichnis der KI-Assistent-Dateien
wird vom Git ausgeschlossen.
