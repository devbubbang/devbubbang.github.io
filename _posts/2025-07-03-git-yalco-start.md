---
title: Git 시작하기
date: 2025-07-03 21:23:00 +0900
categories: [Git, 얄팍한 코딩사전]
tags: [Git]
toc: true
---

### **1. Git을 배워야 하는 이유**

- Git은 VCS(Version Control System)<sup>버전 관리 시스템</sup> 란 종류의 프로그램들 중 하나
- **버전 관리**
  - 프로젝트의 `시간`과 `차원`을 관리하는 것
  - 시간 > 프로젝트의 버전을 과거로 되돌리거나 특정 내역을 취소할 수 있다.
  -  차원 > 프로젝트의 여러 모드를 쉽게 전환하고 관리할 수 있다.

```text
🔹 Git은 프로젝트의 시간과 차원을 자유롭게 넘나들 수 있도록 해준다.
🔹 Git은 여러 사람들이 프로젝트에서 협업할 수 있도록 도와준다.
```

---

### **2. Git 설치 (Windows / Mac)**

#### **Windows - 기본 설치**

- [https://git-scm.com/](https://git-scm.com/) 로 이동해서 Git을 다운로드
- ⭐ 설치과정에서 **Git Bash** 반드시 포함 
  - Git 사용에 적합한 `터미널`
  - 리눅스/맥(유닉스)에서 사용되는 CLI 명령어들을 윈도우에서 사용 가능 > 타 프로그래밍에도 유용
  - 기본 설정된 그대로 설치 진행
- 설치 후 Git Bash에서 아래 명령어로 테스트 진행
```terminal
git --version
```
```terminal
git config --global core.autocrlf true
```
- 협업 시 윈도우와 맥에서 엔터 방식 차이로 인한 오류 방지

#### **Windows - SourceTree 설치**

- [https://www.sourcetreeapp.com/](https://www.sourcetreeapp.com/) - Git을 GUI로 다룰 수 있도록 해주는 툴
  - 기타 : GitHub Desktop, GitKraken 등 (https://git-scm.com/downloads/guis 참조)
- 설치 시 BitBucket 계정 관련은 건너뛰기

#### **Windows - 코드 에디터 설치**

- 강의에서는 가장 인기있는 코드 에디터 중 하나인 VS Code 설치 > [https://code.visualstudio.com/](https://code.visualstudio.com/)
- `터미널` 메뉴에서 새 터미널 열어보기
  - 프로그래밍 중 바로 Git 명령어 사용
  - 대부분의 타 에디터/IDE에서도 터미널 기능 제공

#### **Windows - VS Code의 기본 터미널을 Git Bash로 설정**

> Git 뿐 아니라 다른 프로그래밍 작업에 있어서도 유용

- VS Code에서 `Ctrl` + `Shift` + `P`
- `Select Default Profile` 검색하여 선택
- **Git Bash** 선택
- 터미널에서 `+`로 새 창을 열어서 기본으로 Git Bash가 설정된 것 확인
- ➕ Git Bash를 **C 드라이브**에 설치해야 이 설정이 가능
  - Git 등 프로그래밍 관련 소프트웨어들은 VS Code 뿐 아니라 기타 연계할 프로그램에서도 C 드라이브에 설치된 것으로 간주되므로, C 드라이브에 설치해야 위 문제를 비롯한 어려움을 겪지 않을 수 있음
- ➕ Material 테마
  - VS Code에서 강의 영상의 화면과 같이 파일 탐색기 (파일 아이콘들) 부분이 보이게 하려면 왼쪽의 `확장` 탭 (블럭 모양 아이콘)에서 **Material Icon Theme** 테마를 검색하여 설치

#### **Mac**

🔹 Mac은 기본적으로 git이 설치되어 있음

- 협업시 윈도우와 맥에서 엔터 방식 차이로 인한 오류를 방지

```terminal
git config --global core.autocrlf input
```

🔹 iterm2 설치와 터미널 세팅

- [https://iterm2.com/](https://iterm2.com/)에서 iTerm2 다운로드 및 설치
  - 탭, split view, 자동완성 등 다양한 기능

🔹 터미널의 테마와 스타일 설정 (아래중 택일)

- [기본 설정](https://www.yalco.kr/_03_mac_terminal/)
- [디테일 설정](https://yalco.notion.site/iTerm2-7866389fbcc449ebb07a587b8b761303)
- [맥 터미널 꾸미기 oh-my-zsh](https://www.google.com/search?q=%EB%A7%A5+%ED%84%B0%EB%AF%B8%EB%84%90+%EA%BE%B8%EB%AF%B8%EA%B8%B0+oh-my-zsh&oq=%EB%A7%A5+%ED%84%B0%EB%AF%B8%EB%84%90+%EA%BE%B8%EB%AF%B8%EA%B8%B0+oh-my-zsh) 키워드로 검색

---

### **3. CLI vs GUI, 무엇을 사용?**

- CLI <sup>Command Line Interface</sup>
  - 터미널에 명령어를 이용하는 방식
- GUI <sup>Graphical User Interface</sup>
  - 소스트리 등의 프로그램을 사용하는 방식

> 🧠 학습 추천 방식
> - 공부할 때는 CLI 위주로 실습해서 명령어들과 동작 방식을 익히기
> - 사용할 때는 작업의 성질에 따라 편리하고 유리한 것으로 유기적으로 혼용할 것

---

### **4. Git 설정 & 프로젝트 관리하기**

#### **Git 최초 설정**

> 🔹 Git 전역으로 사용자 이름과 이메일 주소를 설정<br />
> 🔹 GitHub 계정과는 별개

- 터미널 프로그램 (Git Bash, iTerm2)에서 아래 명령어 실행

```terminal
git config --global user.name "(본인 이름)"
git config --global user.email "(본인 이메일)"
```

- 아래의 명령어들로 확인

```terminal
git config --global user.name
git config --global user.email
```

> 🔹 기본 브랜치명 변경<br />
> 🔹 기존 master / slave 등의 용어로 주로 사용되었으나 변화하는 추세

```terminal
git config --global init.defaultBranch main
```

#### **프로젝트 생성 & Git 관리 시작**

적당한 위치에 원하는 이름으로 폴더를 생성하고 코드 에디터 오픈

해당 폴더에서 아래 명령어 입력

```terminal
git init
```
폴더에 숨김모드로 `.git` 폴더 생성 확인
- ⚠️ 이 폴더를 지우면 Git 관리내역이 삭제 (현 파일들은 유지)
- Mac에서 숨김 파일 보기 : `Command` + `shift` + `.`

❗ 모든 작업(파일 생성, 수정)마다 파일을 꼭 **저장**

```terminal
git status
> 현재 폴더의 상황을 Git의 관점으로 보여주는 명령어
```

#### **소스트리 사용해보기**

> 현존하는 저장소 추가
- 소스트리에 폴더를 드래그하거나, `로컬 저장소 추가`

> Git이 관리하는 저장소 새로 만들기
- `.git` 폴더 삭제 후 진행
- 소스트리에 폴더를 드래그하거나, `로컬 저장소 생성 (Create)`

---

### **5. Git에게 맡기지 않을 것들**

#### **Git의 관리에서 특정 파일/폴더를 배제해야 할 경우**

- 포함할 필요가 없을 때
  - 자동으로 생성 또는 다운로드되는 파일들 (빌드 결과물, 라이브러리)
- 포함하지 말아야 할 때
  - 보안상 민감한 정보를 담은 파일

🔹 `.gitignore` 파일을 사용해서 배제할 요소들을 지정

`.gitignore` 형식
- [https://git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore) 참조

| 형식                          | 설명                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| file.c                        | 모든 file.c                                                  |
| /file.c                       | 최상위 폴더의 file.c                                         |
| *.c                           | 모든 .c 확장자 파일                                          |
| !not_ignore_this.c            | .c 확장자지만 무시하지 않을 파일                             |
| logs                          | logs란 이름의 파일 또는 폴더와 그 내용들                     |
| logs/                         | logs란 이름의 폴더와 그 내용들                               |
| logs/debug.log<br /> logs/*.c | logs 폴더 바로 안의 debug.log와 .c 파일들                    |
| logs/**/debug.log             | logs 폴더 바로 안, 또는 그 안의 다른 폴더(들) 안의 debug.log |

---
