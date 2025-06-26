# 🤖 RAG 기반 학교 공지사항 챗봇 서비스

# ✍️ 프로젝트 소개
## 프로젝트 배경
현재 금오공과대학교 내 공지사항은 학생, 교수, 교직원, 외부인 등에게 학사일정, 채용공고, 동아리 공고, 학술 대회 등 중요한 정보를 제공하는 데 반해, 다음과 같은 문제점을 가지고 있습니다.

- 정보 과부하: 매일 수많은 공지사항이 게시되어 중요한 정보를 놓치기 쉬움
- 검색 불편: 기존 웹사이트의 공지사항 검색은 키워드 기반으로만 작동하여 맥락 이해가 부족함
- 접근성 문제: 여러 부서와 학과의 공지사항이 분산되어 정작 필요한 정보를 빠르게 찾아내기 어려움
- 시간 소모: 장문의 공지사항에서 핵심 정보만 빠르게 파악하기 어려움 

## 문제점 해결
RAG(Retrieval-Augmented Generation) 기술을 활용하여 대화형 챗봇 서비스를 구축하여, 상기 문제점을 다음과 같이 해결하였습니다.

- 자연어 검색: 일상 언어로 질문하면 관련 공지사항을 정확하게 검색함
- 맥락 이해: 사용자의 질문 의도를 파악하여 적절한 정보 제공함
- 정보 통합: 여러 출처의 공지사항을 통합하여 한 번에 응답 가능함
- 요약 제공: 핵심 내용을 간결하게 요약하여 시간을 절약함
- 마크다운 지원: 챗봇 응답 시 -, **, # 과 같은 마크다운 형식으로 응답하는 경우 이를 지원하여 사용자 가독성을 향상시킴

# 🖊️ 아키텍처
![image](https://github.com/user-attachments/assets/df6c805a-5d0f-41af-8992-9d9e1421cadd)

# 📜 시작 가이드
## 요구사항
- Java 17 이상
- Node.js 18.x 이상
- MySQL 8.0 이상
- Python 3.9 이상 (Fast API 서버 사용)
- Docker Desktop 27.x 이상
- LM Studio 0.3.x 이상

## React & SpringBoot 환경 설정
### 1. 아래 주소에 있는 team-gongjibot-all repository clone

```
git clone https://github.com/team-gongjibot/team-gongjibot-all
```

### 2. application-db.yml, application-jwt.yml, application-mail.yml, application-oauth.yml, .env 파일 별도 생성 후 추가

위에 언급한 5개 파일은 보안 상 깃허브에 업로드하지 않아 포함되지 않은 파일들입니다.

아래에 표시된 `{}` 영역을 각자 설정에 맞게 작성해주세요.
#### application-db.yml
```
local-db:
  mysql:
    host: localhost
    port: 3308
    name: ragchat
    username: {MySQL 사용자 이름}
    password: {MySQL 사용자 비밀번호}

ragchat:
  cors-allow-origins: http://localhost:3000
```

#### application-jwt.yml
```
JWT_SECRET_KEY: {임의로 작성된 JWT 비밀키}
```

#### application-mail.yml
해당 파일은 회원가입 진행 시 인증번호 발송을 위해 Google SMTP 설정을 진행해야만 사용이 가능합니다.

자세한 설정 방법은 아래 링크를 참고해주세요.

<https://blogan99.tistory.com/101>

Google 계정에 SMTP 설정 후 application-mail.yml에 작성할 파일은 다음과 같습니다.
```
local-mail:
  host: smtp.gmail.com
  port: 587
  username: {SMTP 설정한 구글 이메일}
  password: {SMTP 설정 시 부여된 비밀번호}
```

#### application-oauth.yml
해당 파일은 OAuth 2.0 소셜 로그인 적용을 위해 작성한 파일입니다.

OAuth 2.0에 대한 자세한 내용과 설정법은 아래 링크를 참고해주세요.

<https://ksh-coding.tistory.com/63>

Google, Kakao, Naver 개발자 센터에서 API 이용 등록 후 application-oauth.yml에 작성할 파일은 다음과 같습니다.

```
spring:
  security:
    oauth2:
      client:
        registration:
          google:
            client-id: {클라이언트 아이디}
            client-secret: {클라이언트 비밀키}
            scope: profile, email
            redirect-uri: {등록한 리다이렉션 URI}

          naver:
            client-id: {클라이언트 아이디}
            client-secret: {클라이언트 비밀키}
            redirect-uri: {등록한 리다이렉션 URI}
            authorization-grant-type: authorization_code
            scope: name, email, profile_image
            client-name: Naver

          kakao:
            client-id: {클라이언트 아이디}
            client-secret: {클라이언트 비밀키}
            redirect-uri: {등록한 리다이렉션 URI}
            client-authentication-method: client_secret_post
            authorization-grant-type: authorization_code
            scope: profile_nickname, profile_image
            client-name: Kakao

        provider:
          naver:
            authorization-uri: https://nid.naver.com/oauth2.0/authorize
            token-uri: https://nid.naver.com/oauth2.0/token
            user-info-uri: https://openapi.naver.com/v1/nid/me
            user-name-attribute: response

          kakao:
            authorization-uri: https://kauth.kakao.com/oauth/authorize
            token-uri: https://kauth.kakao.com/oauth/token
            user-info-uri: https://kapi.kakao.com/v2/user/me
            user-name-attribute: id
```

#### .env
```
MYSQL_DATABASE: ragchat
MYSQL_USERNAME: {MySQL 사용자 이름}
MYSQL_ROOT_PASSWORD: {MySQL 사용자 비밀번호}
```

위에서 작성한 5개 파일을 아래 사진의 프로젝트 디렉터리 구조와 정확히 일치하도록 넣어주세요.

![image](https://github.com/user-attachments/assets/cc24baca-7655-4069-b983-16e4ee3d6b48)

### 3. 도커 실행
Intellij 터미널 창 또는 다른 터미널 창을 통해 `docker-compose.yml` 파일 위치로 이동 후 docker compose
```
docker compose up -d
```

### 4. SpringBoot & React 실행
- SpringBoot는 main 함수(`RagchatApplication.java`) 실행
- React는 `team-gongjibot-all\src\main\frontend` 경로로 이동 후 ``npm start`` 명령으로 실행

## FastAPI & AI 환경 설정
### 1. 아래 주소에 있는 team-gongjibot-chatbot repository clone
```
git clone https://github.com/team-gongjibot/team-gongjibot-chatbot
```

### 2. FastAPI 폴더로 이동 후, 필요한 라이브러리 설치
```
pip install uvicorn pyngrok fastapi transformers torch faiss-cpu openai
```

### 3. server_on.py 실행
```
python server_on.py
```

### 4. LLM 모델 설치
아래 링크에서 `qwen_final_model.gguf` 다운

<https://huggingface.co/jiho123/Qwen3_4B_Collage/tree/main>

### 5. LM Studio 실행
`C:\Users\{사용자}\.lmstudio\models` 폴더로 이동 후 `Qwen\Qwen3` 폴더를 생성하여 모델을 해당 경로에 추가

(`C:\Users\{사용자}\.lmstudio\models\Qwen\Qwen3`에 모델을 추가하면 됩니다.)

e.g. C:\Users\{사용자}\.lmstudio\models\Qwen3\Qwen3_4B_collage_Chat\qwen_final_model.gguf

# ⚙️ 기술 스택
| 분류 | 기술 스택 |
|---|---|
| 프론트엔드 | <img src="https://img.shields.io/badge/javascript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=white"/> <img src="https://img.shields.io/badge/css-663399?style=for-the-badge&logo=css&logoColor=white"/> <img src="https://img.shields.io/badge/react-61DAFB?style=for-the-badge&logo=react&logoColor=white"/> |
| 백엔드 | <img src="https://img.shields.io/badge/JAVA-007396?style=for-the-badge&logo=java&logoColor=white"> <img src="https://img.shields.io/badge/Spring Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white">  <img src="https://img.shields.io/badge/Spring Security-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white"> <img src="https://img.shields.io/badge/fastapi-009688?style=for-the-badge&logo=fastapi&logoColor=white"> |
| 인프라 & 데이터베이스 | <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white"/> <img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=MySQL&logoColor=white"/> |
| AI | <img src="https://img.shields.io/badge/huggingface-FFD21E?style=for-the-badge&logo=huggingface&logoColor=white"/> <img src="https://img.shields.io/badge/ollama-000000?style=for-the-badge&logo=ollama&logoColor=white"/> |

# 📌 주요 기능

## 홈 화면


## 회원가입
![Image](https://github.com/user-attachments/assets/c83787bd-d6ad-48a9-9d8c-7651dd4efa5b)

## 로그인

