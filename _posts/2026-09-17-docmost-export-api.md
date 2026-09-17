---
title: "Docmost Export API로 마크다운 뽑기"
date: 2026-09-17 22:20:28 +0900
categories: ["Infra & Automation", "Docmost"]
tags: [docmost, api]
toc: true
---

## 한 줄 요약

Docmost 페이지는 `POST /api/pages/export` 한 번으로 마크다운을 그대로 뽑을 수 있다. 표·코드블록·mermaid는 유지되고, 블로그로 옮길 때 손볼 곳은 **제목 줄 · 이미지 · 콜아웃** 세 군데뿐이다.

## 배운 것

### 요청

- `POST /api/pages/export`, 본문 `{"pageId": "페이지 UUID", "format": "markdown"}`
- 헤더 `Authorization: Bearer 토큰` (API 토큰 또는 로그인 JWT)
- 응답은 JSON이 아니라 **마크다운 파일 자체** (`application/octet-stream`)

### 그대로 유지되는 것

- 제목, 목록(중첩 포함), 인용
- 코드블록 — 언어 이름(yaml, mermaid 등)까지
- GFM 표 (`| --- |` 구분선 포함)

### 달라지는 것

| 항목  | 내보낸 결과 | 블로그(Jekyll)로 옮길 때 |
| --- | --- | --- |
| 페이지 제목 | 첫 줄에 `# 제목` | 지우고 front matter `title:`로 |
| 이미지 | `![](/api/files/파일ID/파일명)` — 로그인 없이 열면 **401** | 토큰으로 내려받아 저장소에 넣고 경로 교체 |
| 콜아웃 | `:::info` … `:::` (Docmost 전용) | Chirpy 테마면 `> 내용` + `{: .prompt-info}` |
| 번호 제목 | `## 1\. 제목` 처럼 마침표 이스케이프 | 그대로 두거나 `1.`로 복원 |

### 반대 방향도 된다

`POST /api/pages/update` 에 `{"content": "마크다운", "format": "markdown", "operation": "replace"}` 를 보내면 서버가 같은 문법( `:::info` 콜아웃, GFM 표)을 해석해서 페이지로 만든다. 블로그 글 24편을 Docmost로 옮길 때 이 방식을 썼다.

## 코드 · 명령

```bash
# 페이지 → 마크다운 파일
curl -s -X POST "$DOCMOST_URL/api/pages/export" \
  -H "Authorization: Bearer $DOCMOST_TOKEN" \
  -H 'Content-Type: application/json' \
  --data '{"pageId":"PAGE_UUID","format":"markdown"}' \
  -o page.md

# 본문에 있던 이미지 내려받기 (토큰 필요)
curl -s "$DOCMOST_URL/api/files/FILE_ID/image.png" \
  -H "Authorization: Bearer $DOCMOST_TOKEN" -o image.png
```

## 계기 / 출처

- 개인 위키(Docmost)에 먼저 쓰고 블로그로 옮기는 흐름을 만들면서, 내보내기 결과가 블로그에 그대로 쓸 만한지 확인했다.
- 확인 환경: 셀프호스팅 Docmost 0.96.0 (2026-09-17)
