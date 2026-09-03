# AI エージェント用スキル

[한국어](README.md) ·
[Deutsch](README.de.md) ·
[English](README.en.md) ·
[Français](README.fr.md) ·
**日本語** ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

Claude のようなツールに **韓国語文書を読ませようとすると、自分でパーサーを書き始めます。**
`.hwp` はバイナリ形式なので、たいていは文字化けか、表の消えた結果が返ってきます。

このスキルは、そういうときに **代わりに `kayatext` を呼ぶよう**教えます。

---

## まず kayatext が必要です

スキルは指示文にすぎず、実際の変換は `kayatext` が行います。

[ダウンロード](../../../releases/latest) → 展開して `PATH` に置いてください。
`.hwp` を扱うには `rhwp` も**同じフォルダに**必要です。

```bash
sudo cp kayatext rhwp /usr/local/bin/
kayatext --version
```

---

## インストール

### Claude Code

```bash
mkdir -p ~/.claude/skills/korean-documents
curl -sL -o ~/.claude/skills/korean-documents/SKILL.md \
  https://raw.githubusercontent.com/kjh0523/kayatext/main/skill/SKILL.md
```

特定のプロジェクトだけで使うなら、`~/.claude` ではなくそのプロジェクトの `.claude`
配下に置きます。

### claude.ai

設定 → Skills で `SKILL.md` をアップロードします。

---

## 確認

新しいセッションで韓国語文書を読むよう頼んでみてください。

```
このファイルを要約して: 議事録.hwp
```

**スキルが効いていれば** `kayatext 議事録.hwp -` を呼びます。
**効いていなければ** Python の `olefile` で開こうとします — その場合はスキルが入って
いません。

---

## 何をさせるか

- `.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf` `.pdf` は**パーサーを書かず**に
  `kayatext` を呼ぶ
- **Markdown を既定にする。** プレーンテキストで取り出すと表が地の文になる —
  116 ページの公文書 1 件に表が 91 個あった
- `kayatext` がなければ**利用者に伝える。** 代わりにパーサーを書いて壊れた結果を出さ
  ない — それは助けではなく誤答である
- 変換で欠けたもの（図形・グラフなど）があれば**併せて報告する。** 文書にあった表が
  結果にないなら、それを根拠に答えてはならない

最後の 2 つが肝心です。**知らないことを知っているふりをさせない**こと、それがこの
スキルが防ごうとしている本当の失敗です。

---

## まだできないこと

`.pptx` `.doc`（旧形式）は `kayatext` が読めません。スキルもそう伝えます。

**PDF は読めます** — 罫線のある表と見出しは残り、罫線のない表は地の文になります。

MCP サーバーは準備中です。インストールなしで `npx` から使えるようにするのが目標です。
