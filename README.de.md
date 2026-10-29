# Digitaltechnik-Lab für den Unterricht: Verilog, VHDL und yosys in einer Ubuntu-VM

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml)

Ein fertiges **Lab für digitalen Schaltungsentwurf**, um Verilog und VHDL zu unterrichten, von der [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning), auf der Schulen und Hochschulen jeder Schülerin und jedem Schüler eigene Maschinen und ein Terminal im Browser geben. Jede und jeder erhält eine Ubuntu-VM mit **Icarus Verilog**, **GHDL** und **yosys**, installiert aus dem Archiv von Ubuntu. Die Lernenden simulieren Schaltungen mit einer Testbench, lesen eine Wahrheitstabelle und ein **Signalverlaufsdiagramm** als Text, vollenden einen Volladdierer in Verilog und in VHDL sowie einen 4-Bit-Zähler mit Enable und synchronem Reset und zählen die Gatter, die yosys daraus macht. Auf dem Computer der Lernenden wird nichts installiert, und ein Board ist nicht nötig.

## Was die Lernenden tun

Das Lab legt einen Ordner `~/hdl` mit den Arbeitsdateien an und im Home-Verzeichnis eine `README.md`, die sagt, was zu tun ist, und keine Lösung verrät. Es hat fünf Teile:

1. Eine kombinatorische Schaltung in Verilog: die Wahrheitstabelle eines vorgegebenen Halbaddierers lesen, dann `full_adder.v` schreiben.
2. Ein Takt und ein Signalverlauf: ein vorgegebener Zähler zeichnet seine Signale in einer VCD-Datei auf, die mit `vcd2txt` als Textzeilen gelesen oder mit `vcd2svg` als Bild gezeichnet und auf Port 8000 des Labs geöffnet wird.
3. Derselbe Volladdierer in VHDL mit den drei Schritten von GHDL, analysieren, elaborieren und ausführen: `full_adder.vhd`.
4. Ein Zähler mit Enable und synchronem Reset, den die Lernenden vollenden: `counter4.v`.
5. Gatter: `hdl gates counter4.v counter4` lässt yosys die Zellen zählen, die ein Entwurf braucht. Das zeigt, dass ein Flipflop ein Bit speichert und ein Latch ein Fehler ist.

`hdl reset DATEI` stellt eine Datei so wieder her, wie sie ausgegeben wurde. Eine Basics-Lektion der Plattform lässt sich in diesem Lab durcharbeiten und überlässt den Volladdierer und den Zähler den Lernenden.

## So wird bewertet

Das Lab hat eine **Prüfung**, ein Skript, das eine abgegebene VM bewertet, ohne dass Sie sie lesen müssen. Es läuft auf einer Kopie der gespeicherten Maschine nach einem frischen Start. Es verlangt `full_adder.v`, `full_adder.vhd` und `counter4.v` in `~/hdl` und weist eine Verilog-Datei ab, die eine Systemaufgabe aufruft, die die Simulation beendet oder verlässt (`$finish`, `$stop`, `$fatal`, `$fopen`, `$system` und weitere, auch in Kommentaren), ebenso eine VHDL-Datei, die `textio`, `std.env` oder `severity failure` verwendet. Dann übersetzt es jeden Entwurf mit **Testbenches, die die Lernenden nie sehen**. Jede enthält ein Referenzmodell und vergleicht es mit dem Entwurf, den Zähler vor und nach jeder Taktflanke, sodass ein Reset, der nicht synchron ist, auffällt. Eine Testbench gibt eine Ergebniszeile mit einer Zahl aus, die die Prüfung bei jedem Lauf neu auslost, sodass ein Entwurf die richtige Zeile nicht selbst ausgeben kann. Zuletzt muss yosys die Entwürfe **ohne Latch** lesen, und der Zähler braucht Flipflops und nicht mehr Zellen als ein einfacher Entwurf. Die erste ausgegebene Zeile sagt, was nicht stimmt.

Was sie nicht tut: Sie liest weder die Signalverläufe noch die Antworten auf die Fragen in der README, sie platziert und verdrahtet nichts für ein Board, und wer in der eigenen VM Root-Rechte hat, könnte die Simulatoren noch manipulieren. Es ist eine Prüfung für den Unterricht, kein Beweis.

## Was in der Datei steht

`hdl-lab.lab.yaml` ist eine vollständige **VM-Lab-Definition**, genau das, was die Plattform ausführt:

| Schlüssel | Was es ist |
| --- | --- |
| `cpus`, `memory`, `disk` | die Größe der VM jeder Schülerin und jedes Schülers: 1 CPU, 1024 MB und 10 GB |
| `open` | die Ports, die im Browser geöffnet werden: 8000, der die vom Lab gezeichneten Signalverlaufsbilder zeigt; alles andere läuft im Terminal |
| `setup` | ein Skript, das einmal als root läuft, wenn die VM jeder Person zum ersten Mal startet: es installiert `iverilog`, `ghdl` und `yosys` aus dem Archiv von Ubuntu (einige Minuten), schreibt die Hilfsbefehle `hdl`, `vcd2txt` und `vcd2svg`, die Arbeitsdateien, die README und eine systemd-Unit, die die Bilder ausliefert |
| `check` | das Bewertungsskript, als root auf einer Kopie der gespeicherten Platte nach einem frischen Start ausgeführt; sein Exit-Status ist das Ergebnis, und `0` bedeutet bestanden |

## Auf der Plattform verwenden

**So geben Sie es einer Klasse als Aufgabe:**

1. Fügen Sie in Ihrem Modul eine Aufgabe hinzu und wählen Sie die Karte **Ubuntu-VM-Lab**.
2. Drücken Sie auf das Beispiel Digitaltechnik oder fügen Sie den Inhalt von `hdl-lab.lab.yaml` ein. Ein Beispiel zu verwenden ersetzt den Text, und Rückgängig bringt Ihren zurück.
3. Veröffentlichen Sie und probieren Sie zuerst Ihre eigene Kopie aus. Jede Schülerin und jeder Schüler hat eine eigene VM, die zwischen den Sitzungen erhalten bleibt.
4. Teilen Sie es zu wie jede Aufgabe. Ist es abgegeben, starten Sie die Prüfung in der Zeile einer Person in der Klassenliste: Sie startet eine Kopie der gespeicherten Maschine, die nicht zurückgespeichert wird, und zeigt, ob sie bestanden hat und was ausgegeben wurde. Die Lernenden erfahren das Ergebnis nicht.

Die [Seite zu diesem Lab](https://mitsurutech.co.uk/de/help/lab-hdl-lab) im Handbuch, die [Anleitung zu Ubuntu-VM-Labs](https://mitsurutech.co.uk/de/help/labs) und die [Seite mit den Beispiel-Labs](https://mitsurutech.co.uk/de/help/sample-labs) erklären den Rest.

Noch kein Konto für Ihre Einrichtung? [Das erste Jahr ist kostenlos](https://mitsurutech.co.uk/de/pricing) für bis zu 50 Lernende, und die [Anmeldung](https://mitsurutech.co.uk/de/signup) braucht nur eine E-Mail-Adresse.

## Die Datei prüfen

Bei jedem Push prüft `.github/workflows/check.yml`, ob die Datei gültiges YAML mit den Schlüsseln `cpus`, `memory`, `disk`, `setup` und `check` ist und ob `setup` und `check` jeweils innerhalb der 16-KB-Grenze der Plattform liegen. Es startet keine VM: Das geschieht auf den Maschinen der Plattform. Das Setup, die Befehle der Basics-Lektion und die Prüfung wurden in einem Ubuntu-26.04-Container ausgeführt, der die VM vertrat. Ein Versuch in einer tatsächlich gestarteten Schüler-VM vor dem Unterricht lohnt sich daher.

## Was in einem Lab nicht funktioniert

Die VM erreicht das Paketarchiv von Ubuntu, PyPI, npm und GitHub und sonst nichts. Alles, was dieses Lab braucht, kommt aus dem Archiv von Ubuntu. Eine Terminal-VM hat keinen Desktop und deshalb keinen grafischen Signalverlaufs-Viewer: Signalverläufe werden als Text oder als Bild auf Port 8000 gelesen. Ubuntu 26.04 liefert GHDL mit dem GCC-Backend, daher schreibt `ghdl -e` eine ausführbare Datei neben die Dateien.

## Über uns

Von [Mitsuru Technologies](https://mitsurutech.co.uk/de) für die [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning): Python, Java, Go, Node, SQL und Linux im Browser für Schulen und Hochschulen, mit Container-Labs, VM-Labs, aus GitHub bereitgestellten Web-Apps und Quizzen, die sich selbst bewerten. Code ohne Konto ausprobieren im [Playground](https://mitsurutech.co.uk/de/playground).

Weitere Labs von derselben Stelle: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) und [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Lizenz

Dieses Werk steht unter der Lizenz [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Sie dürfen es zum Lernen und Lehren teilen und bearbeiten, wenn Sie Mitsuru Technologies Ltd nennen, es nicht kommerziell nutzen und alles, was Sie daraus machen, unter derselben Lizenz weitergeben. Der vollständige Text steht in der Datei `LICENSE`.

[Icarus Verilog](https://steveicarus.github.io/iverilog/) (GPL-2.0 oder später), [GHDL](https://github.com/ghdl/ghdl) (GPL-2.0 oder später) und [yosys](https://yosyshq.net/yosys/) (ISC) werden beim Start einer VM aus dem Archiv von Ubuntu installiert; nichts davon wird hier verteilt.
