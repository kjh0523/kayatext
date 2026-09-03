# A skill for AI agents

[한국어](README.md) ·
[Deutsch](README.de.md) ·
**English** ·
[Français](README.fr.md) ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

Ask a tool like Claude to read a Korean document and **it will write its own parser**.
`.hwp` is a binary format, so what usually comes back is mojibake, or a document with
its tables gone.

This skill tells it to **call `kayatext` instead**.

---

## You need kayatext first

The skill is only instructions; `kayatext` does the actual conversion.

[Download it](../../../releases/latest) → unpack it and put it on your `PATH`.
`rhwp` has to be **in the same folder** for `.hwp` to work.

```bash
sudo cp kayatext rhwp /usr/local/bin/
kayatext --version
```

---

## Installing

### Claude Code

```bash
mkdir -p ~/.claude/skills/korean-documents
curl -sL -o ~/.claude/skills/korean-documents/SKILL.md \
  https://raw.githubusercontent.com/kjh0523/kayatext/main/skill/SKILL.md
```

To use it in a single project, put it under that project's `.claude` instead of
`~/.claude`.

### claude.ai

Settings → Skills, then upload `SKILL.md`.

---

## Checking that it works

In a fresh session, ask it to read a Korean document.

```
Summarise this file for me: minutes.hwp
```

**If the skill fired**, it calls `kayatext minutes.hwp -`.
**If it didn't**, it will try to open the file with `olefile` in Python — which means the
skill isn't installed.

---

## What it tells the agent to do

- for `.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf` `.pdf`, **don't write a parser** —
  call `kayatext`
- **default to Markdown.** Extract plain text and the tables become prose — one
  116-page government document had 91 of them
- if `kayatext` isn't there, **say so to the user.** Don't write a parser instead and
  hand back a broken result — that isn't help, it's a wrong answer
- if something was left out of the conversion (shapes, charts …), **report it too.** If
  a table that was in the document is missing from the output, it must not answer as if
  it had read it

The last two matter most. **Not pretending to know what it doesn't** is the real failure
this skill is there to prevent.

---

## Not supported yet

`kayatext` cannot read `.pptx` or `.doc` (the old format), and the skill says so.

**PDF is read** — ruled tables and headings survive; tables without rules come out
as prose.

An MCP server is in progress. The goal is to make it usable through `npx`, with no
installation.
