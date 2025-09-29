---
title: GitHub 사용하기
date: 2025-09-26 22:58:00 +0900
categories: [Git, 얄팍한 코딩사전]
tags: [Git]
toc: true
---

### **1. GitHub은 뭐고 왜 사용하는가?**

> GitHub는 쉽게 말해 곧, **코드 공유 및 협업 서비스!**

: Git으로 관리하는 모든 프로젝트들을 **온라인 공간에 공유**해서 프로젝트 구성원들이 함께 소프트웨어를 만들어낼 수 있도록 도와주는 서비스

GitHub 등의 온라인 Git 저장소는 모든 업로드와 다운로드를 **커밋 <sup>Commit</sup>** 단위로 주고 받는다.

#### **시나리오**

1. A가 작업을 먼저 작업을 마치고 커밋을 해서 버전(version)을 만들고 업로드를 하면 GitHub 상의 프로젝트는 **해당 버전으로 최신화**가 된다.
2. 다음 B가 완료한 작업을 커밋해서 업로드하기 위해서는 반드시 GitHub 상의 최신 커밋을 먼저 다운받아서 본인 컴퓨터에 있는 프로젝트에 적용부터 하도록 **강제**가 된다.
3. 커밋 상의 충돌 상황이 있다면 그것도 B의 컴퓨터에서 해결해서 병합(merge)의 방식이든 처리하고 나서야 비로소 B가 작업한 커밋을 업로드 할 수 있다.

🔹 위와 같이 각자 원하는 때의 본인의 작업 내용을 협업 공간에 업로드 하는 것을 **GitHub가 중간에서 교통 정리 역할**을 하여 서로의 작업을 덮어씌우거나 할 걱정 없는 편한 협업 환경을 제공한다.

#### **참고**

- **GitLab**: GitHub와 유사한 Git 기반 웹 서비스로, 오픈소스이며 자체 호스팅이 가능한 대안
- **Bitbucket**: Atlassian에서 제공하는 Git 기반 서비스로, Jira, Confluence와의 통합이 강점

---

### **2. 원격 저장소 사용하기**

#### **2-1. 로컬에 원격 저장소 추가 후 Push**

> 강의에서는 `HTTPS` 프로토콜을 사용

```terminal
git remote add origin (원격 저장소 주소)
```

- 로컬의 Git 저장소에 원격 저장소로의 연결 추가
  - 원격 저장소 이름에 흔히 `origin` 사용. 다른 것으로 수정 가능

```terminal
git branch -M main
```

- GitHub 권장: 기본 브랜치명을 `main`으로

```terminal
git push -u origin main 
```

- 로컬 저장소의 커밋 내역들 원격으로 **push(업로드)**
    - `-u` 또는 `--set-upstream` : 현재 브랜치와 명시된 원격 브랜치 기본 연결

⭐️ **GitHub의 해당 레포지토리 페이지 새로고침하여 살펴볼 것**

- 파일들 내용
- 커밋 내역들

**🔹 참고 명령어**

```terminal
git remote: 원격 목록 보기
git remote -v: 자세히 보기
git remote remove (원격 이름): 원격 지우기 (로컬 프로젝트와의 연결만 없애는 것. GitHub의 레포지토리는 지워지지 않음)
```

#### **2-2. GitHub에서 프로젝트 다운받기**

- `Download ZIP`: 파일들만 다운받음, Git 관리내역 제외
- Git clone: Git 관리내역 포함 다운로드

> 터미널이나 Git Bash에서 대상 폴더 이동 후

```terminal
git clone (원격 저장소 주소)
```

---

### **3. push과 pull**

#### **3-1. 원격으로 커밋 밀어 올리기 (push)**

1. 로컬에서 변경 사항 만들기
2. 아래 명령어 입력
```terminal
git push
```
- 이미 `git push -u origin main`으로 대상 원격 브랜치가 지정되었기 때문에 가능
3. GitHub에서 확인
- GitHub의 파일들과 커밋 내역 확인

#### **3-2. 원격의 커밋 당겨오기 (pull)**

1. GitHub에서 변경 사항 만들기
2. 아래 명령어 입력
```terminal
git pull
```
3. 로컬에서 파일과 로그 살펴보기

#### **3-3. pull할 것이 있을 때 push를 하면**

1. 로컬에서 변경 사항 만들기
2. GitHub에서 변경 사항 만들기
3. push 시도 시
   - 원격에 먼저 적용된 새 버전이 있으므로 적용 불가
   - pull 해서 원격의 버전을 받아온 다음 push 가능
4. push 할 것이 있을 시 pull 하는 두 가지 방법
   - `git pull --no-rebase` - **merge** 방법
     - GUI에서 확인
     - **reset** 후 아래 방식 시도
   - `git pull --rebase` - **rebase** 방법
     - pull 상의 rebase는 협업 시 사용 가능

#### **참고**

> 로컬의 내역 원격에 강제로 push

```terminal
git push --force
```

---

### **4. 원격의 브랜치 다루기**

#### **4-1. 로컬에서 브랜치 생성하여 원격에 push**

1. 로컬에서 브랜치 생성
```terminal
git switch -c (브랜치 이름)
```
2. git push 시도
   - **대상을 명시하라는 메시지**가 나옴
3. 아래 명령어로 원격의 브랜치 명시 및 기본설정
```
git push -u origin (로컬에서 만든 브랜치 이름)
```
4. 브랜치 목록 살펴보기
   - GitHub에서 목록 보기
   - 아래 명령어로 로컬과 원격의 브랜치들 확인
```terminal
git remote -all
git remote -a
```

#### **4-2. 원격의 브랜치 로컬에 받아오기**

1. GitHub에서 브랜치 생성
   - `git branch -a`에서 지금은 보이지 않음
2. 아래 명령어로 원격의 변경사항 확인
```terminal
git fetch
```
- `git branch -a` 로 확인
3. 아래 명령어로 로컬에 같은 이름의 브랜치를 생성하여 연결하고 switch
```terminal
git switch -t (원격 이름)/(GitHub에서 만든 브랜치 이름)
```

#### **4-3. 원격의 브랜치 삭제**

```terminal
git push (원격 이름) --delete (원격의 브랜치명)
```

---
