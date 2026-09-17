# TODO – Kanban-Board

Pflege durch eine kleine Gruppe (1–2 Entwickler und KI-Agenten). Das
Board bildet den Workflow in vier Spalten ab, jeweils eine eigene
Markdown-Datei:

| Offen | In Arbeit | Prüfung | Erledigt |
|---|---|---|---|
| [todo/offen.md](todo/offen.md) | [todo/in_arbeit.md](todo/in_arbeit.md) | [todo/pruefung.md](todo/pruefung.md) | [todo/erledigt.md](todo/erledigt.md) |

**Wie eine Karte wandert:** Jede Karte ist ein Abschnitt (`###` mit
K-ID und Titel) plus kurzer Beschreibung und Checkboxen. Neues gehört
nach Bestätigung in `todo/offen.md`; beim Beginnen verschiebst du den
Abschnitt nach `todo/in_arbeit.md`, nach Fertigstellung zur Prüfung
nach `todo/pruefung.md` und nach Freigabe/Commit nach `todo/erledigt.md`
(dort mit Datum, ggf. Commit-Hash). Jede abschließende Änderung wird
zusätzlich in [CHANGELOG.md](CHANGELOG.md) unter „Unreleased"
eingetragen.

## Hinweise für Mitwirkende und KI-Agenten

- Vorschläge werden nur nach ausdrücklicher Bestätigung (durch die
  Gruppe) als Karte aufgenommen – unbestätigte Ideen bleiben im
  Gespräch und stehen nicht im Board.
- Sprache von Doku, Karten und Commit-Botschaften: Deutsch; Commits im
  Stil „Bereich: Kernaussage" (siehe Git-History).
- Änderungen am `Vagrantfile` vor dem Commit mit `vagrant validate`
  prüfen.
- Abgelehnte Karten wandern nach `todo/erledigt.md` mit Vermerk
  „nicht gewünscht" – so bleiben Entscheidungen nachvollziehbar.
- Eine `AGENTS.md` ist in diesem Projekt **nicht** vorgesehen: Dieser
  Abschnitt übernimmt ihre Rolle. Kein `AGENTS.md` anlegen.
