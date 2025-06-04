---
title: Github에 IntelliJ 연동하기
date: 2025-06-04 20:59:00 +0900
categories: [Git, 기타]
tags: [Git]
toc: true
---

### **1단계: GitHub와 IntelliJ 연동 준비**

> **GitHub 측 설정**

- GitHub 계정 로그인
- **Personal Access Token**을 생성 (HTTPS 인증에 필요)

> **IntelliJ 설정**

- IntelliJ IDEA 실행 후 `Settings (Ctrl+Alt+S)` → `Version Control > GitHub`에서 계정을 추가합니다.
- 방법:
  - Add Account 클릭
  - Log In with GitHub
  - Token 입력 방식 선택 → 위에서 생성한 Personal Access Token을 사용

### **2단계: GitHub 저장소를 IntelliJ로 Clone**

1. GitHub 저장소 접속 (예: `https://github.com/사용자명/저장소명`)
2. `Code` 버튼 클릭 → `HTTPS` 주소 복사
3. IntelliJ IDEA에서:
   - File > New > Project from Version Control 클릭
   - Git 선택
   - URL 입력란에 복사한 주소 붙여넣기
   - 로컬 경로 설정 후 Clone 클릭
