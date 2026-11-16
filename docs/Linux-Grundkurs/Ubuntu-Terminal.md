# Ubuntu-Terminal: Die Befehle, die Du für ROS 2 brauchst

Noch nie mit Linux gearbeitet? Kein Problem. Hier lernst Du genau das, was Du für den Einstieg in ROS 2 brauchst. ROS 2 (Robot Operating System 2, ein Software-Baukasten für Roboterprogramme) läuft vor allem unter Ubuntu, einer Linux-Variante, und fast jede Anleitung dazu besteht aus Terminal-Befehlen.

Das hier ist kein vollständiger Linux-Kurs. Du bekommst das Handwerkszeug, ohne das die ROS-2-Tutorials nicht funktionieren. Alles ist auf **Ubuntu 24.04 LTS** und **ROS 2 Jazzy Jalisco** zugeschnitten.

**Vorab eine Einschränkung**

Die Übungsbefehle bis zum Abschnitt „Aufräumen" fassen nur Dateien im Ordner `~/uebung_terminal` an, solange Du sie unverändert abtippst. Zwei Ausnahmen gibt es: `sudo apt ...` greift ins System ein, und `echo ... >> ~/.bashrc` verändert eine Konfigurationsdatei in Deinem Home-Ordner. Beides wird an Ort und Stelle erklärt. Ein Löschbefehl mit falschem Pfad kann dagegen auch andere Dateien treffen.

**Wozu überhaupt ein Terminal?**

Grafische Oberflächen ändern sich ständig: Menüs wandern, Buttons heißen plötzlich anders. Das Terminal bleibt, wie es ist. Anleitungen dafür halten jahrelang. ROS 2 wird zudem fast ausschließlich darüber bedient. Dran vorbei kommst Du also nicht, aber der Weg ist kürzer, als er aussieht.

**Terminal, Shell, Kommandozeile**

Im Netz wird das gern synonym benutzt, und irgendwo tippt gerade jemand empört einen Absatz über die Unterschiede. Der Vollständigkeit halber:

- Das **Terminal** ist das Fenster, in dem Du tippst und liest.
- Die **Shell** ist das Programm darin, das Deine Eingaben versteht und ausführt. Unter Ubuntu ist das standardmäßig **Bash**.
- **Kommandozeile** meint die Arbeitsweise: Befehle als Text eingeben.

Im Alltag sagt jeder „Terminal" und meint alles zusammen.

**Terminal öffnen:** `Strg+Alt+T`

**Kopieren und Einfügen:** `Strg+Umschalt+C` und `Strg+Umschalt+V`. Finger weg von `Strg+C` allein: Das bricht laufende Programme ab, statt zu kopieren. In ROS 2 brauchst Du genau das ständig, um Programme zu beenden.

Die Befehle hier kannst Du per Copy & Paste übernehmen. Klappt etwas nicht, lies zuerst die Fehlermeldung und schau in den Abschnitt „Typische Anfängerfehler". Meist ist es ein Tippfehler oder der falsche Ordner. Abgeschickt wird mit `Enter`.

**So liest Du die Beispiele**

In den Codeblöcken steht, was Du eintippst. Zeilen mit `#` sind Kommentare: Sie erklären etwas oder zeigen die erwartete Ausgabe. Die tippst Du nicht ein.

Vor Deiner Eingabe zeigt das Terminal eine Zeile wie `name@rechner:~$`. Das ist der **Prompt**. Das `~` verrät Dir, in welchem Ordner Du gerade steckst. Das `$` tippst Du nie mit.

**Dateisystem in drei Sätzen**

Dateien liegen in Ordnern, Ordner in Ordnern, bis ganz oben zur Wurzel `/`. Dein persönlicher Bereich ist der **Home-Ordner**, meist `/home/<Dein Name>`, abgekürzt `~`. Ein **Pfad** beschreibt den Weg zu einer Datei, etwa `/home/anna/uebung_terminal/notiz.txt`.

Zwei Kürzel brauchst Du ständig:

- `.` bedeutet „dieser Ordner"
- `..` bedeutet „der Ordner darüber"

**Die Werkzeugkiste**

| Befehl | Beispiel | Was er tut |
|---|---|---|
| `pwd` | `pwd` | Zeigt, in welchem Ordner Du bist (**p**rint **w**orking **d**irectory) |
| `ls` | `ls` | Listet den Ordnerinhalt auf (**l**i**s**t); mit `-a` auch versteckte Dateien |
| `cd` | `cd ordner` | Wechselt den Ordner (**c**hange **d**irectory) |
| `mkdir` | `mkdir ordner` | Legt einen Ordner an (**m**a**k**e **dir**ectory) |
| `touch` | `touch datei.txt` | Legt eine leere Datei an, oder aktualisiert nur den Zeitstempel, falls sie schon existiert |
| `cat` | `cat datei.txt` | Gibt den Dateiinhalt im Terminal aus |
| `nano` | `nano datei.txt` | Einfacher Texteditor im Terminal |
| `rm` | `rm datei.txt` | Löscht eine Datei, Ordner mit `-r` (**r**e**m**ove) |
| `grep` | `grep Hallo datei.txt` | Zeigt nur Zeilen, die „Hallo" enthalten |
| `sudo` | `sudo apt update` | Führt einen Befehl mit Administratorrechten aus |
| `apt` | `sudo apt install git` | Installiert Software, hier das Programm `git` |
| `source` | `source datei.bash` | Liest eine Skriptdatei in der laufenden Shell ein |
| `alias` | `alias hallo="echo hallo"` | Vergibt einen Kurznamen für einen Befehl |

`source` taucht in fast jeder ROS-2-Anleitung auf. Deshalb steht es schon hier.

**Aufwärmen: Ordner, Dateien, Editor**

Du legst einen Übungsordner an und erstellst darin Dateien. Aufgeräumt wird erst am Ende, damit der Ordner für die späteren Beispiele bleibt.

Der Name `uebung_terminal` ist bewusst speziell. Prüfe zuerst, ob es ihn schon gibt:

```bash
ls -d ~/uebung_terminal
# "ls: cannot access ...: No such file or directory" (Fehlermeldung): Ordner gibt es noch nicht, alles gut.
# "/home/<Dein Name>/uebung_terminal": Ordner existiert schon.
```

**Existiert der Ordner schon, nimm ihn nicht einfach weiter.** Wähle einen anderen Namen, etwa `uebung_terminal2`, und ersetze ihn in allen folgenden Befehlen. Du weißt sonst nicht, was darin liegt, und beim Aufräumen würdest Du es mitlöschen.

Dann geht es los:

```bash
cd ~
mkdir uebung_terminal
cd uebung_terminal
touch leere_datei.txt
pwd
# /home/<Dein Name>/uebung_terminal
```

Meldet `mkdir` „File exists", war der Ordner doch schon da. Dann Namen ändern, siehe oben. Mit `pwd` siehst Du, wo Du gelandet bist. Wenn Du Dich verirrst: `cd ~` bringt Dich immer nach Hause.

Jetzt eine Datei mit Inhalt. Vorsicht: Gibt es `datei_mit_inhalt.txt` schon, öffnet nano die vorhandene Datei. Im frisch angelegten Ordner passiert das nicht.

```bash
nano datei_mit_inhalt.txt
```

Es öffnet sich ein Editor. Tippe `Hallo` und verlasse nano so:

1. `Strg+X`
2. `J` (bei deutscher Spracheinstellung, bei englischer `Y`), um das Speichern zu bestätigen
3. `Enter`

Falls Du unsicher bist: Die Zeile am unteren Fensterrand zeigt die Optionen. `^X` heißt `Strg+X`.

Kontrolle:

```bash
ls
# datei_mit_inhalt.txt  leere_datei.txt

cat datei_mit_inhalt.txt
# Hallo
```

**Tab-Vervollständigung: Tipp weniger**

Lange Pfade komplett von Hand tippen? Lass es. Das Terminal ergänzt Namen selbst, wenn Du `Tab` drückst.

```bash
cd ~
cd uebung_t      # jetzt Tab drücken
# wird zu:
cd uebung_terminal/
```

Gibt es mehrere Möglichkeiten, zeigt zweimal `Tab` alle an. In ROS 2 ist das Gold wert, weil Paket- und Befehlsnamen dort ziemlich lang werden.

Zwei weitere Tricks:

- Die **Pfeiltaste nach oben** holt den letzten Befehl zurück.
- `Strg+R` plus Suchbegriff durchsucht alle früheren Befehle.

**Umleitungen: `>` und `>>`**

Normalerweise landet die Ausgabe eines Befehls im Terminal. Mit `>` schickst Du sie stattdessen in eine Datei. Bevor Du für so eine Kleinigkeit ein 300-Zeilen-Python-Skript schreibst: Die Bash kann das meistens schon.

```bash
cd ~/uebung_terminal
ls > ergebnis.txt
cat ergebnis.txt
```

```
  ls ──────   [Terminal]         (ohne Umleitung)

  ls ──> ergebnis.txt            (mit  >  : Datei wird ÜBERSCHRIEBEN)
  ls ─>> ergebnis.txt            (mit >>  : Ausgabe wird ANGEHÄNGT)
```

Existiert die Datei noch nicht, legt Bash sie an.

> **Warnung:** `>` überschreibt die Zieldatei kommentarlos, `>>` hängt an. Wer die beiden verwechselt, verliert Inhalt ohne Rückfrage. Das gilt später auch für Konfigurationsdateien.

Mehr dazu im [Bash Reference Manual](https://www.gnu.org/software/bash/manual/html_node/Redirections.html).

**Befehle verketten mit `|`**

Der senkrechte Strich `|` (Pipe, reicht die Ausgabe eines Befehls an den nächsten weiter) macht aus kleinen Werkzeugen eine Kette:

```bash
ls | grep txt
```

Das listet den Ordnerinhalt und zeigt nur Zeilen mit „txt". In ROS 2 fischst Du so das Wichtige aus langen Ausgaben.

**Hilfe holen, ohne zu googeln**

Fast jeder Befehl erklärt sich selbst:

```bash
ls --help       # Kurzübersicht
man ls          # ausführliches Handbuch, Beenden mit q
```

`man` steht für „manual". Im Handbuch blätterst Du mit den Pfeiltasten, `q` beendet es.

**Aufräumen**

Fertig mit den Übungen? Dann weg mit dem Ordner. Aber nicht blind. `ls` allein zeigt versteckte Dateien (Namen mit führendem Punkt) nicht, deshalb nimmst Du `-la`:

```bash
cd ~
ls -la uebung_terminal
# Erwartet: nur leere_datei.txt, datei_mit_inhalt.txt, ergebnis.txt
# (plus die Einträge . und ..)
```

Steht dort etwas anderes, hast Du entweder einen alten Ordner weiterverwendet oder selbst etwas hineingelegt. Kopiere, was Du behalten willst, vorher woanders hin. Erst wenn die Liste genau Deinen drei Übungsdateien entspricht:

```bash
rm -r uebung_terminal
```

> **Warnung:** `rm` kennt keinen Papierkorb. Was weg ist, ist weg. Lies Löschbefehle vor dem `Enter` noch einmal durch, besonders `rm -r` mit falschem Ordnernamen. Im Home-Ordner bist Du König, und ein Fehler trifft Deine eigenen Dateien.

Willst Du später weiter üben, legst Du den Ordner mit `mkdir uebung_terminal` neu an.

**`sudo`: der Generalschlüssel**

`sudo` (**s**uper**u**ser **do**, führt einen Befehl mit Administratorrechten aus) erlaubt Dir, im System praktisch alles zu ändern, auch alles kaputt zu machen. Das Passwort, das `sudo` verlangt, ist Dein Benutzerpasswort. Sternchen beim Tippen? Gibt es nicht, das ist normal.

**Faustregel:** `sudo` nur, wenn eine vertrauenswürdige Anleitung es ausdrücklich verlangt, etwa die offizielle ROS-2-Installationsanleitung. `chmod` und `chown` (ändern Dateirechte und Besitzer) lässt Du vorerst ebenfalls in Ruhe.

Legst Du Dateien mit `sudo` an, gehören sie dem Administrator (`root`) statt Dir. Später kannst Du sie womöglich nicht mehr ändern oder löschen. Mehr dazu im [Ubuntu-Community-Wiki zu Dateirechten](https://help.ubuntu.com/community/FilePermissions).

**Software installieren mit `apt`**

`apt` ist die Paketverwaltung von Ubuntu. Zwei Befehle begegnen Dir ständig:

```bash
sudo apt update          # aktualisiert die Liste verfügbarer Pakete
sudo apt install git     # installiert das Paket "git"
```

Erst `update`, dann `install`. Das ist der erste der beiden Befehle mit Systemzugriff aus der Einschränkung oben.

**ROS 2 Jazzy installieren: Hinweise zur offiziellen Anleitung**

Für Ubuntu 24.04 (Noble Numbat) ist **Jazzy Jalisco** die passende Distribution (ein veröffentlichter ROS-2-Release mit eigenem Namen). Laut [offizieller Jazzy-Installationsanleitung](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html) gibt es Deb-Pakete (Installationspakete für `apt`) für Ubuntu Noble. Die Schritte dort nacheinander abzuarbeiten ist der sichere Weg. Sie umfassen unter anderem Locale-Einstellung, die ROS-Paketquelle und die Installation von `ros-jazzy-desktop`.

Ein Detail aus der Anleitung betrifft gerade Ubuntu-24.04-Nutzer: Enthält Deine `/etc/apt/sources.list.d/ubuntu.sources` in der Zeile `Suites:` nur `noble`, können bei `ros-dev-tools` Abhängigkeitskonflikte auftreten. Die Anleitung beschreibt, wie Du das prüfst und behebst. Lies den Abschnitt, bevor Du Dateien in `/etc` anfasst.

Warum nicht Lyrical Luth, die neueste LTS? Laut Vulcanexus-Tabelle ist Lyrical auf Ubuntu 26.04 Tier 1 (höchste Test- und Support-Stufe), auf Ubuntu 24.04 aber nur Tier 3. Dort müsstest Du es selbst aus dem Quellcode bauen. Du bleibst bei Jazzy, bis Du auf Ubuntu 26.04 wechselst.

Zum Stand der Distributionen (aus der ROS-Dokumentation und [endoflife.date](https://endoflife.date/ros-2)):

- **Jazzy Jalisco** (LTS): unterstützt bis Mai 2029.
- **Humble Hawksbill** (LTS): bis Mai 2027, läuft auf dem älteren Ubuntu 22.04.
- **Kilted Kaiju** (nicht LTS): endet Ende 2026. Die ROS-Startseite nennt den 30.11.2026, die Release-Tabelle Dezember 2026. Für Einsteiger deshalb ungeeignet.
- **Lyrical Luth** (LTS): bis Mai 2031, aber nur auf Ubuntu 26.04 Tier 1.

**`source` und `~/.bashrc`: der ROS-2-Pflichtbefehl**

Hier stolpern die meisten Einsteiger. Nach der Installation kennt Dein Terminal die ROS-2-Befehle noch nicht. Du musst ihm erst die Einrichtungsdatei bekannt machen. Für Jazzy lautet der Befehl, so steht er auch in der offiziellen Anleitung:

```bash
source /opt/ros/jazzy/setup.bash
```

`source` gilt nur für das aktuelle Fenster. Neues Terminal, neuer `source`. Das nervt schnell. Die Lösung heißt `~/.bashrc`: Eine Konfigurationsdatei in Deinem Home-Ordner, die Bash beim Start einer interaktiven Sitzung einliest, sofern sie nicht als Login-Shell startet. Die Terminal-Fenster von Ubuntu verhalten sich üblicherweise so. Hängst Du den `source`-Befehl dort an, ist er in jedem neuen Terminal automatisch aktiv.

Mach vorher eine Sicherung. Beachte: Existiert `~/.bashrc.sicherung` schon, überschreibt `cp` sie ohne Rückfrage. Prüfe das zuerst:

```bash
ls ~/.bashrc.sicherung
# Fehlermeldung "No such file or directory": gut, dann geht's weiter
cp ~/.bashrc ~/.bashrc.sicherung
```

Dann der eigentliche Befehl:

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
```

Das ist der zweite Befehl, der über den Übungsordner hinausgeht. Achte auf das `>>`: Es **hängt an**. Mit einem einfachen `>` überschreibst Du Deine komplette `.bashrc`. Führ den Befehl nur einmal aus, sonst steht die Zeile doppelt drin.

Auch ein `alias` gilt nur im aktuellen Fenster, außer Du schreibst ihn ebenfalls in die `.bashrc`.

**Nautilus: Dateien per Maus, aber vom Terminal aus**

`nautilus` ist der Standard-Dateimanager von Ubuntu, vergleichbar mit dem Explorer unter Windows oder dem Finder unter macOS. Aus dem Terminal gestartet, öffnet er genau den Ordner, in dem Du steckst:

```bash
cd ~
nautilus .
```

Der Punkt steht für den aktuellen Ordner. Praktisch, wenn Du das Ergebnis Deiner Terminal-Arbeit kurz grafisch ansehen willst. Die verlinkte [Manpage](https://manpages.ubuntu.com/manpages/jammy/en/man1/nautilus.1.html) stammt von Ubuntu 22.04 („Jammy") und passt nicht exakt zu Deinem 24.04. Die Grundfunktion `nautilus .` ist davon unberührt.

**Typische Anfängerfehler**

- **„command not found"**: Meist ein Tippfehler. Prüfe die Schreibweise und nutze `Tab`. Bei ROS-Befehlen fehlt oft das `source`.
- **„No such file or directory"**: Falscher Ordner oder falscher Pfad. `pwd` und `ls` zeigen Dir, wo Du bist.
- **„File exists"** bei `mkdir`: Den Ordner gibt es schon. Anderen Namen wählen.
- **„Permission denied"**: Keine Rechte für diese Datei. Das ist kein Grund, reflexartig `sudo` davorzusetzen. Prüfe erst, ob Du im richtigen Ordner bist.
- **Das Terminal „hängt"**: Meist läuft noch ein Programm. `Strg+C` beendet es.

**Fazit**

Mit `cd`, `ls`, `pwd`, `mkdir`, `cat`, `nano`, `source` und `Tab` kommst Du für die ersten ROS-2-Tutorials weit. `rm` und `sudo` behandelst Du wie eine Kettensäge: nützlich, aber nie nebenbei. Probier die Beispiele einmal komplett durch, das dauert etwa zehn Minuten.

---

## Glossar

- **alias**: Kurzname für einen längeren Befehl.
- **apt**: Paketverwaltung von Ubuntu zum Installieren von Software.
- **Bash**: Standard-Shell unter Ubuntu, führt Deine Eingaben aus.
- **.bashrc**: Konfigurationsdatei im Home-Ordner, die Bash bei jeder neuen interaktiven Sitzung einliest.
- **chmod / chown**: Ändern Dateirechte bzw. Dateibesitzer.
- **Deb-Paket**: Installationspaket für Ubuntu und Debian.
- **Distribution** (bei ROS 2): Ein veröffentlichter ROS-2-Release mit eigenem Namen, etwa Humble, Jazzy, Kilted oder Lyrical Luth.
- **grep**: Filtert Textzeilen nach einem Suchbegriff.
- **Home-Ordner**: Dein persönliches Verzeichnis, meist `/home/<Name>`, abgekürzt `~`.
- **LTS** (Long Term Support): Version mit besonders langer Unterstützung.
- **nano**: Einfacher Terminal-Texteditor.
- **Nautilus**: Dateimanager von Ubuntu.
- **Pfad**: Beschreibung, wo eine Datei im Dateisystem liegt.
- **Pipe** (`|`): Reicht die Ausgabe eines Befehls an den nächsten weiter.
- **Prompt**: Die Zeile vor Deiner Eingabe im Terminal.
- **Redirection** (Umleitung): Lenkt die Ausgabe eines Befehls in eine Datei um (`>`, `>>`).
- **ROS 2** (Robot Operating System 2): Software-Baukasten für Roboteranwendungen.
- **Shell**: Programm, das Deine Texteingaben versteht und ausführt.
- **source**: Liest eine Skriptdatei in der laufenden Shell ein.
- **sudo**: Führt einen Befehl mit Administratorrechten aus.
- **Terminal**: Fenster zur Eingabe von Textbefehlen an das System.
- **Tier 1 / Tier 3** (bei ROS 2): Support-Stufen einer Plattform; Tier 1 ist am besten getestet, Tier 3 nur per Selbstbau aus dem Quellcode nutzbar.

**Tags:** ubuntu, terminal, bash, shell, ros2, jazzy, nano, sudo, apt, source, bashrc, grep, redirection, tab-completion, linux-basics
