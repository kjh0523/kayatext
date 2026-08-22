# 面向 AI 智能体的技能

[한국어](README.md) ·
[Deutsch](README.de.md) ·
[English](README.en.md) ·
[Français](README.fr.md) ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
**中文**

让 Claude 这类工具**去读韩文文档，它会自己动手写解析器。** `.hwp` 是二进制格式，这样
做出来的结果通常是乱码，或者表格全没了。

这个技能会告诉它：那种时候**改为调用 `kayatext`**。

---

## 先要有 kayatext

技能只是一份指示，真正做转换的是 `kayatext`。

[下载](../../../releases/latest) → 解压后放进 `PATH`。
要处理 `.hwp`，`rhwp` 必须**在同一个文件夹里**。

```bash
sudo cp kayatext rhwp /usr/local/bin/
kayatext --version
```

---

## 安装

### Claude Code

```bash
mkdir -p ~/.claude/skills/korean-documents
curl -sL -o ~/.claude/skills/korean-documents/SKILL.md \
  https://raw.githubusercontent.com/kjh0523/kayatext/main/skill/SKILL.md
```

若只想在某一个项目里用，就放到该项目的 `.claude` 下，而不是 `~/.claude`。

### claude.ai

设置 → Skills，上传 `SKILL.md`。

---

## 验证

在新会话里让它读一份韩文文档。

```
帮我总结这个文件：会议记录.hwp
```

**技能生效**时，它会调用 `kayatext 会议记录.hwp -`。
**没生效**时，它会试着用 Python 的 `olefile` 打开 —— 那就说明技能没装上。

---

## 让它做什么

- 遇到 `.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf`，**不要写解析器**，
  调用 `kayatext`
- **默认用 Markdown。** 按纯文本提取，表格会变成散文 —— 一份 116 页的公文里有 91 个表格
- 如果没有 `kayatext`，**要告诉用户。** 不要改为自己写解析器、交出残缺的结果 ——
  那不是帮忙，是给了错误答案
- 转换中若有遗漏（图形、图表等），**一并报告。** 文档里有而结果里没有的表格，不能拿它
  当依据作答

最后两条最要紧。**不让它不懂装懂** —— 这才是这个技能真正要防住的失败。

---

## 目前还不支持

`kayatext` 读不了 `.pdf` `.pptx` `.doc`，技能里也是这么说的。

MCP 服务器正在准备中，目标是不用安装、通过 `npx` 直接使用。
