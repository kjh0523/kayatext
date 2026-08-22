# kayatext

[한국어](README.md) ·
[Deutsch](README.de.md) ·
[English](README.en.md) ·
[Français](README.fr.md) ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
**中文**

**从 HWP（韩文文字处理格式）、Excel、Word 文档中提取 AI 可读的文本。**

[下载](../../releases/latest) · [报告缺陷](../../issues) ·
[其他工具](https://github.com/kjh0523/kayautils)

这里是发布仓库，不含源码，只放可执行文件（[原因](#为什么没有源码)）。

---

## 为什么做这个

要把文档交给 AI，先得把它变成文本。可是 **HWP 做不到。** docx 和 pdf 有大量现成的
提取工具，韩文文档几乎没有，而**韩国的公文绝大多数是 HWP**。

而且只取出字符只算做了一半。**检索质量取决于结构是否还在** —— 表格被压成散文就检索
不准，没有标题就找不到切分文档的边界。

所以默认输出是 **Markdown**。表格输出为 `|` 表格，工作表输出为 `##` 标题。业界本来
就直接对 Markdown 做分块，因此可以原样接入现有流水线。

```
一份 116 页的公文 → Markdown 193,000 字 · 表格 91 个
```

91 个表格全部保留。若按纯文本提取，它们会全部变成散文。

---

## 用法

在解压后的文件夹中（macOS · Linux）

```bash
./kayatext 会议记录.hwp                # 生成 会议记录.md
./kayatext *.hwp *.xlsx -o out/        # 多个文件输出到同一文件夹
./kayatext 文档.hwp --txt              # 纯文本（丢弃结构）
./kayatext 文档.hwp -                  # 输出到标准输出（供管道使用）
./kayatext --version
./kayatext --licenses                  # 开源声明
```

在 Windows PowerShell 中需要 `.\`，且必须带扩展名。

```powershell
.\kayatext.exe 会议记录.hwp
.\kayatext.exe *.hwp *.xlsx -o out\
.\kayatext.exe 文档.hwp --txt
.\kayatext.exe --version
```

放进 `PATH` 后就不必再写前面的 `./` —— 参见[在任何位置调用](#在任何位置调用)。

**单个文件失败不会中断整批。** 失败清单会汇总后在最后报告，只要有一个失败就返回退出码
1 —— 脚本必须能据此判断。

### 支持的格式

`.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf`

RTF 只读到段落层级 —— 原文若有表格，会以散文形式输出。这不是我们丢掉的，而是解析器
没有给出，结果中会如实说明。

`.pdf` `.pptx` `.doc` 尚不支持。

---

## 下载

| | |
|---|---|
| **macOS (Apple Silicon)** | ✅ [`kayatext-macos-arm64.tar.gz`](../../releases/latest) |
| **Windows (x64)** | ✅ [`kayatext-windows-x64.zip`](../../releases/latest) |
| Linux (x64, musl) | 准备中 |
| macOS (Intel) | 靠后 |

解压后有四样东西。

```
kayatext        可执行文件
rhwp            HWP 转换器 —— 有它才能处理 .hwp
LICENSE.txt
THIRD-PARTY-NOTICES.md
```

**无需安装。** 整个文件夹随便搬，只要 `rhwp` 在旁边就行。

**macOS · Linux**

```bash
tar xzf kayatext-macos-arm64.tar.gz
cd kayatext-macos-arm64
./kayatext --version
```

**Windows** —— PowerShell 不会搜索当前文件夹，所以需要 `.\`。在 `cmd.exe` 中直接写
`kayatext` 即可。

```powershell
cd kayatext-windows-x64
.\kayatext.exe --version
```

### 在任何位置调用

放进 `PATH` 就可以省掉 `./`。**两个文件要放在同一个文件夹里** —— `rhwp` 不在旁边，
`.hwp` 就用不了。

```bash
sudo cp kayatext rhwp /usr/local/bin/          # macOS · Linux
```

```powershell
# Windows —— 把整个文件夹搬过去，再把该文件夹加进 PATH，这样更省事
move kayatext-windows-x64 C:\tools\kayatext
setx PATH "%PATH%;C:\tools\kayatext"
```

> **没有签名，所以系统会拦一次。** 这是个人项目，还没有附上开发者证书。**文件没有
> 损坏。**
>
> - **macOS** —— 系统设置 → 隐私与安全性 →「仍要打开」
> - **Windows** —— 在 SmartScreen 的蓝色窗口点「更多信息」→「仍要运行」

---

## 许可

**不是开源软件。** 发布包里的 `LICENSE.txt` 是全文。

**任何人都可以免费使用** —— 家庭和企业自不必说，政府机关、学校、非营利组织同样如此，
商业与非商业用途皆可。

**不设功能限制。** 没有付费版，发布的就这一个版本。里面没有统计用量或锁定功能的代码。

### 企业许可是可选的

**买或不买都行。程序不会因此不同。**

有些机构在引进审查或审计时，必须**用书面材料证明自己有权使用这款软件**。「因为免费所以
就用了」在那种场合过不了关。为这些机构准备了**许可证书、发票、优先支持**。**功能一项
也不会增加。**

**禁止再分发。** 把安装包放在别人能取走的地方也属于此列 —— 公司共享文件夹、文件
服务器、协同办公系统的资料库、网盘、开启了共享的云盘都算。介绍给别人时，请转发本仓库
的地址而不是文件。如果确有在组织内分发的需要，请提 Issue，我们可以另行商定条件。

**提取出的文本没有任何限制。** 完全属于你。

### 开源声明

```bash
./kayatext --licenses
```

发布包中的 `THIRD-PARTY-NOTICES.md` 也有全文。

HWP 转换建立在 [rhwp](https://github.com/edwardkim/rhwp)（MIT）之上。没有那个项目，
就没有这个工具。

---

## 为什么没有源码

这是个人开发的工具，我选择了阻止再分发。取得渠道只有一处，才能知道外面在流通哪个
版本，修好的东西也才真正送得到。

我向其他开源项目所做的贡献及其依据，都会公开。

---

## 请告诉我处理不了的文档

有些缺陷用测试文件抓不到，因为**它们只存在于真实文档里** —— 11 列合并、混着图形的
表格、用旧版文字处理软件做的表单。

### 最有帮助的

**已公开的政府或公共机构文档。** 公告、表格、新闻稿 —— 不用把文件发给我，
**只要能下载的网址**就够了。

### 如果是公司文档 —— 请不要上传

业务文档里有姓名和联系方式，而且**作者信息还会留在文件属性中**。一旦发到公开的地方
就收不回来，上传的人也可能因此为难。

请改为**只描述症状**。像「一份带表格的 3 页公文，从第 2 页起框线消失」这样就够了，
我会做一个类似的文件来复现。若确实需要文件，我们私下交换，我会说明如何匿名化。

### 一点谢意

如果你提供的东西确实抓到了缺陷，或进入了回归测试 ——

- 会把你的名字列入回归测试清单（如果你愿意）
- **免费赠送一份企业许可证书。** 无论在公司还是别处，本来就是免费的，用它并不需要
  这个。它只对**必须提交书面材料**应对引进审查或审计的人有价值

请不要为了拿证书而勉强发送文件。
**一个网址胜过十个文件。**

---

Copyright © 2026 가야태자 (kjh0523). All rights reserved.
