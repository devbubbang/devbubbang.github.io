---
title: docmost 개인 서버 설치
date: 2025-12-30 15:30:00 +0900
categories: [Personal, docmost]
tags: [docmost]
toc: true
---

> - Notion 대체 공용 Workspace 구축 <br/>
> - 로컬 Mac에 Docmost를 설치하고, ngrok를 통해 협업 Workspace 구성
{:.prompt-info}

## **사전 준비 사항**

### **Docker 설치**

- Docker Desktop for Mac 설치
- 설치 후 아래 명령 정상 동작 확인

```bash
docker --version
docker compose version
```

### **Docmost 설치**

#### **1. 작업 디렉토리 생성**
```bash
mkdir ~/docmost
cd ~/docmost
```

#### **2. docker-compose.yml 작성**

> 파일명 주의, 반드시 `docker-compose.yml` -> 오타가 나는 경우 Docker가 인식하지 못 함
{:.prompt-warning}

```yaml
services:
  docmost:
    image: docmost/docmost:latest
    container_name: docmost
    depends_on:
      - db
      - redis
    environment:
      APP_URL: http://localhost:3001
      DATABASE_URL: postgresql://docmost:dianfEkr79@db:5432/docmost?schema=public
      REDIS_URL: redis://redis:6379
    ports:
      - "3001:3000"
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    container_name: docmost-db
    environment:
      POSTGRES_DB: docmost
      POSTGRES_USER: docmost
      POSTGRES_PASSWORD: dianfEkr79
    volumes:
      - docmost_db_data:/var/lib/postgresql/data
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    container_name: docmost-redis
    restart: unless-stopped

volumes:
  docmost_db_data:
```

> **yml 파일에서 변경한 환경 변수 정리**

| 항목              | 설명                                     |
| ----------------- | ---------------------------------------- |
| APP_URL           | 사용자가 실제 접속하는 주소 기준         |
| DATABASE_URL      | DB 계정 / 비밀번호 반드시 DB 설정과 일치 |
| ports             | 외부 접속 포트는 3001로 설정             |
| POSTGRES_PASSWORD | 임의 지정 (DATABASE_URL과 동일해야 함)   |

#### **3. docmost 실행**

```bash
docker compose up -d
```

상태 확인:
```bash
docker compose ps
```

정상이라면
- docmost
- docmost-db
- docmost-redis 모두 `Started`

---

### **외부 접속을 위한 ngrok 설정**

#### **ngrok 설치**
```bash
brew install ngrok/ngrok/ngrok
```

버전 확인:
```bash
ngrok version
```

#### **ngrok 계정 연결**

1. [https://ngrok.com](https://ngrok.com) 가입
2. Dashboarad -> Authtoken 복사
```bash
ngrok config add-authtoken <토큰>
```

#### **Docmost 외부 공개**

```bash
ngrok http 3001
```

출력 예시:
```text
Forwarding https://xxxx.ngrok-free.dev -> http://localhost:3001
```
이 `https://xxx.ngrok-free.dev` 주소가 **외부 접속 주소**

### **APP_URL 변경 (중요)**

Docmost는 초대 링크 / 리다이렉트에 `APP_URL`을 사용함

#### **docker-compose.yml 수정**

```yaml
APP_URL: https://xxxx.ngrok-free.dev
```

#### **Docmost 재시작 (필수)**

```bash
docker compose down
docker compose up -d
```

### **Member 초대**

1. Docmost 접속 (ngrok 주소)
2. Space settings -> Members
3. Invite
4. 이메일 초대 또는 초대 링크 공유
   - 초대 후 `더보기 버튼 (...)` 눌러 링크 복사

### **주의 사항 정리**

#### **localhost는 외부에서 접속 절대 불가**

- 반드시 ngrok / Cloudflare Tunnel / 서버 필요

#### **APP_URL은 내부 포트 기준이 아님**

- 항상 **사용자가 접속하는 주소 기준**

#### **DATABASE_URL 비밀번호 불일치 주의**

- DB 설정과 다르면 Docmost는 떠도 내부 에러 발생

#### **ngrok 무료 플랜 주의**

- URL 재시작 시 변경될 수 있음
- Mac 종료 시 서버 종료

---
