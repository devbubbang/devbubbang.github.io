---
title: 커밋 취소하기
date: 2025-05-12 21:56:00 +0900
categories: [Git, FixNote]
tags: [Git, Commit]
toc: true
---

**📅 발생일자 : 2025-05-12**

## **⚠️ 문제 상황**

- Azure DevOps의 main 브랜치에서 직접 작업을 진행함
- Branch Policies가 설정되어 있어 직접 push가 불가능한 상황
- 이미 로컬에서 여러 커밋을 했는데, 이 커밋들을 취소하고 올바른 방식으로 다시 작업해야 함

## **🔍 원인 분석**

- Azure DevOps의 Branch Policies 설정으로 인해:
  - 직접 push가 차단되어 있음
  - PR(Pull Request) 기반의 merge만 가능
  - 최소 1명의 리뷰어 지정 필요
  - 최소 1개의 work item 연동 필요
- 이러한 정책을 고려하지 않고 main 브랜치에서 직접 작업을 진행함
- 따라서 현재 커밋들을 취소하고, 새로운 feature 브랜치를 만들어 작업을 다시 시작해야 함

## **🛠️ 해결 방법**

1. 현재 main 브랜치의 커밋 취소
   ```bash
   # main 브랜치의 모든 커밋을 취소하고 원격 저장소와 동기화
   git reset --hard origin/main
   ```

2. 새로운 feature 브랜치 생성 및 작업 시작
   ```bash
   # 새로운 feature 브랜치 생성
   git checkout -b feature/작업내용
   
   # 이전에 작업했던 내용을 새 브랜치에 적용
   # (필요한 경우 이전 작업 내용을 복사하여 새로 커밋)
   ```

3. 올바른 작업 흐름으로 진행
   - feature 브랜치에서 작업 진행
   - 작업 완료 후 PR 생성
   - 리뷰어 지정 및 work item 연동
   - 리뷰 승인 후 merge

## **✅ 최종 상태 확인**

- `git branch` 명령어로 현재 브랜치 확인
- `git log` 명령어로 커밋 히스토리 확인
- Azure DevOps에서 PR 생성 가능 여부 확인
- work item 연동 상태 확인

## **💡 재발 방지 대책**

1. 브랜치 작업 전 체크리스트
   - 현재 브랜치 확인 (`git branch`)
   - Branch Policies 확인
   - work item 확인

2. 올바른 작업 흐름
   - main 브랜치에서 직접 작업하지 않기
   - 항상 feature 브랜치에서 작업 시작
   - PR 기반으로 작업 진행
   - 리뷰어 지정 및 work item 연동 필수

## **📝 관련 명령어 정리**

```bash
# 현재 브랜치 확인
git branch

# 원격 저장소와 동기화
git fetch origin

# main 브랜치 커밋 취소
git reset --hard origin/main

# 새로운 feature 브랜치 생성
git checkout -b feature/작업내용

# 변경사항 확인
git status
git log
```

## **📌 참고**

### **Azure DevOps의 Branch Policies**

1. 프로젝트로 이동
2. Repos > Branches 메뉴 진입
3. main 브랜치 우측의 **... (More options)** 클릭 -> `Branch polices` 선택
4. 아래 옵션을 설정

| 옵션                                                      | 설명                                                      |
| --------------------------------------------------------- | --------------------------------------------------------- |
| **Require a minimum number of reviewers**                 | 직접 push를 막고 PR(풀리퀘스트) 기반의 merge를 강제합니다 |
| **Check for linked work items**                           | 작업 항목 연동을 요구할 수 있음                           |
| **Check for comment resolution**                          | PR 내 모든 댓글 해결 요구 가능                            |
| **Limit merge types**                                     | 예: squash merge만 허용                                   |
| **Build Validation**                                      | 빌드 파이프라인 통과 후 병합 가능하도록 제한              |
| **Allow users to push directly to this branch 체크 해제** | 이 항목의 체크를 해제하면 직접 push 차단                  |

> 현재 Pull Request 생성 후 최소 리뷰어 1, 최소 work item 1을 지정하면서 주로 작업함.

### **Commit 취소 방식식**

#### **1. 커밋만 취소하고 작업 내용은 그대로 유지 (`--soft`)**

```bash
git reset --soft HEAD~1
```

- 설명 : 마지막 커밋만 제거하고, 그 커밋에 포함됐던 변경 사항은 staging area(index)에 그대로 남아 있는다.
- 활용 예 : 메시지를 수정하거나 커밋을 다시 정리하고 싶을 때

#### **2. 커밋과 staging area까지 모두 취소 (`--mixed`)**

```bash
git reset --mixed HEAD~1
```

- 설명 : 마지막 커밋을 제거하고, 변경사항은 working directory에 남아 있으며, staging area에서는 제거된다. (기본 옵션)
- 활용 예 : 커밋을 취소하고 다시 `git add` 부터 새로 하고 싶을 때

#### **3. 커밋도, 작업 내용도 모두 제거 (`--hard`)**

```bash
git reset --hard HEAD~1
```

- 설명 : 커밋 뿐 아니라 해당 커밋에서 작업한 내용까지 모두 삭제된다.
- ⚠️ 주의 : 복구 불가능할 수 있으므로 꼭 백업 또는 현재 상태를 확인하고 사용해야 한다.

#### **`revert`를 사용한 커밋 취소 (기록은 남김)**

```bash
git revert HEAD
```

- 설명 : 이전 커밋의 변경사항을 되돌리는 새로운 커밋을 생성한다.
- 비교 예시 : `reset`은 기록 자체를 지우고, `revert`는 되돌리는 기록을 남긴다.
- **Public Branch(예: main, master)** 에서는 `reset` 보다 `revert`가 권장된다.

**Update date : 2025-05-12**