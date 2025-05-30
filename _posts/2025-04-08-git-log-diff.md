---
title: "변경 사항 확인하기 | log & diff"
date: 2025-04-08 23:15:00 +0900
categories: [Git, Version Management]
tags: [Git]
---

## **커밋 기록 및 변경 내용 확인**

- **`git log`**는 Git 저장소의 **커밋 히스토리**를 시간 역순으로 보여준다.
- **`git log -p`**는 커밋의 변경 내용까지 확인 가능하다.
 - `+`로 시작하는 줄 : 새로 추가된 코드
 - `-`로 시작하는 줄 : 삭제된 코드
 - `-- a/파일명`, `+++ b/파일명` : 이전(a)과 이후(b)의 파일명

## **변경 사항 비교**

- **`git diff`**는 다양한 시점 간의 변경 내용을 보여준다.

**🔸 워킹 디렉토리 vs 스테이징 영역**
  - 아직 `git add` 되지 않은 변경 내용을 확인할 수 있다.

**🔸 스테이징 영역 vs 마지막 커밋**

```bash
git diff --staged
# 또는
git diff --cached
```
▶ 커밋 예정인 코드 확인용

- **🔸 특정 커밋 간 비교**

```bash
git diff <commitID1> <commitID2>
```

▶ 두 커밋 사이에서 어떤 파일이 어떻게 바뀌었는지 보여줌
▶ 삭제된 파일은 `/dev/null`로 표시됨

- **🔸 현재 작업 vs 특정 커밋**
```bash
git diff <commitID>
```

▶ 현재 작업 디렉토리의 상태와 과거 커밋을 비교

---
