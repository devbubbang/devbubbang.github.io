---
title: Spring Boot, Spring Security
date: 2025-11-04 22:41:00 +0900
categories: [개발공부, 인증]
tags: [spring, 인증, secaas]
toc: true
---

---

**SECaaS 연구소 지원을 받아 함께 진행하는 인증/인가 관련<br /> Spring Boot, Spring Security 프로젝트성 Study (251106 ~ )**

---

### **1. Hello Spring Security**

#### **개념의 구분**

- 인증 <sup>Authentication</sup> : 사용자가 누구인지 확인하는 절차. 로그인 화면을 통해 사용자 이름과 비밀번호를 입력받아 확인하는 과정
- 인가(권한부여) <sup>Authorization</sup> : 검증된 사용자가 어떤 자원에 접근할 수 있는지를 결정

#### **Spring Boot + Spring Security | 종속성 업데이트**

- Spring Boot는 Spring Security 관련 종속성을 집계하는 starter를 제공
  - `spinrg-boot-starter-security` 라는 starter를 수동으로 추가하여 사용할 수 있다.
    - 대표적으로 아래 두 가지 방식 사용
      - Maven: `pom.xml`
      - Gradle: `build.gradle`

**[Maven]**
```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-security</artifactId>
	</dependency>
</dependencies>
```

**[Gradle]**
```gradle
dependencies {
	implementation "org.springframework.boot:spring-boot-starter-security"
}
```

#### **401과 404 리턴**

- `401 Unauthorized`: 브라우저에서 아무 경로나 요청하나, 자격 증명 <sup>Credential</sup> 없이 엔드포인트에 도달하는 경우 응답
- `404 Not found`: 인증은 성공하나 존재하지 않는 경로일 경우 응답

#### **Runtime 동작**

#### **자동 보안 구성**

```java
@EnableWebSecurity
@Configuration
public class DefaultSecurityConfig {
    @Bean
    @ConditionalOnMissingBean(UserDetailsService.class)
    InMemoryUserDetailsManager inMemoryUserDetailsManager() {
        String generatedPassword = // ...;
        return new InMemoryUserDetailsManager(User.withUsername("user")
                .password(generatedPassword).roles("USER").build());
    }

    @Bean
    @ConditionalOnMissingBean(AuthenticationEventPublisher.class)
    DefaultAuthenticationEventPublisher defaultAuthenticationEventPublisher(ApplicationEventPublisher delegate) {
        return new DefaultAuthenticationEventPublisher(delegate);
    }
}
```

- `@EnableWebSecurity`: Spring Security의 필터 체인을 애플리케이션에 등록하여 활성화
- `AuthenticationEventPublisher`: 인증 관련 이벤트를 퍼블리시

#### **사용 사레 | UseCase**

> REST API

- JWT 또는 기타 Bearer 토큰

> Web Application, API Gateway, BFF

- OAuth 2.0 또는 OIDC 기반 로그인
- SAML 2.0 기반 로그인

> CAS

- 싱글 사인온(SSO) 구현을 위한 인증 서버

> 사용자 관리

- LDAP / AD / Spring Data / JDBC
- 암호 (password)

---
