---
title: 251126 | Authorization Code를 통한 Token 발급 Flow (디버깅)
date: 2025-12-01 23:06:00 +0900
categories: [Development, With SECaaS]
tags: [spring, 인증, 디버깅]
toc: true
---

---

## **IntelliJ 디버거 핵심 개념**

- **Breakpoint**
  - 디버거 모드로 실행시켰을 때 코드 실행이 멈추는 지점
  - 여러 개 설정 가능
  - 디버그를 할 때, 해당하는 Breakpoint가 없다면 단지 로직이 쭉 실행되고 멈춤
  - **걸린 Line의 코드가 실행되기 직전의 상태에서 멈춤**
- **Step Over**
  - 다음 줄로 넘어감
  - `method call` 안으로 들어가지 않음
- **Step Into**
  - 지금 대기하고 있는 `method call` 내부로 들어감
- **Resume Program**
  - 다음 breakpoint로 이동
- **Step Out**
- **Evaluate Expression**
  - 멈춘 지점에서 이런 저런 값을 계산해볼 수 있는 유용한 기능

---
