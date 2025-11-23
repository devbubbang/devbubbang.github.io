---
title: Spring Boot JWT Tutorial
date: 2025-11-23 19:42:00 +0900
categories: [Development, Spring Boot]
---

### **JWT란?**

> 정의: RFC 7519 웹 표준으로 지정되어 있고, Json 객체를 사용해서 토큰 자체에 정보들을 저장하고 있는 Web Token

JWT Token은 `Header`, `Payload`, `Signature` 로 구분(구성)

- **Header:** Signature를 해싱하기 위한 알고리즘 정보들이 담겨 있음
- **Payload:** 서버와 클라이언트가 주고 받는, 시스템에서 실제로 사용될 정보에 대한 내용들이 담겨 있음
- **Signature:** 토큰의 유효성 검증을 위한 문자열

**JWT 장점**

- 중앙의 인증서버, 데이터 스토어에 대한 의존성 없음 ➡ 시스템 수평 확장에 유리
- Base64 URL Safe Encoding ➡ URL, Cookie, Header 모두 사용 가능 범용성 

**JWT 단점**

- Payload의 정보가 많아지면 네트워크 사용량 증가, 데이터 설계 시 고려 필요
- 토큰이 클라이언트에 저장 ➡ 서버에서 클라이언트의 토큰을 조작할 수 없음

---

### **프로젝트 구성**

> 업데이트: 2025-11-23

- **Project:** Gradle - Groovy
- **Language:** Java
- **Spring Boot:** 4.0.0
- **Project Metadata**
  - **Group:** com.example
  - **Artifact:** jwt-tutorial
  - **Name:** jwt-tutorial
  - **Description:** Spring Boot JWT Tutorial
  - **Package name:** com.example.tutorial
  - **Packaging:** Jar
  - **Configuration:** YAML
  - **Java:** 17
- **Dependencies**
  - Spring Web
  - Spring Security
  - Spring Data JPA
  - H2 Database
  - Lombok
  - Validation

> 📌 IntelliJ IDE 툴 사용 ➡ Lombok 을 사용하기 때문에 아래 설정을 진행해주어야 함.


```text
IntelliJ Preferences
⬇️
Annotation Processors
⬇️
Enable annotation processing CHECK
```

#### **테스트용 Rest API 생성**

- controller 패키지 생성
- HelloController 클래스 생성

```java
package com.example.jwt_tutorial.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class HelloController {
    @GetMapping("/hello")
    public ResponseEntity<String> hello() {
        return ResponseEntity.ok("Hello World");
    }
}

```

- Spring Boot Application 실행 (Run)
   - Console을 통해 정상 구동 확인
- Postman 활용 API 테스트 시 아래 현상 식별
   - GET /api/hello 요청
   - `401 Unauthorized`
   - Body 없음

📌 원인: 이는 Spring Security 모든 요청을 인증으로 두는 기본 보안 설정에 의해 발생하는 문제로 예상
- `Spring Boot 3.x/4.x` + **Security** 해당

---
