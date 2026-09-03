# kayatext

[한국어](README.md) ·
**Deutsch** ·
[English](README.en.md) ·
[Français](README.fr.md) ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

**Holt aus HWP- (koreanisches Textverarbeitungsformat), Excel- und Word-Dokumenten
Text heraus, den eine KI lesen kann.**

[Herunterladen](../../releases/latest) · [Fehler melden](../../issues) ·
[Weitere Werkzeuge](https://github.com/kjh0523/kayautils)

Dies ist ein Auslieferungs-Repository. Es enthält keinen Quellcode, nur ausführbare
Dateien ([warum](#warum-es-keinen-quellcode-gibt)).

---

## Warum es das gibt

Bevor ein Dokument an eine KI gehen kann, muss daraus Text werden. **Bei HWP geht das
nicht.** Für docx und pdf gibt es reichlich Extraktoren, für koreanische Dokumente
kaum welche — und **fast jedes koreanische Behördendokument ist HWP**.

Bloße Zeichen herauszuziehen ist erst die halbe Miete. **Über die Trefferqualität
entscheidet, ob die Struktur überlebt**: Eine zu Fließtext plattgedrückte Tabelle wird
schlecht gefunden, und ohne Überschriften gibt es keine Grenze, an der man das Dokument
zerteilen könnte.

Deshalb ist **Markdown** die Standardausgabe. Tabellen kommen als `|`-Tabellen heraus,
Arbeitsblätter als `##`-Überschriften. Markdown wird ohnehin schon so, wie es ist, in
Chunks zerlegt — es passt also direkt in bestehende Pipelines.

```
ein Behördendokument mit 116 Seiten → 193.000 Zeichen Markdown · 91 Tabellen
```

Alle 91 Tabellen überleben. Als reinen Text extrahiert, wird jede einzelne zu Fließtext.

---

## Benutzung

Im entpackten Ordner (macOS · Linux)

```bash
./kayatext Protokoll.hwp               # schreibt Protokoll.md
./kayatext *.hwp *.xlsx -o out/        # viele Dateien in einen Ordner
./kayatext Dokument.hwp --txt          # reiner Text (verwirft die Struktur)
./kayatext Dokument.hwp -              # nach stdout (für Pipes)
./kayatext --version
./kayatext --licenses                  # Open-Source-Hinweise
./kayatext --terms                     # Nutzungsbedingungen (mit Kontaktadresse)
```

In der Windows-PowerShell braucht es `.\` und die Dateiendung.

```powershell
.\kayatext.exe Protokoll.hwp
.\kayatext.exe *.hwp *.xlsx -o out\
.\kayatext.exe Dokument.hwp --txt
.\kayatext.exe --version
```

Liegt es im `PATH`, entfällt das führende `./` — siehe
[Von überall aufrufen](#von-überall-aufrufen).

**Eine fehlgeschlagene Datei hält den Lauf nicht an.** Die Fehler werden gesammelt und
am Ende gemeldet; ist auch nur eine fehlgeschlagen, lautet der Exit-Code 1 — ein Skript
muss das entscheiden können.

### Sprache der Meldungen

Hilfe und Fehlermeldungen **richten sich nach Ihrer Locale**. Es gibt zwei: Koreanisch
und Englisch.

```bash
LANG=ko_KR.UTF-8 kayatext Dokument.hwp     # Koreanisch
LANG=C           kayatext Dokument.hwp     # Englisch
KAYATEXT_LANG=en kayatext Dokument.hwp     # Englisch, unabhängig von der Locale
```

Beginnt `LANG` mit `ko`, wird Koreanisch gewählt, sonst Englisch. Unter Windows, wo
`LANG` meist fehlt, gilt stattdessen die Anzeigesprache des Systems.

> Dass es nicht sieben Sprachen sind, hat einen Grund. Die Lingua franca des Terminals
> ist Englisch, und bei sieben Fassungen jeder Fehlermeldung könnte ich die Zeile nicht
> mehr lesen, die Sie in ein Issue einfügen.

### Angenommene Formate

`.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf`

RTF wird nur bis zur Absatzebene gelesen — enthält das Original Tabellen, kommen sie als
Fließtext heraus. Das haben nicht wir weggeworfen; der Parser gibt es nicht her, und das
Ergebnis sagt es dazu.

`.pdf` `.pptx` `.doc` gehen noch nicht.

---

## Herunterladen

| | |
|---|---|
| **macOS (Apple Silicon)** | ✅ [`kayatext-macos-arm64.tar.gz`](../../releases/latest) |
| **Windows (x64)** | ✅ [`kayatext-windows-x64.zip`](../../releases/latest) |
| **Linux (x64)** | ✅ [`kayatext-linux-x64.tar.gz`](../../releases/latest) |
| macOS (Intel) | später |
| Linux (ARM64) | später |

Der Linux-Build ist **statisch gelinkt (musl)** und läuft daher auf jeder
Distribution — Ubuntu, Debian, Alpine, CentOS, direkt aus dem Archiv heraus. Auch im
Container braucht es keine zusätzlichen Bibliotheken.

Im Archiv liegen vier Dinge.

```
kayatext        die ausführbare Datei
rhwp            der HWP-Konverter — ohne ihn geht .hwp nicht
LICENSE.txt
THIRD-PARTY-NOTICES.md
```

**Keine Installation nötig.** Verschieben Sie den ganzen Ordner, wohin Sie wollen —
wichtig ist nur, dass `rhwp` daneben liegt.

**macOS · Linux**

```bash
# macOS
tar xzf kayatext-macos-arm64.tar.gz && cd kayatext-macos-arm64

# Linux
tar xzf kayatext-linux-x64.tar.gz && cd kayatext-linux-x64

./kayatext --version
```

**Windows** — die PowerShell durchsucht den aktuellen Ordner nicht, daher das `.\`. In
der `cmd.exe` genügt `kayatext`.

```powershell
cd kayatext-windows-x64
.\kayatext.exe --version
```

### Von überall aufrufen

Im `PATH` können Sie das `./` weglassen. **Legen Sie beide Dateien zusammen** — ohne
`rhwp` daneben funktioniert `.hwp` nicht.

```bash
sudo cp kayatext rhwp /usr/local/bin/          # macOS · Linux
```

```powershell
# Windows — einfacher ist es, den ganzen Ordner zu verschieben und ihn in den PATH zu legen
move kayatext-windows-x64 C:\tools\kayatext
setx PATH "%PATH%;C:\tools\kayatext"
```

> **Es ist nicht signiert, das Betriebssystem hält Sie also einmal auf.** Dies ist ein
> privates Projekt, ein Entwicklerzertifikat hängt noch nicht daran. **Die Datei ist
> nicht beschädigt.**
>
> - **macOS** — Systemeinstellungen → Datenschutz & Sicherheit → „Dennoch öffnen“
> - **Windows** — im blauen SmartScreen-Fenster „Weitere Informationen“ → „Trotzdem
>   ausführen“

---

## Lizenz

**Kein Open Source.** Der volle Text steht in der `LICENSE.txt` im Archiv.

**Für alle kostenlos** — für Privatleute ebenso wie für Unternehmen, Behörden, Schulen
und gemeinnützige Organisationen, kommerziell wie nicht kommerziell.

**Keine Funktionsbeschränkung.** Es gibt keine Bezahlversion; dies ist der einzige Build.
Kein Code darin zählt Ihre Nutzung oder sperrt eine Funktion.

### Die Unternehmenslizenz ist freiwillig

**Kaufen Sie sie oder nicht. Das Programm ändert sich dadurch nicht.**

Manche Organisationen müssen bei einer Beschaffungsprüfung oder einem Audit **schriftlich
belegen, dass sie eine Software benutzen dürfen**. „Es ist kostenlos, also nutzen wir es
einfach“ reicht dort nicht. Für solche Stellen gibt es **eine Lizenzurkunde,
Vertragsunterlagen und bevorzugten Support**. **Funktionen kommen keine hinzu.**

**Preis und Form der Nachweise werden im Gespräch festgelegt**, denn Nutzungsumfang und
benötigte Unterlagen sind überall anders. Schreiben Sie an **kayautils@gmail.com**.

**Weitergabe ist untersagt.** Dazu gehört auch, das Archiv irgendwo abzulegen, wo andere
es sich holen können — interne Freigabeordner, Dateiserver, Dokumentenablagen in
Groupware, Filehoster, Cloud-Laufwerke mit aktivierter Freigabe. Geben Sie statt der
Datei bitte die Adresse dieses Repositorys weiter. Wenn Sie es innerhalb Ihrer
Organisation wirklich verteilen müssen, schreiben Sie an **kayautils@gmail.com** — dann
lassen sich gesonderte Bedingungen vereinbaren.

**Für den extrahierten Text gibt es keinerlei Einschränkung.** Er gehört ganz Ihnen.

### Open-Source-Hinweise · Nutzungsbedingungen

```bash
./kayatext --licenses      # die mitgelieferten Open-Source-Komponenten
./kayatext --terms         # die Nutzungsbedingungen des Programms selbst
```

Die vollen Texte liegen auch als `THIRD-PARTY-NOTICES.md` und `LICENSE.txt` im Archiv.

Die HWP-Unterstützung steht auf [rhwp](https://github.com/edwardkim/rhwp) (MIT). Ohne
dieses Projekt gäbe es dieses Werkzeug nicht.

---

## Warum es keinen Quellcode gibt

Das hier baut eine einzelne Person, und ich habe mich dagegen entschieden, die Weitergabe
zuzulassen. Wenn es genau eine Bezugsquelle gibt, weiß ich, welche Version im Umlauf ist,
und eine Korrektur erreicht tatsächlich die Leute, die auf den Fehler gestoßen sind.

Meine Beiträge zu anderen Open-Source-Projekten und die Gründe dafür veröffentliche ich.

---

## Sagen Sie mir, welche Dokumente scheitern

Manche Fehler tauchen in eigens angefertigten Testdateien nie auf. **Es gibt sie nur in
echten Dokumenten** — eine Verbindung über 11 Spalten, eine Tabelle mit eingestreuten
Zeichenobjekten, ein Formular aus einer alten Version der Textverarbeitung.

### Was am meisten hilft

**Öffentlich veröffentlichte Dokumente von Behörden und öffentlichen Stellen.**
Bekanntmachungen, Formulare, Pressemitteilungen — Sie müssen keine Datei schicken,
**die Adresse zum Herunterladen genügt**.

### Bei Firmendokumenten — bitte nicht hochladen

In Geschäftsdokumenten stehen Namen und Kontaktdaten, und **der Verfasser bleibt auch in
den Dateieigenschaften erhalten**. Einmal öffentlich hochgeladen, lässt sich das nicht
zurücknehmen, und es kann Ihnen Ärger einbringen.

**Beschreiben Sie stattdessen nur das Symptom.** So etwas wie „ein dreiseitiges
Behördenschreiben mit Tabellen, ab Seite 2 verschwinden die Linien“ reicht mir, um eine
ähnliche Datei zu bauen und es nachzustellen. Wird die Datei wirklich gebraucht, tauschen
wir sie nicht öffentlich aus, und ich erkläre Ihnen, wie man sie anonymisiert.

### Ein Dankeschön

Wenn das, was Sie schicken, tatsächlich einen Fehler fängt oder in die Regressionstests
eingeht —

- kommt Ihr Name auf die Liste der Regressionstests (wenn Sie möchten)
- **gibt es eine Unternehmenslizenz-Urkunde kostenlos.** Zum Benutzen brauchen Sie sie
  nicht — es ist auch in der Firma ohnehin kostenlos. Wert hat sie nur für Leute, die bei
  einer Beschaffungsprüfung oder einem Audit **Papiere vorlegen müssen**

Bitte strengen Sie sich nicht an, Dateien zu schicken, nur um eine Urkunde zu bekommen.
**Eine Adresse ist mehr wert als zehn Dateien.**

---

Copyright © 2026 가야태자 (kjh0523). All rights reserved.
