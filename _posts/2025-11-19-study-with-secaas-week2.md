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
