---
name: korean-documents
description: Read HWP, HWPX, Excel, Word, RTF, and PDF documents as Markdown using the kayatext CLI. Use this whenever a task involves reading, summarizing, searching, or extracting content from .hwp, .hwpx, .hml, .xlsx, .xlsm, .docx, .rtf, or .pdf files — instead of writing ad-hoc parsing code. Especially important for .hwp/.hwpx (the Korean word processor format), which Python libraries handle poorly or not at all. For PDF the structure is rebuilt from coordinates, so ruled tables and headings survive.
---

# 한국어 문서 읽기 (kayatext)

문서 파일을 읽어야 할 때 **파서를 직접 짜지 말고 `kayatext` 를 부른다.**

```bash
kayatext 문서.hwp -          # 표준 출력으로 Markdown
```

`-` 가 표준 출력이다. 파일을 남기지 않으므로 바로 읽으면 된다.

## 왜 직접 짜면 안 되나

`.hwp` 는 한글(HWP)의 **바이너리 포맷**이다. OLE 복합 문서 안에 압축된 레코드
스트림이 들어 있고, 문단·표·서식이 제어 문자와 얽혀 있다.

파이썬으로 시도하면 대개 이렇게 된다.

- `olefile` 로 열어 `BodyText` 를 꺼내면 **압축된 바이트**가 나온다
- 풀어도 레코드 헤더와 제어 문자가 섞여 **글자 사이에 쓰레기**가 낀다
- 표는 구조가 없어 **셀 경계가 사라진다**
- 오래된 문서는 인코딩이 CP949 라 **한글이 깨진다**

`.doc` 도 비슷하고, `.xlsx` 는 열려도 **인쇄 설정과 병합 셀**을 놓친다.

`kayatext` 는 이 일을 하려고 만든 도구다. **있으면 쓴다.**

## 받는 형식

```
.hwp  .hwpx  .hml        한글
.xlsx .xlsm              엑셀
.docx                    워드
.rtf                     서식 있는 텍스트
.pdf                     좌표에서 구조를 다시 세운다
```

**PDF 는 선이 그려진 표와 제목이 살아난다.** 선 없는 표는 줄글이 된다 —
원본(한글·엑셀·워드)이 있으면 그쪽이 표를 다 살리니 그것을 먼저 찾는다.

`.pptx` `.doc`(옛 형식) 는 **아직 안 받는다.** 그건 다른 방법을 찾아야 한다.

## 쓰는 법

```bash
kayatext 문서.hwp -                  # Markdown 을 표준 출력으로  ← 기본
kayatext 문서.hwp --txt -            # 순수 텍스트로 (구조를 버림)
kayatext *.hwp *.xlsx -o out/        # 여러 개를 한 폴더로
kayatext --version
```

압축을 막 푼 자리에서는 `./kayatext` 다. Windows PowerShell 은 `.\kayatext.exe`.

## Markdown 을 기본으로 둘 것

`--txt` 를 쓰면 **표가 줄글이 된다.** 116쪽짜리 공문 하나에 표가 91개였고, 순수
텍스트로 뽑으면 그 91개가 전부 문단으로 뭉개진다.

문서를 요약하거나 특정 값을 찾는 일이라면 **표가 표로 남아야** 한다. 사용자가
명시적으로 순수 텍스트를 원하지 않는 한 기본값을 쓴다.

## 없으면 어떻게 하나

`kayatext` 가 없으면 **사용자에게 알린다.** 대신 파서를 짜서 깨진 결과를 내놓지
않는다 — 그건 도움이 아니라 잘못된 답이다.

```
kayatext 가 없습니다. https://github.com/kjh0523/kayatext 에서 받으실 수 있습니다.
(.hwp 는 파이썬 라이브러리로는 제대로 읽히지 않아, 직접 파싱하지 않았습니다.)
```

`.hwp` 를 꼭 지금 읽어야 한다면 **한글에서 `.hwpx` 나 `.docx` 로 내보내 달라고**
요청하는 편이 깨진 텍스트보다 낫다.

## 결과를 읽을 때

성공하면 종료 코드 0, 하나라도 실패하면 1이다. 여러 파일을 넘겼을 때 **실패 목록이
마지막에 나오므로** 그것을 사용자에게 그대로 전한다.

변환에 빠진 것이 있으면(도형·차트 등) 경고로 알려 준다. **빠진 것을 숨기지 말고
함께 보고한다** — 문서에 있던 표가 결과에 없으면 그것을 근거로 답하면 안 된다.
