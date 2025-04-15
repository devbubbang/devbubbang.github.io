---
title: "분석도구 gistory"
date: 2025-04-15 23:15:00 +0900
categories : [Git, Principal]
tags: [Git, 원리]
---

## **gisotry란?**

- **gistory**는 Git 저장소의 내부 구조와 동작 과정을 시각적으로 탐색할 수 있도록 도와주는 Python 기반의 오픈소스 도구
- Git 명령어 실행 시 `.git` 디렉토리 내부에서 발생하는 변화들을 웹 인터페이스를 통해 시각화하여, Git의 작동 방식을 보다 명확하게 이해할 수 있도록 설계되었다.

## **주요 기능 및 사용법**

1. **설치 방법**

- Windows > Python이 설치된 환경에서 다음 명령어를 실행하여 설치

```bash
pip install gistory
```

2. **사용 방법**

- 분석하려는 Git 저장소의 `.git` 디렉토리로 이동

```bash
cd /path/to/your/repo/.git
```

- `gistory` 명령을 실행

```bash
gistory
```

- 명령어 실행 후 출력되는 로컬 서버 주소(예 : `http://0.0.0.0:8000`) 를 웹 브라우저에 입력하여 인터페이스에 접속

3. **주의사항**

- `.git` 디렉토리 내부에서 실행해야 하며, 그렇지 않을 경우 오류 메시지가 출력
- Git Bash에서 `gistory`를 실행하면 해당 세션에서는 Git 명령어 사용이 제한될 수 있으므로, 별도의 터미널을 사용하는 것이 좋다.
