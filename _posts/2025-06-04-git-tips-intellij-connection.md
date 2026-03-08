---
title: Github에 IntelliJ 연동하기
date: 2025-06-04 20:59:00 +0900
categories: [Git, etc]
tags: [Git]
toc: true
---

### **✅ 1단계: GitHub와 IntelliJ 연동 준비**

> **GitHub 측 설정**

- GitHub 계정 로그인
- **Personal Access Token**을 생성 (HTTPS 인증에 필요)

> **IntelliJ 설정**

- IntelliJ IDEA 실행 후 `Settings (Ctrl+Alt+S)` → `Version Control > GitHub`에서 계정을 추가합니다.
- 방법:
  - Add Account 클릭
  - Log In with GitHub
  - Token 입력 방식 선택 → 위에서 생성한 Personal Access Token을 사용

### **✅ 2단계: GitHub 저장소를 IntelliJ로 Clone**

1. GitHub 저장소 접속 (예: `https://github.com/사용자명/저장소명`)
2. `Code` 버튼 클릭 → `HTTPS` 주소 복사
3. IntelliJ IDEA에서:
   - File > New > Project from Version Control 클릭
   - Git 선택
   - URL 입력란에 복사한 주소 붙여넣기
   - 로컬 경로 설정 후 Clone 클릭

---

### **💡 진행 중 알게된 지식**

#### **1. Access Token 생성 시 옵션 차이**

🔹 Generate new token vs Generate new token (classic)

| 항목                         | 설명                                         |
| ---------------------------- | -------------------------------------------- |
| Generate new token           | Fine-grained PAT, 세분화된 권한, GitHub 권장 |
| Generate new token (classic) | 기존 방식, IntelliJ 등 기존 툴과 호환성 우수 |

> ✅ 개인 개발용이라면 **classic 방식**도 실용적인 선택

#### **2. Access Token 만료일 권장**

- **권장 만료 기간:** `30일 ~ 60일`
- 너무 짧으면 불편, 너무 길면 보안 위험
- GitHub는 만료일 설정을 강하게 권장
- `No expiration` 비추천

#### **3. Repository Access 옵션**

| 옵션                     | 설명               | 권장 여부 |
| ------------------------ | ------------------ | --------- |
| All repositories         | 모든 저장소 접근   | ❌ 비권장  |
| Only select repositories | 특정 저장소만 접근 | ✅ 권장    |

> ⚠️ 저장소 접근 범위는 **토큰 생성 후 변경 불가** → 새로 생성해야 함

#### **4. Fine-grained Token 권한(Permissions) 설정 권장**

| 권한 항목              | 권장 설정        |
| ---------------------- | ---------------- |
| `Contents`             | ✅ Read and write |
| `Metadata`             | ✅ Read-only      |
| 그 외 (PR, Actions 등) | ❌ None           |

> 위 두 항목만 설정해도 IntelliJ에서 코드 clone/push/pull/commit 가능

#### **5. clone 후 Java 코드 작성**

- 프로젝트 clone 직후 `src` 폴더 및 `.java` 없음 → 직접 생성 필요
- `src > 패키지 > 클래스` 순으로 작성
- JDK 설정: `openjdk-23` 확인됨 → 정상

#### **6. IntelliJ에서 패키지/클래스가 디렉토리/파일처럼 보이는 문제**

- 원인
  - `src` 폴더가 **Source Root로 등록되지 않음**
- 해결 방법

```plaintext
src 폴더 우클릭 → Mark Directory as > Sources Root
```
