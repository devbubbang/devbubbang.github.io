---
title: "GitHub 잔디 0칸이면 폰으로 알림 보내기"
date: 2026-09-19 14:35:00 +0900
categories: ["Infra & Automation", "ntfy"]
tags: [ntfy, cron, python, github]
toc: true
---

## 한 줄 요약

GitHub 잔디가 0칸인 날 밤 10시에 폰으로 푸시를 보내는 cron을 만들면서, 잔디 HTML·ntfy·urllib에서 하나씩 막혔다. 그중 하나는 **정작 알림이 필요한 날에만 조용히 실패하는** 종류였다.

## 배운 것

### 1. 잔디 칸에는 커밋 수가 없다

공개 잔디 페이지(`https://github.com/users/<계정>/contributions`)의 날짜 칸은 이렇게 생겼다.

```html
<td data-date="2026-09-17" id="contribution-day-component-4-52"
    data-level="3" role="gridcell" class="ContributionCalendar-day"></td>
```

칸 자체에는 **색 단계(**`data-level` **0~4)만 있고 커밋 수가 없다.** 숫자는 칸의 `id`를 가리키는 별도 엘리먼트에 문장으로 들어 있다.

```html
<tool-tip for="contribution-day-component-4-52" class="sr-only">9 contributions on September 17th.</tool-tip>
```

그래서 읽는 게 2단계다. 날짜로 칸을 찾아 `id`를 꺼내고 → 그 `id`를 `for`로 갖는 `<tool-tip>`을 찾아 문장을 파싱한다.

날짜 칸을 `data-date` 값으로 찾는 것도 중요하다. 같은 페이지에 작년 같은 날짜 칸이 또 있어서 "September 17th" 같은 문구로 찾으면 엉뚱한 칸을 집는다.

### 2. 0칸인 날은 숫자로 안 적힌다 — 제일 위험했던 지점

문장을 실제로 모아보면:

| 커밋  | 문구  |
| --- | --- |
| 9건  | `9 contributions on September 17th.` |
| 1건  | `1 contribution on January 1st.` — 단수라 `contribution` |
| 0건  | `No contributions on September 18th.` |

`(\d+)\s+contributions` 같은 정규식을 쓰면 **0칸인 날에만** 매칭에 실패한다. 커밋한 날은 전부 멀쩡히 통과하니까 평소엔 아무 문제가 없어 보이고, 정작 알림을 보내야 하는 단 하나의 날에 "파싱 실패"로 빠진다.

`(No|\d+)\s+contributions?`로 받고 `No`를 0으로 매핑해야 한다.

교훈은 정규식이 아니라 **테스트 데이터 고르는 법**이었다. 커밋이 있는 날로만 시험하면 절대 안 걸린다. 내가 감지하려는 조건이 하필 "예외 표기"를 쓰고 있는지 먼저 봐야 했다.

### 3. 한글은 HTTP 헤더에 못 넣는다

ntfy는 알림 제목을 `Title` 헤더로 받는다. 그런데 한글 제목을 넣으면 요청이 나가기도 전에 터진다.

```python
urllib.request.Request(url, headers={"Title": "오늘 커밋 0칸"})
# UnicodeEncodeError: 'latin-1' codec can't encode characters...
```

`http.client`가 HTTP 헤더를 latin-1로 인코딩하기 때문이다(RFC 규격상 헤더는 ASCII 범위). 우회하려면 RFC 2047 base64로 인코딩해야 하는데, ntfy는 **JSON 본문으로도 같은 값을 받는다.**

```python
body = {"topic": TOPIC, "title": "오늘 커밋 0칸", "message": "...", "priority": 4}
req = urllib.request.Request("https://ntfy.sh/", data=json.dumps(body).encode(),
                             headers={"Content-Type": "application/json"})
```

본문으로 보내면 인코딩 문제가 아예 없다. `json.dumps`가 기본값 `ensure_ascii=True`라 실제 전송 바이트는 순수 ASCII 이스케이프로 나가고, 서버에서 다시 한글로 복원된다.

### 4. cron은 `LANG`을 물려주지 않는다 — 확인 결과는 괜찮았음

한글 로그를 파일로 리다이렉트하는 스크립트라 cron에서 `UnicodeEncodeError`가 날 가능성이 걱정됐다. cron은 `HOME`·`LOGNAME`·`PATH`·`SHELL`만 주고 `LANG`/`LC_*`를 안 준다. SSH 접속으로 테스트하면 내 `LANG`이 딸려 들어가서 이 차이가 안 보인다.

`env -i`로 cron 환경을 그대로 재현해서 확인했더니 Ubuntu 22.04 / Python 3.10에서는 `sys.stdout.encoding`이 `utf-8`이었다. PEP 538이 로케일이 없을 때 `C.UTF-8`로 올려주기 때문. **문제는 없었지만, 확인 방법 자체가 건진 것이다.**

### 5. 하루 기다리지 않고 양쪽 갈래를 시험하는 법

"0칸이면 보낸다 / 있으면 안 보낸다"를 확인하려면 원래 밤 10시까지 기다려야 한다. `--date YYYY-MM-DD`로 검사 날짜를 바꿀 수 있게 하니 **잔디에 이미 남아 있는 과거 날짜가 그대로 테스트 픽스처**가 됐다. 커밋이 있던 날, 0칸이던 날, 아예 없는 날짜 셋을 그 자리에서 돌려봤다.

`--dry-run`(전송 없이 보낼 내용만 출력)도 같이 두면 실제 푸시를 쏘지 않고 분기만 확인할 수 있다.

## 코드 · 명령

핵심은 칸 → `id` → tool-tip 2단계 파싱이다.

```python
def parse_count(html, date_str):
    cell = re.search(r'<[a-zA-Z-]+([^>]*\bdata-date="%s"[^>]*)>' % re.escape(date_str), html)
    if not cell:
        raise CheckFailed("%s 날짜 칸을 찾지 못함" % date_str)
    attrs = cell.group(1)
    cell_id = re.search(r'\bid="([^"]+)"', attrs).group(1)
    level = int(re.search(r'\bdata-level="(\d+)"', attrs).group(1))

    tip = re.search(r'<tool-tip[^>]*\bfor="%s"[^>]*>(.*?)</tool-tip>' % re.escape(cell_id), html, re.S)
    text = re.sub(r"\s+", " ", tip.group(1)).strip()

    m = re.match(r"(No|[\d,]+)\s+contributions?\b", text, re.I)   # No를 빠뜨리면 안 됨
    raw = m.group(1)
    count = 0 if raw.lower() == "no" else int(raw.replace(",", ""))

    # 문장에서 읽은 수와 칸 색이 어긋나면 파싱이 깨진 것으로 본다
    if (count == 0) != (level == 0):
        raise CheckFailed("칸 수(%d)와 data-level(%d)이 어긋남" % (count, level))
    return count, level
```

마지막 교차 검증은 값이 두 군데에 중복으로 있으니까 공짜로 얻는 안전장치다. 조용히 틀린 알림을 보내는 것보다 "확인 실패"로 드러나는 쪽이 낫다.

cron 환경 재현:

```bash
env -i HOME=$HOME LOGNAME=$USER USER=$USER SHELL=/bin/sh PATH=/usr/bin:/bin \
  /bin/sh -c 'cd ~/commit-alert && python3 check_commit.py --dry-run'
```

## 계기 / 출처

- 1일 1커밋을 시작하면서, 놓치기 전에 알림을 받으려고 만들었다. 개인 서버 cron이 매일 13:00 UTC(= 22:00 KST)에 잔디를 확인하고 0칸이면 ntfy로 폰에 푸시를 보낸다.
- 잔디 HTML 구조와 tool-tip 문구는 2026-09-19에 직접 받아서 확인한 것이다. GitHub가 마크업을 바꾸면 그대로 깨지므로, 못 읽으면 "확인 실패" 푸시가 오게 해뒀다.
- ntfy 공개 서버는 토픽 이름만 알면 누구나 구독할 수 있다. 토픽 이름 자체가 비밀번호라 추측하기 어려운 문자열을 쓰고, 알림 본문에는 칸 수만 넣었다.
