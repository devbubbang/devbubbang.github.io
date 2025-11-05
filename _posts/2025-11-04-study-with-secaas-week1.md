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

### **2. Architecture**

#### **보안 구조 정리**

- Spring Security는 서블릿 기반 웹 애플리케이션에서 요청을 가로채는 필터 체인을 중심으로 동작

#### **필터를 사용하는 이유**

- 모든 HTTP 요청은 서블릿 앞에서 여러 보안 필터를 통과해야 함
- 각각의 필터는 인증, 권한 확인, CSRF 보호 등 특정 역할을 수행
- 핵심 요소: `DelegatingFilterProxy` → `FilterChainProxy` → 여러 개의 `SecurityFilterChain`
- 이 세 가지가 Spring Security의 기본 뼈대

#### **필터**

> 필터

- Java EE에서 요청과 응답을 가로채어 전/후처리를 수행하는 기능
- 예를 들어 로그인 정보를 확인하거나, 요청을 수정하거나, 응답을 조기 종료하는 역할

**요청 흐름**

- 클라이언트 → 컨테이너가 `FilterChain`을 생성 → 등록된 필터들이 차례로 실행 → 최종적으로 서블릿(예: `DispatcherServlet`)이 호출됨
- 핵심: **요청을 여러 단계로 나누어 처리한다**

#### **DelegatingFilterProxy**

**역할**

- 서블릿 컨테이너는 오직 `javax.servlet.Filter`만 인식
- Spring Security의 필터들은 Spring이 관리하는 빈(bean)
- `DelegatingFilterProxy`는 컨테이너와 Spring Security 사이의 다리 역할
- 실제로 서블릿 컨테이너에 등록되는 필터는 딱 하나, 바로 이 `DelegatingFilterProxy`
- 이 프록시는 모든 요청을 Spring 컨텍스트 안에 정의된 `springSecurityFilterChain` bean으로 넘김
- 이런 구조 덕분에 보안 로직을 Spring bean으로 손쉽게 관리할 수 있고, 서블릿 컨테이너 설정을 건드릴 필요가 없음

#### **FilterChainProxy**

**역할**

- `DelegatingFilterProxy`가 넘겨준 요청을 받아 여러 개의 `SecurityFilterChain` 중 어떤 체인을 적용할지 결정하는 핵심 구성 요소
- URL 패턴에 따라 다른 보안 규칙을 적용할 수 있게 해 줌
- 예를 들어 `/public/**`는 누구나 접근 가능하게, `/admin/**`는 관리자만 접근 가능하게 설정 가능

#### **SecurityFilterChain**

**정의**

- 특정 URL 패턴에 대응하는 필터들의 목록
- 각 필터는 인증, 권한, CSRF, 예외 처리 등 하나의 책임을 가지고 순서대로 실행됨

**실전 예시**

- 코드에서 `HttpSecurity`를 사용해 `.authorizeHttpRequests()`나 `.httpBasic()` 등을 설정하면 이 `SecurityFilterChain`이 만들어짐

**알아두면 좋은 필터들**

- `SecurityContextPersistenceFilter`: 인증 정보를 세션에서 꺼내오거나 저장함
- `UsernamePasswordAuthenticationFilter`: 폼 로그인("/login") 요청을 처리함
- `BearerTokenAuthenticationFilter`: JWT/OAuth2 토큰을 처리함
- `AuthorizationFilter`: 권한을 검사해 접근 허용 여부를 결정함
- `ExceptionTranslationFilter`: 인증/권한 실패 시 401 또는 403 응답을 반환하거나 로그인 페이지로 리다이렉트함