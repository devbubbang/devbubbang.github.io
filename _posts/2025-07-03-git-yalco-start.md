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
```text
git --version
```
```text
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

#### **Windows - VS COde의 기본 터미널을 Git Bash로 설정**

> Git 뿐 아니라 다른 프로그래밍 작업에 있어서도 유용

- VS Code에서 `Ctrl` + `Shift` + `P`
- `Select Default Profiel` 검색하여 선택
- **Git Bash** 선택
- 터미널에서 `+`로 새 창을 열어서 기본으로 Git Bash가 설정된 것 확인
- ➕ Git Bash를 **C 드라이브**에 설치해야 이 설정이 가능
  - Git 등 프로그래밍 관련 소프트웨어들은 VS Code 뿐 아니라 기타 연계할 프로그램에서도 C 드라이브에 설치된 것으로 간주되므로, C 드라이브에 설치해야 위 문제를 비롯한 어려움을 겪지 않을 수 있음
- ➕ Material 테마
  - VS Code에서 강의 영상의 화면과 같이 파일 탐색기 (파일 아이콘들) 부분이 보이게 하려면 왼쪽의 `확장` 탭 (블럭 모양 아이콘)에서 **Material Icon Theme** 테마를 검색하여 설치

#### **Mac**

🔹 작업 가능한 Mac 장비가 없기 때문에 현재 영상 시청 X, 이 후 Mac OS 사용 시 컨텐츠 추가

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
