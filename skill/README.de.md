# Ein Skill für KI-Agenten

[한국어](README.md) ·
**Deutsch** ·
[English](README.en.md) ·
[Français](README.fr.md) ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

Bitten Sie ein Werkzeug wie Claude, ein koreanisches Dokument zu lesen, **dann schreibt
es sich selbst einen Parser**. `.hwp` ist ein Binärformat — heraus kommt meist
Zeichensalat oder ein Dokument, dessen Tabellen verschwunden sind.

Dieser Skill sagt ihm, dass es stattdessen **`kayatext` aufrufen** soll.

---

## Zuerst brauchen Sie kayatext

Der Skill ist nur eine Anweisung; die eigentliche Umwandlung macht `kayatext`.

[Herunterladen](../../../releases/latest) → entpacken und in den `PATH` legen.
Damit `.hwp` funktioniert, muss `rhwp` **im selben Ordner** liegen.

```bash
sudo cp kayatext rhwp /usr/local/bin/
kayatext --version
```

---

## Installation

### Claude Code

```bash
mkdir -p ~/.claude/skills/korean-documents
curl -sL -o ~/.claude/skills/korean-documents/SKILL.md \
  https://raw.githubusercontent.com/kjh0523/kayatext/main/skill/SKILL.md
```

Für ein einzelnes Projekt legen Sie ihn statt unter `~/.claude` unter das `.claude` des
Projekts.

### claude.ai

Einstellungen → Skills, dort `SKILL.md` hochladen.

---

## Prüfen, ob es greift

Bitten Sie in einer neuen Sitzung darum, ein koreanisches Dokument zu lesen.

```
Fasse mir diese Datei zusammen: Protokoll.hwp
```

**Greift der Skill**, ruft er `kayatext Protokoll.hwp -` auf.
**Greift er nicht**, versucht das Werkzeug, die Datei in Python mit `olefile` zu öffnen —
dann ist der Skill nicht installiert.

---

## Was er dem Agenten aufträgt

- bei `.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf` `.pdf` **keinen Parser schreiben**,
  sondern `kayatext` aufrufen
- **standardmäßig Markdown.** Als reiner Text extrahiert werden aus den Tabellen
  Fließtext — ein Behördendokument mit 116 Seiten hatte 91 davon
- ist `kayatext` nicht da, **den Benutzer darauf hinweisen.** Nicht stattdessen einen
  Parser schreiben und ein kaputtes Ergebnis liefern — das ist keine Hilfe, sondern eine
  falsche Antwort
- fehlt in der Umwandlung etwas (Zeichenobjekte, Diagramme …), **auch das melden.**
  Fehlt eine Tabelle im Ergebnis, die im Dokument stand, darf er nicht so antworten, als
  hätte er sie gelesen

Die letzten beiden sind die wichtigen. **Nicht so zu tun, als wüsste er, was er nicht
weiß** — das ist das eigentliche Versagen, das dieser Skill verhindern soll.

---

## Was noch nicht geht

`.pptx` und `.doc` (das alte Format) kann `kayatext` nicht lesen, und der Skill
sagt das auch.

**PDF wird gelesen** — Tabellen mit Linien und Überschriften bleiben erhalten,
Tabellen ohne Linien werden zu Fließtext.

Ein MCP-Server ist in Arbeit. Ziel ist die Nutzung über `npx`, ganz ohne Installation.
