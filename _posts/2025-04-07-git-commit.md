---
title: "git commit"
date: 2025-04-07 22:00:00 +0900
categories: [Git, Version Management]
tags: [Git, 명령어]
---

## **개요**
`git commit` 명령어는 **스테이지에 올라간 변경 사항들을 하나의 버전으로 저장**하는 데 사용된다. Git 저장소의 **변경 이력을 기록**하며, 각 커밋은 고유한 ID를 갖는다.

---

## **사전 설정 (최초 1회)**
커밋 작성자 정보를 등록해야 한한다. 이는 모든 커밋에 작성자 정보를 포함하기 위함이다.

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

- `--global` 옵션은 전체 시스템에 한 번만 설정하면 됨
- 커밋 메시지에는 이 정보가 포함되어 **작성자 식별이 가능**

## **커밋 실행**

```bash
git commit
```

- 기본 텍스트 에디터가 열리며, **커밋 메시지를 입력**
- 커밋 메시지는 해당 변경사항의 **의미 있는 설명**을 포함해야 함

## **커밋 결과**

- 커밋이 완료되면 해당 상태가 Git 저장소에 **영구 기록**
- 이후 `git log` 명령어로 커밋 이력을 확인할 수 있음
