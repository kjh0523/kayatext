# AI 에이전트용 스킬

Claude 같은 도구에 **한글 문서를 읽으라고 하면 직접 파서를 짭니다.** `.hwp` 는
바이너리 포맷이라 그렇게 하면 대개 깨진 글자가 나오거나 표가 사라집니다.

이 스킬은 그럴 때 **`kayatext` 를 대신 부르도록** 알려 줍니다.

---

## 먼저 kayatext 가 있어야 합니다

스킬은 지시문일 뿐이고, 실제 변환은 `kayatext` 가 합니다.

[내려받기](../../../releases/latest) → 압축을 풀고 `PATH` 에 두세요.
`rhwp` 도 **같은 폴더에** 있어야 `.hwp` 가 됩니다.

```bash
sudo cp kayatext rhwp /usr/local/bin/
kayatext --version
```

---

## 설치

### Claude Code

```bash
mkdir -p ~/.claude/skills/korean-documents
curl -sL -o ~/.claude/skills/korean-documents/SKILL.md \
  https://raw.githubusercontent.com/kjh0523/kayatext/main/skill/SKILL.md
```

프로젝트 하나에만 쓰려면 `~/.claude` 대신 그 프로젝트의 `.claude` 아래에 둡니다.

### claude.ai

설정 → Skills 에서 `SKILL.md` 를 올립니다.

---

## 확인

새 세션에서 한글 문서를 읽어 달라고 해 보세요.

```
이 파일 요약해 줘: 회의록.hwp
```

**스킬이 걸리면** `kayatext 회의록.hwp -` 를 부릅니다.
**안 걸리면** 파이썬으로 `olefile` 을 열려고 합니다 — 그러면 스킬이 안 깔린 것입니다.

---

## 무엇을 시키나

- `.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf` 는 **파서를 짜지 말고**
  `kayatext` 를 부른다
- **Markdown 을 기본으로** 쓴다. 순수 텍스트로 뽑으면 표가 줄글이 된다 —
  116쪽 공문 하나에 표가 91개였다
- `kayatext` 가 없으면 **사용자에게 알린다.** 대신 파서를 짜서 깨진 결과를
  내놓지 않는다 — 그건 도움이 아니라 잘못된 답이다
- 변환에 빠진 것이 있으면(도형·차트 등) **함께 보고한다.** 문서에 있던 표가
  결과에 없으면 그것을 근거로 답하면 안 된다

마지막 둘이 중요합니다. **모르는 것을 아는 척하지 않게** 하는 것이 이 스킬이
막으려는 진짜 실패입니다.

---

## 아직 안 되는 것

`.pdf` `.pptx` `.doc` 는 `kayatext` 가 못 읽습니다. 스킬도 그렇게 말합니다.

MCP 서버는 준비 중입니다. 설치 없이 `npx` 로 쓰게 하는 것이 목표입니다.
