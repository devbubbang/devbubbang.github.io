---
title: 251120
date: 2025-11-19 00:31:00 +0900
categories: [Development, With SECaaS]
tags: [spring, 인증, secaas]
toc: true
---

---

### 개발 환경

- JDK 17 / Gradle
- Spring Boot 3.5.7
    - Spring Boot 최신 Release를 확인해보니 `v4.0.0-RC2` 확인 가능
    - https://github.com/spring-projects/spring-boot/releases
- Spring Security 6.5.6
    - Spring Security 최신 Release를 확인해보니 `7.0.0` 확인 가능
    - https://github.com/spring-projects/spring-security/releases

### Spring authorization server dependency 의존성 추가 (dependencies)

- 참고
  - https://docs.spring.io/spring-authorization-server/reference/getting-started.html?utm_source=chatgpt.com

```groovy
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.5.7'
	id 'io.spring.dependency-management' version '1.1.7'
}

group = 'io.security'
version = '0.0.1-SNAPSHOT'
description = 'springsecuritymin'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(17)
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-security'
	implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation "org.springframework.boot:spring-boot-starter-oauth2-authorization-server"
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.springframework.security:spring-security-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

tasks.named('test') {
	useJUnitPlatform()
}
```

적용 후 `External Libraries`에 `spring-security-oauth2-authorization-server` 확인

---

### SecurityConfig.java 구성

- In-Memory에 사용자, 클라이언트 설정 (하드코딩)
  - username -> `byungjunmin`
  - password -> `1q2w3e4r`
- OAuth 인증 서버는 로그인 사용자 뿐만 아닌, 토큰을 요청하는 앱(클라이언트) 정보도 필요
  - 하기 `registeredClientRepository` 영역
    - .clinetId
    - .clinetSecret
    - .redirectUri
    - **Q) 세 항목도 하드코딩이 필요한 것 같으나, 해당 필드에 입력 하는 정해진 표준 양식이 있는지?**

```java
package com.example.security.config;

import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;
import java.util.UUID;

import com.nimbusds.jose.jwk.JWKSet;
import com.nimbusds.jose.jwk.RSAKey;
import com.nimbusds.jose.jwk.source.ImmutableJWKSet;
import com.nimbusds.jose.jwk.source.JWKSource;
import com.nimbusds.jose.proc.SecurityContext;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.annotation.Order;
import org.springframework.http.MediaType;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.oauth2.core.AuthorizationGrantType;
import org.springframework.security.oauth2.core.ClientAuthenticationMethod;
import org.springframework.security.oauth2.core.oidc.OidcScopes;
import org.springframework.security.oauth2.jwt.JwtDecoder;
import org.springframework.security.oauth2.server.authorization.client.InMemoryRegisteredClientRepository;
import org.springframework.security.oauth2.server.authorization.client.RegisteredClient;
import org.springframework.security.oauth2.server.authorization.client.RegisteredClientRepository;
import org.springframework.security.oauth2.server.authorization.config.annotation.web.configuration.OAuth2AuthorizationServerConfiguration;
import org.springframework.security.oauth2.server.authorization.config.annotation.web.configurers.OAuth2AuthorizationServerConfigurer;
import org.springframework.security.oauth2.server.authorization.settings.AuthorizationServerSettings;
import org.springframework.security.oauth2.server.authorization.settings.ClientSettings;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.LoginUrlAuthenticationEntryPoint;
import org.springframework.security.web.util.matcher.MediaTypeRequestMatcher;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    @Order(1)
    public SecurityFilterChain authorizationServerSecurityFilterChain(HttpSecurity http)    //OAuth2 프로토콜 엔드포인트를 처리하는 보안 필터 체인
            throws Exception {
        OAuth2AuthorizationServerConfigurer authorizationServerConfigurer =
                OAuth2AuthorizationServerConfigurer.authorizationServer();

        http
                .securityMatcher(authorizationServerConfigurer.getEndpointsMatcher())
                .with(authorizationServerConfigurer, (authorizationServer) ->
                        authorizationServer
                                .oidc(Customizer.withDefaults())	// Enable OpenID Connect 1.0
                )
                .authorizeHttpRequests((authorize) ->
                        authorize
                                .anyRequest().authenticated()
                )
                // Redirect to the login page when not authenticated from the
                // authorization endpoint
                .exceptionHandling((exceptions) -> exceptions
                        .defaultAuthenticationEntryPointFor(
                                new LoginUrlAuthenticationEntryPoint("/login"),
                                new MediaTypeRequestMatcher(MediaType.TEXT_HTML)
                        )
                );

        return http.build();
    }

    @Bean
    @Order(2)
    public SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http)        //인증 서버 자체의 보안을 담당하는 기본 필터 체인
            throws Exception {
        http
                .authorizeHttpRequests((authorize) -> authorize
                        .anyRequest().authenticated()
                )
                // Form login handles the redirect to the login page from the
                // authorization server filter chain
                .formLogin(Customizer.withDefaults());

        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService() {        //로그인 화면에서 실제로 로그인할 사용자(User)의 정보를 조회하는 서비스
        UserDetails userDetails = User.withDefaultPasswordEncoder()
                .username("byungjunmin")
                .password("1q2w3e4r")
                .roles("USER")
                .build();

        return new InMemoryUserDetailsManager(userDetails);
    }

    @Bean
    public RegisteredClientRepository registeredClientRepository() {        //클라이언트 정보를 관리하는 저장소
        RegisteredClient oidcClient = RegisteredClient.withId(UUID.randomUUID().toString())
                .clientId("oidc-client")
                .clientSecret("{noop}secret")
                .clientAuthenticationMethod(ClientAuthenticationMethod.CLIENT_SECRET_BASIC)
                .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
                .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
                .redirectUri("http://127.0.0.1:8080/login/oauth2/code/oidc-client")
                .postLogoutRedirectUri("http://127.0.0.1:8080/")
                .scope(OidcScopes.OPENID)
                .scope(OidcScopes.PROFILE)
                .clientSettings(ClientSettings.builder().requireAuthorizationConsent(true).build())
                .build();

        return new InMemoryRegisteredClientRepository(oidcClient);
    }

    @Bean
    public JWKSource<SecurityContext> jwkSource() {     //액세스 토큰(JWT) 서명에 사용할 키 세트(JWK Set)를 제공 , RSA 키 쌍을 생성하여 토큰의 무결성 보장
        KeyPair keyPair = generateRsaKey();
        RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();
        RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();
        RSAKey rsaKey = new RSAKey.Builder(publicKey)
                .privateKey(privateKey)
                .keyID(UUID.randomUUID().toString())
                .build();
        JWKSet jwkSet = new JWKSet(rsaKey);
        return new ImmutableJWKSet<>(jwkSet);
    }

    private static KeyPair generateRsaKey() {
        KeyPair keyPair;
        try {
            KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");
            keyPairGenerator.initialize(2048);
            keyPair = keyPairGenerator.generateKeyPair();
        }
        catch (Exception ex) {
            throw new IllegalStateException(ex);
        }
        return keyPair;
    }

    @Bean
    public JwtDecoder jwtDecoder(JWKSource<SecurityContext> jwkSource) {
        return OAuth2AuthorizationServerConfiguration.jwtDecoder(jwkSource);
    }

    @Bean
    public AuthorizationServerSettings authorizationServerSettings() {      //인증 서버의 전반적인 설정을 담당
        return AuthorizationServerSettings.builder().build();
    }

}
```

---

### Authorization code를 통한 토큰 발행

- 필요 항목
  - 클라이언트 설정 `registeredClientRepository`
  - 키 설정 `JWKSource`
  - 기본 값 그대로 사용
- 서버 실행 후, 아래 양식으로 브라우저에 도메인 입력

```text
http://localhost:9000/oauth2/authorize?response_type=code&client_id={xxx..}&scope={xxx..}&redirect_uri={xxx..}
```

![img1](/assets/img/authorization_token_request.png)
- profile 체크
- Submit Consent 클릭
![img2](/assets/img/Authorization_token_response.png)
- 도메인의 `code=` 뒤에 처음 보는 긴 문자열 생성 확인
  - 해당 문자열이 토큰이 정상적으로 발행되었다는 뜻

---

### 추가 이해 내용

- 잘못된 이해 포인트
  - 도메인 내 Redirect URL의 `code=...` 부분 생성된 문자열을 **"Token이 발행되었다"** 로 인식
    - 그러나 이는 단순한 **Authorization Code**가 발급된 것
  - **실제 Token은 발급된 code 값을 이용하여 요청한다.**

### 흐름 정리

**요청 URL**
```bash
http://localhost:9000/oauth2/authorize?response_type=code&client_id=oidc-client&scope=openid%20profile&redirect_uri=http://127.0.0.1:8080/login/oauth2/code/oidc-client
```

1. 브라우저 접속
2. 로그인 Form에서 로그인 진행
   - 하드코딩했던 ID / PW (byungjunmin / 1q2w3e4r)
3. Consent 화면에서 profile 체크 후 submit
4. Redirect Uri로 이동하며 code 전달
5. 위에서 발행된 code 값을 이용해 Access Token 요청
   - 문자열 복사하여 하위 `발급받은_code값` 위치에 붙여넣기

**curl 명령어 방식 이용**

```bash
curl -X POST "http://localhost:9000/oauth2/token" \         //인증 서버의 토큰 발급 엔드포인트
 -H "Content-Type: application/x-www-form-urlencoded" \     //헤더 --> 전송하는 데이터가 어떤 형식임을 명시 
 -u "oidc-client:secret" \                                
 -d "grant_type=authorization_code" \                        //데이터 --> 현재 요청이 authorization code를 통한 토큰 교환임을 서버에 알림
 -d "code=발급받은_code값" \                                   //데이터 --> 이전 단계에서 발행된 code 값
 -d "redirect_uri=http://127.0.0.1:8080/login/oauth2/code/oidc-client"      //데이터 --> 보안 검증용 필드
```

**성공 응답 확인**

![](../assets/img/access_token_check.png)

```json
{"access_token":"eyJraWQiOiJkYTdjMTI2Yi04NDliLTQ4YjYtOTE1NC1kMjdiM2ZmOWM3MGUiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJieXVuZ2p1bm1pbiIsImF1ZCI6Im9pZGMtY2xpZW50IiwibmJmIjoxNzY0MDc1OTM1LCJzY29wZSI6WyJvcGVuaWQiLCJwcm9maWxlIl0sImlzcyI6Imh0dHA6Ly8xMjcuMC4wLjE6OTAwMCIsImV4cCI6MTc2NDA3NjIzNSwiaWF0IjoxNzY0MDc1OTM1LCJqdGkiOiI5ZTVjNmJmNC0xZDFkLTQyNjEtOGQ2Mi05ODhhNWNkZTFhMzkifQ.ug4AkPccRdZXMFtrogz366m1c67vIq-uHJWbI_0OcAgloThAKbpU_FMykK63W50c--oqCeqdWbyl8sTIJLwCl-dCXx-b250OTQffCTgCSfO-MXQVAYeUinv4zB7mAo63TWOWObGm_uPTiKpa5ogfdjjIVQMGMF0Zlz9XJCsKVftXJ6js3sUfBZ7M5nbCmkYy0jkq7PQ6g68-2bPMc_kLp-BJyxcRrEIQOk3FIVTBJ4j1QReVVVcUIwvJ4X1ui7TouefVr2FSDP6JTLZElUMYqv4sbWWWet4WdWSCRpnkLu3IlP_GlRQpbiZS-enOKRluwoy041XnOkMZ1nQIEPtWzg", 
  "refresh_token":"ObAh2on3ICwvklGA7BwyvYA-5VhXzhrPTuFOMlb3Gnr788kpxTOCVj0vGcOQvc6fu9N8PC8_f3HCrxcp4Ky0UMDB3HzF330HF1vDNnEfsc-7K9RggVXaipLJYXq-KeHc",
  "scope":"openid profile",
  "id_token":"eyJraWQiOiJkYTdjMTI2Yi04NDliLTQ4YjYtOTE1NC1kMjdiM2ZmOWM3MGUiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJieXVuZ2p1bm1pbiIsImF1ZCI6Im9pZGMtY2xpZW50IiwiYXpwIjoib2lkYy1jbGllbnQiLCJhdXRoX3RpbWUiOjE3NjQwNzU5MTYsImlzcyI6Imh0dHA6Ly8xMjcuMC4wLjE6OTAwMCIsImV4cCI6MTc2NDA3NzczNSwiaWF0IjoxNzY0MDc1OTM1LCJqdGkiOiI5NmU2NjZmMy1hM2NmLTQxMjQtYTZmYy05MmU0MjUyY2I4YjAiLCJzaWQiOiJmLUJEcTZma3B4T2ppaVVGcjVDUlU3X3JuRlUyR0w2cmY1ajNMWTNkQl9nIn0.FiMjnZB9b96lexSoS6IoY38OBZqahOaja2IOtd-8JAs-XQVWFqgv1yFuO1AixUN1-naLHzkrQy--NmniAFsAjgobNGI2mVVIxH32BmZO3zyy1HYzzJFh_mwRtyNkVF4CKuK10JWgWcjqpDiTcqtvBDDLiPmexmjM5GTFnLM6VrDqD-P10uFvvPBpl5tkEfamu-UMZ9M23RqqzY0eyUwOck8I3xKkrlfw1YkTLmiwO4Yde_DBEYrS52ns8UORjQd1Qk824ReJYNAM7UO707-fi2-a2y3qZeyVJ5lP-DMUSzw-bpfiml_6BxFy7LiUvNxHDuCf6G5JvRoBYf9tbkMZQA",
  "token_type":"Bearer",
  "expires_in":299}
```

---
