---
title: 잘못된 Github 계정으로 커밋
date: 2025-04-25 23:08:00 +0900
categories: [Git, FixNote]
tags: [Git]
toc: true
---


**Date : 2025-04-25**

## **문제 상황**

- 두 개의 Github 계정이 존재
  - 도메인이 `@gmail.com`인 계정 1개 > A라고 지칭
  - 도메인이 `@naver.com`인 계정 1개 > B라고 지칭
- 평소 작업용 노트북에서 **A 계정**의 repository를 clone하여 작업
- 그러다 다른 PC (회사 PC) 에서 같은 **A 계정**의 repository를 clone 한 후, 작업 및 commit을 진행
- `git push origin main` 명령어를 통해 최종 push를 진행했는데 commit 사용자 정보가 **B 계정**으로 되어있었음
  - Github 웹 페이지에서까지 B 계정 프로필 아이콘이 commit에 나타나서 문제가 생겼음을 인지

## **원인 분석**

- 회사 PC의 `git config` 설정이 B 계정 정보로 되어 있었음
- Git은 커밋 시점에 설정된 `user.name`과 `user.email`을 바탕으로 커밋 작성자를 기록함
- Github는 이메일 주소 기준으로 계정 소유자를 연결함
- 결과적으로 커밋은 A 계정의 레포지토리에 올라갔지만 작성자(author)는 B 계정으로 보이게 됨

## **해결 방법**

- 노트북에서 Git 사용자 정보를 A 계정으로 재설정 (= 최근 커밋을 올바른 사용자 정보로 수정)

```bash
git config user.name "A계정의 이름"
git config user.email "A계정에 등록된 이메일"
```
- 그리고 최근 커밋을 `--amend`로 수정

```bash
git commit --amend --reset-author
# --reset-author는 현재 git config의 유저 정보로 커밋 작성자 정보를 덮어 씌운다.
```

## **최종 상태 확인**

- `git log --pretty=full` 명령어로 확인 시 모든 커밋이 A 계정으로 일치하는지 확인
- GitHub 웹 UI에서도 A 계정의 프로필 아이콘이 정상적으로 표시되는지 확인

## **재발 방지 대책**

- 새로운 환경 (회사 PC, 다른 사람 PC 등)에서 작업하기 전에 Git 정보 확인 필수

## **관련 명령어 정리**

```bash
# 커밋 확인
git log --pretty=full

# 사용자 정보 조회
git config user.name
git config user.email

# 사용자 정보 수정

git config user.name "수정할 계정"
git config user.email "수정할 계정에 등록된 메일"

# 커밋 작성자 재설정
git commit --amend --reset-author

```

---
