# kayatext

[한국어](README.md) ·
[Deutsch](README.de.md) ·
**English** ·
[Français](README.fr.md) ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

**Extracts AI-readable text from HWP (Korean word processor), Excel and Word documents.**

[Download](../../releases/latest) · [Report a bug](../../issues) ·
[Other tools](https://github.com/kjh0523/kayautils)

This is a distribution repository. It holds no source, only binaries
([why](#why-there-is-no-source)).

---

## Why this exists

Before you can feed a document to an AI, you have to turn it into text. **With HWP,
you can't.** There are plenty of extractors for docx and pdf; Korean documents have
almost none — and **nearly every public-sector document in Korea is HWP**.

Pulling out bare characters only gets you halfway. **Retrieval quality depends on
whether the structure survives**: a table flattened into prose retrieves badly, and
without headings there is no boundary to chunk on.

So the default output is **Markdown**. Tables come out as `|` tables, sheets as `##`
headings. The ecosystem already chunks Markdown as-is, so it drops straight into
existing pipelines.

```
one 116-page government document → 193,000 characters of Markdown · 91 tables
```

All 91 tables survive. Extract plain text instead and every one of them becomes prose.

---

## Usage

From the unpacked folder (macOS · Linux)

```bash
./kayatext minutes.hwp                 # writes minutes.md
./kayatext *.hwp *.xlsx -o out/        # many files into one folder
./kayatext doc.hwp --txt               # plain text (discards structure)
./kayatext doc.hwp -                   # to stdout (for pipes)
./kayatext --version
./kayatext --licenses                  # open-source notices
./kayatext --terms                     # usage terms (includes the licensing contact)
```

In Windows PowerShell it's `.\` and the extension is required.

```powershell
.\kayatext.exe minutes.hwp
.\kayatext.exe *.hwp *.xlsx -o out\
.\kayatext.exe doc.hwp --txt
.\kayatext.exe --version
```

Put it on your `PATH` and the leading `./` goes away — see
[Calling it from anywhere](#calling-it-from-anywhere).

**One failing file does not stop the run.** Failures are collected and reported at the
end, and the exit code is 1 if anything failed — a script has to be able to tell.

### Message language

Help and error messages **follow your locale**. There are two: Korean and English.

```bash
LANG=ko_KR.UTF-8 kayatext doc.hwp     # Korean
LANG=C           kayatext doc.hwp     # English
KAYATEXT_LANG=en kayatext doc.hwp     # English regardless of locale
```

A `LANG` starting with `ko` gives Korean; anything else gives English. On Windows,
where `LANG` is usually unset, the system display language is used instead.

> There is a reason this is not seven languages. English is the lingua franca of the
> terminal, and with seven versions of every error I could not read the line you paste
> into an issue.

### Formats accepted

`.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf` `.pdf`

RTF is read down to paragraphs only — if the original has tables, they come out as
prose. That isn't something we threw away; the parser doesn't hand it over, and the
result says so.

For PDF the structure is rebuilt from coordinates — **ruled tables and headings
survive; tables drawn without rules come out as prose.** If you have the original
(Hangul, Excel, Word), that path keeps every table.

`.pptx` and `.doc` (the old format) are not supported yet.

---

## Download

| | |
|---|---|
| **macOS (Apple Silicon)** | ✅ [`kayatext-macos-arm64.tar.gz`](../../releases/latest) |
| **Windows (x64)** | ✅ [`kayatext-windows-x64.zip`](../../releases/latest) |
| **Linux (x64)** | ✅ [`kayatext-linux-x64.tar.gz`](../../releases/latest) |
| macOS (Intel) | later |
| Linux (ARM64) | later |

The Linux build is **statically linked (musl)**, so it does not care which
distribution you run — Ubuntu, Debian, Alpine, CentOS all work straight out of the
archive. Dropping it into a container needs no extra libraries either.

The archive holds four things.

```
kayatext        the executable
rhwp            the HWP converter — .hwp needs this
LICENSE.txt
THIRD-PARTY-NOTICES.md
```

**No installation.** Move the whole folder wherever you like; all that matters is that
`rhwp` sits next to it.

**macOS · Linux**

```bash
# macOS
tar xzf kayatext-macos-arm64.tar.gz && cd kayatext-macos-arm64

# Linux
tar xzf kayatext-linux-x64.tar.gz && cd kayatext-linux-x64

./kayatext --version
```

**Windows** — PowerShell does not search the current folder, hence the `.\`. In
`cmd.exe`, plain `kayatext` works.

```powershell
cd kayatext-windows-x64
.\kayatext.exe --version
```

### Calling it from anywhere

Put it on your `PATH` and you can drop the `./`. **Keep both files together** — without
`rhwp` beside it, `.hwp` won't work.

```bash
sudo cp kayatext rhwp /usr/local/bin/          # macOS · Linux
```

```powershell
# Windows — moving the whole folder and adding it to PATH is simpler
move kayatext-windows-x64 C:\tools\kayatext
setx PATH "%PATH%;C:\tools\kayatext"
```

> **It isn't code-signed, so your OS will stop you once.** This is a personal project
> and a developer certificate isn't attached yet. **The file is not corrupt.**
>
> - **macOS** — System Settings → Privacy & Security → "Open Anyway"
> - **Windows** — on the blue SmartScreen dialog, "More info" → "Run anyway"

---

## License

**Not open source.** The full text is in `LICENSE.txt` inside the archive.

**Free for everyone** — homes and companies alike, government offices, schools,
non-profits, commercial and non-commercial use.

**No functional limits.** There is no paid edition; this is the only build there is. No
code in it counts your usage or locks a feature.

### A commercial license is optional

**Buy it or don't. The program does not change.**

Some organisations must **prove on paper that they have the right to use a piece of
software** before a procurement review or an audit will pass. "It's free, so we just use
it" does not clear that bar. For those places there is a **licence certificate,
contract paperwork and priority support**. **It adds no features whatsoever.**

**The price and the form of the paperwork are settled by agreement**, because the scope
of use and the documents each organisation needs differ. Write to
**kayautils@gmail.com**.

**Redistribution is not permitted.** That includes putting the archive anywhere other
people can pick it up — an internal shared folder, a file server, a groupware document
library, a file-hosting service, a cloud drive with sharing turned on. When you tell
someone about it, send this repository's address instead of the file. If you have a
real need to distribute it inside your organisation, write to **kayautils@gmail.com**
and we can work out separate terms.

**There are no restrictions on the text you extract.** It is entirely yours.

### Open-source notices · usage terms

```bash
./kayatext --licenses      # the open-source components shipped with it
./kayatext --terms         # this program's own usage terms
```

The full texts are also in `THIRD-PARTY-NOTICES.md` and `LICENSE.txt` in the archive.

HWP support stands on [rhwp](https://github.com/edwardkim/rhwp) (MIT). Without that
project this tool would not exist.

---

## Why there is no source

This is a tool built by one person, and I chose to prevent redistribution. When there is
exactly one place to get it, I know which version is out in the world, and a fix I make
actually reaches the people who hit the bug.

Contributions I make to other open-source projects, and the reasoning behind them, are
published.

---

## Tell me about documents that fail

Some bugs never show up in files made for testing. **They only exist in real
documents** — an 11-column merge, a table with shapes mixed in, a form built in an old
version of Hangul.

### What helps most

**Publicly published government or public-sector documents.** Notices, forms, press
releases — you don't need to send the file, **just the address it can be downloaded
from**.

### If it's a work document — please don't upload it

Business documents carry names and contact details, and **the author's name survives in
the file metadata**. Once it's posted somewhere public there is no taking it back, and
it can land you in trouble.

**Describe the symptom instead.** Something like "a 3-page official letter with tables;
from page 2 the ruling lines disappear" is enough for me to build a similar file and
reproduce it. If the file really is needed, we exchange it privately and I'll walk you
through anonymising it.

### A thank-you

If what you send actually catches a bug or joins the regression suite —

- your name goes on the regression-suite list (if you want it to)
- **a commercial license certificate, free of charge.** You never needed it to use the
  tool — it's free at a company too. It is only worth something to people who **have to
  produce paperwork** for a procurement review or an audit

Please don't strain to send files just to get a certificate.
**One address beats ten files.**

---

Copyright © 2026 가야태자 (kjh0523). All rights reserved.
