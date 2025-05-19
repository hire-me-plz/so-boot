# So-Boot API

***

## 목차

- [소개](#소개)
- [API Reference](#api-reference)
- [1. 인증 관련 API](#1-인증-관련-api)
- [2. 회원 관련 API](#2-회원-관련-api)
- [3. 질문 관련 API](#3-질문-관련-api)
- [4. 모의 면접 결과 관련 API](#4-모의-면접-결과-관련-api)
- [5. 기업 관련 API](#5-기업-관련-api)
- [오류 코드](#오류-코드)
- [개발 환경](#개발-환경)

## 소개

So-Boot API는 면접 연습 및 피드백을 제공하는 서비스의 백엔드 API입니다. 사용자가 면접 질문에 대한 답변을 등록하고, 해당 답변에 대한 피드백과 점수를 받을 수 있는 기능을 제공합니다.

### API URL

API URL은 다음과 같습니다.

| 환경  | URL                           |
| --- | ----------------------------- |
| 개발  | http://localhost:8080/api     |
| 배포  | https://so-boot.ssafy.com/api |

### 응답 형식

모든 API 응답은 다음과 같은 공통 형식을 따릅니다:

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": { /* 응답 데이터 */ }
}
```

### 오류 응답 형식

오류가 발생한 경우 응답은 다음과 같은 형식을 따릅니다:

```json
{
    "isSuccess": false,
    "code": 400,
    "message": "입력값을 확인해주세요.",
    "result": []
}
```

## API Reference

v1.0 버전입니다.

## 1. 인증 관련 API

### 1.1 회원가입

신규 회원을 등록하는 API입니다.

#### 요청 URL

| 메서드  | URL          |
| ---- | ------------ |
| POST | /auth/signup |

#### 요청 Body

| 필드       | 타입     | 필수 여부    | 설명                |
| -------- | ------ | -------- | ----------------- |
| loginId  | String | required | 로그인 ID            |
| password | String | required | 비밀번호              |
| nickname | String | required | 사용자 닉네임           |
| birth    | Date   | required | 생년월일 (yyyy-MM-dd) |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "회원가입 완료",
  "result": []
}
```

### 1.2 로그인

회원 로그인을 처리하는 API입니다.

#### 요청 URL

| 메서드  | URL         |
| ---- | ----------- |
| POST | /auth/login |

#### 요청 Body

| 필드       | 타입     | 필수 여부    | 설명       |
| -------- | ------ | -------- | -------- |
| id       | String | required | 로그인 ID   |
| password | String | required | 로그인 비밀번호 |

#### 요청 예시

```json
{
  "id": "aaa123",
  "password": "abc123"
}
```

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "로그인 성공",
  "result": {
    "memberId": 2,
    "nickName": "닉네임",
    "profilePath": "이미지 링크",
    "birth": "1999-09-29"
  }
}
```

### 1.3 로그아웃

현재 로그인된 사용자의 액세스 토큰을 무효화하여 로그아웃하는 API입니다.

#### 요청 URL

| 메서드  | URL          |
| ---- | ------------ |
| POST | /auth/logout |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명            |
| ------------- | ------ | -------- | ------------- |
| Authorization | String | required | Bearer 액세스 토큰 |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": []
}
```

### 1.4 리프레시 토큰 검증 및 액세스 토큰 재발급

리프레시 토큰을 검증하고 유효할 경우 새로운 액세스 토큰을 재발급하는 API입니다.

#### 요청 URL

| 메서드  | URL           |
| ---- | ------------- |
| POST | /auth/reissue |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                              |
| ------------- | ------ | -------- | ------------------------------- |
| Authorization | String | required | Bearer 리프레시 토큰 (AccessToken 아님) |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": []
}
```

### 1.5 SSAFY 로그인 URL 요청

SSAFY SSO 로그인을 위한 인증 URL을 요청하는 API입니다.

#### 요청 URL

| 메서드 | URL             |
| --- | --------------- |
| GET | /auth/ssafy/url |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": "https://project.ssafy.com/oauth/sso-check?client_id=8ca48d75-aad6-49f1-a71f-66807d2503c4&redirect_uri=http://localhost:5173/auth/ssafy/callback&response_type=code"
}
```

### 1.6 SSAFY 로그인 콜백

SSAFY OAuth 인증 서버로부터 전달된 인가 코드를 받아 토큰을 발급하고 로그인을 완료하는 API입니다.

#### 요청 URL

| 메서드 | URL                  |
| --- | -------------------- |
| GET | /auth/ssafy/callback |

#### 요청 Query Parameter

| 필드   | 타입     | 필수 여부    | 설명                      |
| ---- | ------ | -------- | ----------------------- |
| code | String | required | SSAFY 인증 서버로부터 받은 인가 코드 |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "로그인 성공",
  "result": {
    "memberId": 2,
    "nickName": "닉네임",
    "profilePath": "이미지 링크",
    "birth": "1999-09-29"
  }
}
```

## 2. 회원 관련 API

### 2.1 내 정보 조회

회원정보를 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL      |
| --- | -------- |
| GET | /members |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "내 정보 조회 완료",
    "result": {
        "nickname": "dasd213",
        "id": "ssafy123",
        "birth": "1999-09-29",
        "profilePath": "s3이미지 링크"
    }
}
```

#### 실패 응답

##### 회원 미존재 오류

```json
{
  "isSuccess": false,
  "code": 1000,
  "message": "회원을 찾을 수 없습니다",
  "result": null
}
```

### 2.2 내 정보 수정

회원정보를 수정하는 API입니다.

#### 요청 URL

| 메서드   | URL      |
| ----- | -------- |
| PATCH | /members |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 요청 Body (multipart/form-data)

| 필드          | 타입        | 필수 여부    | 설명         |
| ----------- | --------- | -------- | ---------- |
| nickname    | String    | required | 변경할닉네임     |
| birth       | LocalDate | required | 변경할 생년월일   |
| profilePath | MultiPart | optional | 변경할 프로필이미지 |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "내 정보 수정 완료",
  "result": [
    "nickname": "새로운닉네임",
    "birth": "새로운 생년월일",
    "profilePath": "이미지 파일" 
  ]
}
```

#### 실패 응답

##### 닉네임 중복 오류

```json
{
  "isSuccess": false,
  "code": 1003,
  "message": "닉네임이 이미 존재합니다.",
  "result": null
}
```

##### 프로필 이미지 업로드 실패 오류

```json
{
  "isSuccess": false,
  "code": 1002,
  "message": "프로필 이미지 업로드에 실패하였습니다.",
  "result": null
}
```

### 2.3 내 이력서 업로드

자신의 이력서를 pdf파일로 업로드하는 API입니다.

#### 요청 URL

| 메서드  | URL              |
| ---- | ---------------- |
| POST | /members/resumes |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 요청 Body (multipart/form-data)

| 필드          | 타입        | 필수 여부    | 설명              |
| ----------- | --------- | -------- | --------------- |
| file        | Multipart | required | 업로드할 이력서 pdf 파일 |
| companyName | String    | required | 지원할 회사명         |
| position    | String    | required | 지원할 직무명         |

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "이력서 업로드 완료",
    "result": []
}
```

#### 실패 응답

##### 이력서 업로드 실패

```json
{
  "isSuccess": false,
  "code": 1003,
  "message": "이력서 업로드에 실패하였습니다.",
  "result": null
}
```

### 2.4 내 이력서 목록 조회

자신이 업로드한 이력서 목록을 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL                      |
| --- | ------------------------ |
| GET | /members/resumes?limit=? |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 요청 Param

| 필드    | 타입  | 필수 여부    | 설명               |
| ----- | --- | -------- | ---------------- |
| limit | int | optional | 한 번에 가져올 최대 항목 수 |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "이력서 목록 조회 완료",
  "result": [
    {
      "resumeId": 1,
      "companyName": "넥슨코리아",
      "position": "프론트엔드"
    },
    {
      "resumeId": 2,
      "companyName": "카카오",
      "position": "백엔드 개발자"
    },
    {
      "resumeId": 3,
      "companyName": "네이버",
      "position": "서버 개발자"
    },
    {
      "resumeId": 4,
      "companyName": "라인",
      "position": "풀스택 개발자"
    }
  ]
}
```

#### 실패 응답

##### 이력서 목록 조회 실패

```json
{
  "isSuccess": false,
  "code": 1001,
  "message": "이력서를 찾을 수 없습니다.",
  "result": null
}
```

### 2.5 내 이력서 상세 조회

특정 이력서의 상세 정보를 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL                         |
| --- | --------------------------- |
| GET | /members/resumes/{resumeId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### Path Variable

| 필드       | 타입   | 필수 여부    | 설명          |
| -------- | ---- | -------- | ----------- |
| resumeId | Long | required | 조회할 이력서의 ID |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": {
    "resumeId": 1,
    "companyName": "넥슨코리아",
    "position": "프론트엔드",
    "fileName": "2024_이력서_김개발.pdf",
    "filePath": "/storage/resumes/user123/2024_이력서_김개발.pdf",
    "fileSize": "2.4MB"
  }
}
```

#### 실패 응답

##### 이력서 조회 실패

```json
{
  "isSuccess": false,
  "code": 1001,
  "message": "이력서를 찾을 수 없습니다.",
  "result": null
}
```

### 2.6 내 이력서 삭제

자신이 업로드한 이력서를 삭제하는 API입니다.

#### 요청 URL

| 메서드    | URL                         |
| ------ | --------------------------- |
| DELETE | /members/resumes/{resumeId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### Path Variable

| 필드       | 타입   | 필수 여부    | 설명         |
| -------- | ---- | -------- | ---------- |
| resumeId | Long | required | 삭제할 이력서 ID |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": []
}
```

#### 실패 응답

##### 이력서 조회 실패

```json
{
  "isSuccess": false,
  "code": 1001,
  "message": "이력서를 찾을 수 없습니다.",
  "result": null
}
```

### 2.7 스트릭 조회

회원의 이력서/면접 관련 스트릭 정보를 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL             |
| --- | --------------- |
| GET | /members/streak |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "스트릭 조회 완료",
  "result": [
    {
      "streakType": "RESUME",
      "streakCount": "4",
      "date": "2024-02-01"
    },
    {
      "streakType": "INTERVIEW",
      "streakCount": "2",
      "date": "2023-02-01"
    },
    {
      "streakType": "INTERVIEW",
      "streakCount": "5",
      "date": "2025-02-01"
    },
    {
      "streakType": "RESUME",
      "streakCount": "3",
      "date": "2025-02-01"
    }
  ]
}
```

#### 실패 응답

```json
{
  "isSuccess": false,
  "code": 1000,
  "message": "회원을 찾을 수 없습니다",
  "result": null
}
```

### 2.8 면접 리허설 결과 목록 조회

회원의 면접 리허설 결과 목록을 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL                           |
| --- | ----------------------------- |
| GET | /members/rehearsals?limit={n} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 요청 Param

| 필드    | 타입  | 필수 여부    | 설명              |
| ----- | --- | -------- | --------------- |
| limit | int | optional | 조회할 최대 리허설 결과 수 |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": [
    {
      "rehearsalResultId": 1,
      "title": "자바 백엔드 개발자 면접 연습",
      "totalScore": 85,
      "createdAt": "2025-05-08T14:30:45"
    },
    {
      "rehearsalResultId": 2,
      "title": "대기업 기술 면접 연습",
      "totalScore": 92,
      "createdAt": "2025-05-07T10:15:22"
    }
  ]
}
```

#### 실패 응답

```json
{
  "isSuccess": false,
  "code": 4000,
  "message": "리허설 결과를 찾을 수 없습니다.",
  "result": null
}
```

### 2.9 면접 리허설 결과 상세 조회

특정 리허설 결과의 상세 내용을 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL                                     |
| --- | --------------------------------------- |
| GET | /members/rehearsals/{rehearsalResultId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### Path Variable

| 필드                | 타입   | 필수 여부    | 설명            |
| ----------------- | ---- | -------- | ------------- |
| rehearsalResultId | Long | required | 조회할 리허설 결과 ID |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": {
    "rehearsalResultId": 1,
    "title": "자바 백엔드 개발자 면접 연습",
    "totalScore": 85,
    "createdAt": "2025-05-08T14:30:45",
    "questionFeedbacks": [
      {
        "questionFeedbackId": 1,
        "question": "자바의 GC(Garbage Collection)에 대해 설명해주세요.",
        "answer": "자바의 GC는 더 이상 사용되지 않는 객체의 메모리를 자동으로 해제하는 기능입니다...",
        "modelAnswer": "가비지 컬렉션(GC)은 JVM의 힙 메모리 관리 시스템으로...",
        "feedbackText": "GC의 기본 개념은 잘 설명하셨으나, 실제 작동 방식과 종류에 대한 설명이 부족했습니다.",
        "contentRelevanceScore": 80,
        "expertiseScore": 70,
        "jobFitScore": 90,
        "questionScore": 80
      },
      {
        "questionFeedbackId": 2,
        "question": "Spring Framework의 DI(Dependency Injection)란 무엇인가요?",
        "answer": "DI는 객체 간의 의존성을 외부에서 주입해주는 디자인 패턴입니다...",
        "modelAnswer": "DI(의존성 주입)는 Spring Framework의 핵심 기능으로...",
        "feedbackText": "DI의 개념과 장점을 명확하게 설명했습니다. 실제 사용 예시도 적절했습니다.",
        "contentRelevanceScore": 95,
        "expertiseScore": 90,
        "jobFitScore": 95,
        "questionScore": 93
      }
    ]
  }
}
```

#### 실패 응답

```json
{
  "isSuccess": false,
  "code": 4001,
  "message": "리허설 결과에 접근 권한이 없습니다.",
  "result": null
}
```

## 3. 질문 관련 API

### 3.1 질문 목록 조회

질문 목록을 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL            |
| --- | -------------- |
| GET | /api/questions |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |

#### 요청 Param

| 필드       | 타입     | 필수 여부       | 설명                                                                                                                |
| -------- | ------ | ----------- | ----------------------------------------------------------------------------------------------------------------- |
| type     | String | required    | 질문 유형 (COMMON, CUSTOM, C_LETTER)                                                                                  |
| subType  | String | optional    | 질문 세부 유형 (data-structure, operating-system, computer-architecture, database, network)<br/>※ `type`이 COMMON일 때만 적용 |
| resumeId | Long   | optional    | 자소서 기반 분류 ID (예: 1, 2, 3...)<br/>※ `type`이 C_LETTER일 때 사용                                                         |
| memberId | Long   | conditional | 사용자 ID<br/>※ `type`이 CUSTOM 또는 C_LETTER일 때 필수                                                                     |

#### 요청 예시

```
GET /api/questions?type=COMMON&subType=data-structure
GET /api/questions?type=CUSTOM&memberId=100
GET /api/questions?type=C_LETTER&resumeId=1&memberId=100
```

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "요청에 성공하였습니다.",
  "result": [
    {
      "questionId": 101,
      "question": "컴퓨터의 구성 요소를 설명해주세요."
    },
    {
      "questionId": 102,
      "question": "TCP와 UDP의 차이점을 설명해주세요."
    }
  ]
}
```

#### 실패 응답

##### 질문 유형이 유효하지 않은 경우

```json
{
  "isSuccess": false,
  "code": 3001,
  "message": "유효하지 않은 질문 유형입니다.",
  "result": []
}
```

##### 회원을 찾을 수 없는 경우

```json
{
  "isSuccess": false,
  "code": 1000,
  "message": "회원을 찾을 수 없습니다.",
  "result": []
}
```

### 3.2 특정 질문 상세 조회

특정 질문의 상세 정보를 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL                     |
| --- | ----------------------- |
| GET | /api/question/{questionId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |

#### Path Variable

| 필드         | 타입   | 필수 여부    | 설명     |
| ---------- | ---- | -------- | ------ |
| questionId | Long | required | 질문 ID  |

#### 요청 Param

| 필드       | 타입   | 필수 여부    | 설명     |
| -------- | ---- | -------- | ------ |
| memberId | Long | optional | 회원 ID  |

#### 요청 예시

```
GET /api/question/101
```

#### 성공 응답

```json
{
   "isSuccess": true,
   "code": 200,
   "message": "요청에 성공하였습니다.",
   "result": {
       "questionId": 1,
       "content": "시간 복잡도와 공간 복잡도에 대해 설명해주세요.",
       "questionType": "COMMON",
       "createdAt": "2025-04-24T11:09:55",
       "answers": [
           {
               "answerId": 1,
               "content": "시간 복잡도는 알고리즘이 실행되는 데 걸리는 시간을 분석한 것으로...",
               "questionId": 1,
               "createdAt": "2025-04-24T11:09:55"
           }
       ]
   }
}
```

#### 실패 응답

##### 질문을 찾을 수 없는 경우

```json
{
  "isSuccess": false,
  "code": 3000,
  "message": "질문을 찾을 수 없습니다.",
  "result": []
}
```

##### 인증되지 않은 요청

```json
{
  "isSuccess": false,
  "code": 401,
  "message": "인증이 필요합니다.",
  "result": []
}
```

### 3.3 커스텀 질문 생성

사용자 정의 질문을 생성하는 API입니다.

#### 요청 URL

| 메서드  | URL                  |
| ---- | -------------------- |
| POST | /api/questions/custom |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |
| Content-Type  | String | required | application/json |

#### 요청 Body

```json
{
  "customQuestion": "입사 후 이루고 싶은 목표는 무엇인가요?"
}
```

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": []
}
```

#### 실패 응답

##### 인증되지 않은 요청

```json
{
  "isSuccess": false,
  "code": 401,
  "message": "인증이 필요합니다.",
  "result": []
}
```

##### 잘못된 요청 형식

```json
{
  "isSuccess": false,
  "code": 400,
  "message": "입력값을 확인해주세요.",
  "result": []
}
```

##### 질문 생성 실패

```json
{
  "isSuccess": false,
  "code": 3002,
  "message": "질문 생성에 실패했습니다.",
  "result": []
}
```

### 3.4 질문 수정

등록된 질문을 수정하는 API입니다.

#### 요청 URL

| 메서드 | URL                     |
| --- | ----------------------- |
| PUT | /api/questions/{questionId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |
| Content-Type  | String | required | application/json |

#### Path Variable

| 필드         | 타입   | 필수 여부    | 설명      |
| ---------- | ---- | -------- | ------- |
| questionId | Long | required | 수정할 질문 ID |

#### 요청 Body

```json
{
  "question": "지원 직무를 선택한 이유는 무엇인가요?"
}
```

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": []
}
```

#### 실패 응답

##### 질문을 찾을 수 없는 경우

```json
{
  "isSuccess": false,
  "code": 3000,
  "message": "질문을 찾을 수 없습니다.",
  "result": []
}
```

##### 질문 수정 권한이 없는 경우

```json
{
  "isSuccess": false,
  "code": 3003,
  "message": "질문 수정 권한이 없습니다.",
  "result": []
}
```

##### 공통 질문을 수정하려는 경우

```json
{
  "isSuccess": false,
  "code": 403,
  "message": "권한이 없습니다.",
  "result": []
}
```

### 3.5 자소서 기반 질문 생성 (FastAPI)

자소서를 기반으로 면접 질문을 생성하는 API입니다.

#### 요청 URL

| 메서드  | URL                            |
| ---- | ------------------------------ |
| POST | /fastapi/questions/cover-letter |

#### 요청 Header

| 필드           | 타입     | 필수 여부    | 설명              |
| ------------ | ------ | -------- | --------------- |
| Content-Type | String | required | application/json |

#### 요청 Body

```json
{
  "resume_id": 2,
  "member_id": 1,
  "company_name": "NIA",
  "position": "미디어사업관리",
  "num_questions": 5
}
```

#### 요청 필드 설명

| 필드           | 타입     | 필수 여부    | 설명                        |
| ------------ | ------ | -------- | ------------------------- |
| resume_id    | Long   | required | 자소서 ID                    |
| member_id    | Long   | required | 회원 ID                     |
| company_name | String | required | 회사명                       |
| position     | String | required | 지원 직무                     |
| num_questions | int    | optional | 생성할 질문 수               |

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": {
      "resume_id": 2,
      "job_role": "미디어사업관리",
      "experience_level": 0,
      "questions": [
        {
          "question_text": "현장포럼에서 시각적 요소를 강화한 홍보물을 제작했다고 했습니다. 당시 사용했던 디자인 툴이나 기술적 접근 방법을 구체적으로 설명해 주시겠습니까?",
          "evaluation_points": [
            "디자인 툴 활용 능력",
            "시각적 커뮤니케이션 기술"
          ],
          "key_answer_points": [
            "Adobe Illustrator",
            "Photoshop",
            "시각적 전달력",
            "사용자 경험"
          ]
        }
      ]
    }
}
```

#### 실패 응답

##### 잘못된 요청 형식

```json
{
  "isSuccess": false,
  "code": 400,
  "message": "입력값을 확인해주세요.",
  "result": []
}
```

##### 이력서를 찾을 수 없는 경우

```json
{
  "isSuccess": false,
  "code": 1001,
  "message": "이력서를 찾을 수 없습니다.",
  "result": []
}
```

##### 회원을 찾을 수 없는 경우

```json
{
  "isSuccess": false,
  "code": 1000,
  "message": "회원을 찾을 수 없습니다.",
  "result": []
}
```

##### 내부 서버 오류

```json
{
  "detail": "내부 서버 오류: 자소서 기반 질문 생성 중 오류가 발생했습니다."
}
```

## 4. 면접 리허설 결과 관련 API

### 4.1 면접 리허설에 대한 AI 팁 요청

면접 리허설에 대한 AI 팁을 요청하는 API입니다.

#### 요청 URL

| 메서드  | URL               |
| ---- | ----------------- |
| POST | /api/rehearsals/ai |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |
| Content-Type  | String | required | application/json |

#### 요청 Body

```json
{
    "answers": [
        "할 수 있습니다 취업을 하려....",
        "체계적으로....",
        "제가 공무원으로 근.... "
    ]
}
```

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": [
        {
            "answer": "할 수 있습니다. 공시생일 때도, ...",
            "tips": [
                "경험을 구체적으로 강조하세요. 예를 들어, 공무원 합격 당시의 체계적인 일정 관리와 구체적인 학습 계획을 언급하며 성장 경험을 부각시키세요.",
                "키워드를 적극 활용하세요. '체계적', '전문적', '경쟁력' 등 긍정적인 단어를 사용하여 스스로를 어필할 수 있도록 하세요.",
                "의문점과 성찰 부분도 긍정적으로 표현하세요. 현재 자신의 방법에 대한 지속적인 검토와 개선 의지를 보여주면 좋습니다.",
                "싸피에서의 목표를 구체적으로 연결지어, 해당 조직에서 어떻게 성공적인 결과를 낼 수 있을지를 설명하면 더욱 설득력 있는 답변이 될 것입니다."
            ]
        },
        {
            "answer": "체계적으로 프론트엔드 지식을 쌓고,...",
            "tips": [
                "경험을 더 구체적으로 언급하세요. 예를 들어, SSAFY의 4개 프로젝트 중 어떤 부분에서 어떤 역할을 맡고 성장했는지 상세하게 설명하면 좋습니다.",
                "자신의 열정, 도전 정신, 그리고 팀워크를 키워드로 강조하여 인성과 동기부여를 어필하는 것이 좋습니다.",
                "지인 추천 및 실제 후기를 통한 자료 조사 경험을 구체적인 사례나 숫자로 보강하면 설득력이 높아집니다."
            ]
        },
        {
            "answer": "제가 공무원으로 근무하다....",
            "tips": [
                "경험 강조: 구체적인 업무 경험과 전환 과정을 설명하면서 왜 변화가 필요했는지에 대해 명확하게 전달하세요.",
                "키워드 활용: '책임감', '창의성', '사용자 중심', '비전' 등의 키워드를 사용해 본인의 가치와 목표를 강조하는 것이 좋습니다.",
                "상황-행동-결과(SCAR) 기법: 공무원 근무 중 느낀 한계와 개발로 전향한 계기를 구체적인 사례와 함께 설명하면 설득력이 높아집니다.",
                "미래 목표 명시: SSAFY 교육과정을 통한 구체적인 기술 습득 목표와, 이를 통해 사용자 편의 서비스를 어떻게 구현할지에 대해 명확하게 언급하세요."
            ]
        }
    ]
}
```

#### 실패 응답

##### 인증 오류

```json
{
  "isSuccess": false,
  "code": 401,
  "message": "인증이 필요합니다.",
  "result": []
}
```

##### 잘못된 요청 형식

```json
{
  "isSuccess": false,
  "code": 400,
  "message": "입력값을 확인해주세요.",
  "result": []
}
```

##### 내부 서버 오류

```json
{
  "isSuccess": false,
  "code": 500,
  "message": "AI 피드백 생성 중 오류가 발생했습니다.",
  "result": []
}
```

### 4.2 키워드 추출 API

면접 답변에서 키워드를 추출하는 API입니다.

#### 요청 URL

| 메서드  | URL                    |
| ---- | ---------------------- |
| POST | /api/rehearsals/keyword |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |
| Content-Type  | String | required | application/json |

#### 요청 Body

```json
{
    "answers": [
        "할 수 있습니다. 공시생일 ...",
        "체계적으로 프론트엔드 지식을 쌓고...",
        "제가 공무원으로 근무하다 최근에 ..."
    ]
}
```

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": [
        {
            "answer": "할 수 있습니다. 공시생일 때도 ...",
            "keywords": [
                "공시생",
                "강의",
                "체계적 일정",
                "8개월",
                "공무원 합격",
                "싸피",
                "전문적 커리큘럼",
                "경쟁력",
                "취업"
            ]
        },
        {
            "answer": "체계적으로 프론트엔드 지식을 쌓고...",
            "keywords": [
                "프론트엔드",
                "실무 경험",
                "개발",
                "싸피",
                "SSAFY",
                "프로젝트",
                "웹 지식",
                "실전 경험",
                "경쟁",
                "열정"
            ]
        },
        {
            "answer": "제가 공무원으로 근무하다 최근에 ...",
            "keywords": [
                "공무원",
                "퇴사",
                "디자인",
                "영상 편집",
                "개발",
                "블로그",
                "SSAFY",
                "서비스"
            ]
        }
    ]
}
```

#### 실패 응답

##### 잘못된 요청

```json
{
  "isSuccess": false,
  "code": 5001,
  "message": "잘못된 요청입니다.",
  "result": []
}
```

##### 키워드 추출 실패

```json
{
  "isSuccess": false,
  "code": 5002,
  "message": "키워드 추출에 실패했습니다.",
  "result": []
}
```


### 4.3 리허설 결과 저장하기 (FastAPI)

리허설 결과를 저장하는 API입니다.

#### 요청 URL

| 메서드  | URL                |
| ---- | ------------------ |
| POST | /fastapi/rehearsals |

#### 요청 Header

| 필드           | 타입     | 필수 여부    | 설명              |
| ------------ | ------ | -------- | --------------- |
| Content-Type | String | required | application/json |

#### 요청 Body

```json
{
  "memberId": 123,
  "title": "백엔드 개발자 면접 리허설",
  "questions": [
    {
      "question": "자바의 OOP 특징에 대해 설명해주세요",
      "answer": "자바의 객체지향 프로그래밍 특징으로는 캡슐화, 상속, 다형성, 추상화가 있습니다. 캡슐화는 데이터와 메소드를 하나로 묶고 접근을 제한하는 것이고, 상속은 기존 클래스의 특성을 물려받아 새로운 클래스를 생성하는 것입니다."
    },
    {
      "question": "스프링의 IoC와 DI에 대해 설명해주세요",
      "answer": "IoC(Inversion of Control)는 프로그램 제어 흐름이 개발자가 아닌 프레임워크에 의해 관리되는 것을 의미합니다. DI(Dependency Injection)는 IoC의 구현 방법 중 하나로, 객체 간의 의존성을 외부에서 주입하는 패턴입니다."
    },
    {
      "question": "RESTful API 설계 원칙에 대해 설명해주세요",
      "answer": "RESTful API 설계 원칙으로는 자원의 식별(URI), 행위에 대한 정의(HTTP Method), 표현(JSON/XML)을 통한 자원의 상태 전달, 무상태성, 균일한 인터페이스 등이 있습니다."
    }
  ]
}
```

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": []
}
```

#### 실패 응답

##### 잘못된 요청 형식

```json
{
  "isSuccess": false,
  "code": 400,
  "message": "입력값을 확인해주세요.",
  "result": []
}
```

##### 내부 서버 오류

```json
{
  "detail": "내부 서버 오류: AI 면접 평가 중 오류가 발생했습니다."
}
```

### 4.4 면접 리허설설 결과 삭제

특정 모의 면접 결과를 삭제하는 API입니다.

#### 요청 URL

| 메서드    | URL                          |
| ------ | ---------------------------- |
| DELETE | /api/rehearsals/{rehearsalResultId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명              |
| ------------- | ------ | -------- | --------------- |
| Authorization | String | required | Bearer {JWT 토큰} |

#### Path Variable

| 필드                | 타입   | 필수 여부    | 설명           |
| ----------------- | ---- | -------- | ------------ |
| rehearsalResultId | Long | required | 삭제할 모의 면접 결과 ID |

#### 성공 응답

```json
{
    "isSuccess": true,
    "code": 200,
    "message": "요청에 성공하였습니다.",
    "result": []
}
```

#### 실패 응답

##### 리허설 결과를 찾을 수 없는 경우

```json
{
  "isSuccess": false,
  "code": 4000,
  "message": "리허설 결과를 찾을 수 없습니다.",
  "result": []
}
```

##### 접근 권한이 없는 경우

```json
{
  "isSuccess": false,
  "code": 4001,
  "message": "리허설 결과에 접근 권한이 없습니다.",
  "result": []
}
```

##### 삭제 실패

```json
{
  "isSuccess": false,
  "code": 4004,
  "message": "리허설 결과 삭제에 실패했습니다.",
  "result": []
}
```

## 5. 기업 관련 API

### 5.1 기업 목록 조회

기업 목록을 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL           |
| --- | ------------- |
| GET | /api/companies |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### 성공 응답

```json
{
  "isSuccess": true,
  "code": 200,
  "message": "기업 목록 조회에 성공하였습니다.",
  "result": [
    {
      "companyId": 1,
      "companyName": "ABC Corp",
      "companyLogo": "https://s3…/abc.png",
      "nStartDate": "2024-10-10T14:00",
      "nEndDate": "2024-12-12T16:00"
    },
    {
      "companyId": 2,
      "companyName": "XYZ Ltd",
      "companyLogo": null,
      "nStartDate": "2024-10-10",
      "nEndDate": "2024-12-12"
    }
  ]
}
```

#### 실패 응답

```json
{
  "isSuccess": false,
  "code": 2001,
  "message": "기업 목록 조회에 실패했습니다.",
  "result": null
}
```

### 5.2 기업 상세 조회

특정 기업의 상세 정보와 채용 문항을 조회하는 API입니다.

#### 요청 URL

| 메서드 | URL                     |
| --- | ----------------------- |
| GET | /api/companies/{companyId} |

#### 요청 Header

| 필드            | 타입     | 필수 여부    | 설명                 |
| ------------- | ------ | -------- | ------------------ |
| Authorization | String | required | Bearer AccessToken |

#### Path Variable

| 필드        | 타입   | 필수 여부    | 설명      |
| --------- | ---- | -------- | ------- |
| companyId | Long | required | 기업 ID   |

#### 성공 응답

```json
{
  "isSuccess": true, 
  "code": 200, 
  "message": "기업 상세 조회에 성공했습니다.", 
  "result": {
    "employmentPageUrl": "https://.net", 
    "employmentUrl": "https://net", 
    "questions": [
      {
        "questionId": 10, 
        "companyQuestion": "지원 동기를 작성해주세요.", 
        "jobName": "백엔드 개발자", 
        "jobType": 2, 
        "charLimit": 300
      }, 
      {
        "questionId": 11, 
        "companyQuestion": "본인의 강점을 설명해주세요.", 
        "jobName": "백엔드 개발자", 
        "jobType": 2, 
        "charLimit": 200
      }
    ]
  }
}
```

#### 실패 응답

```json
{
  "isSuccess": false, 
  "code": 2005, 
  "message": "문항 목록 조회에 실패했습니다.", 
  "result": null
}
```

## 오류 코드

API 호출 시 발생할 수 있는 오류 코드에 대한 설명입니다.

### 일반 오류 코드

| HTTP 상태 코드 | 코드  | 메시지            | 설명                        |
| ---------- | --- | -------------- | ------------------------- |
| 400        | 400 | 입력값을 확인해주세요.   | 요청 파라미터가 잘못된 경우           |
| 401        | 401 | 인증이 필요합니다.     | 인증이 필요한 API에 인증 없이 접근한 경우 |
| 403        | 403 | 권한이 없습니다.      | 해당 리소스에 접근 권한이 없는 경우      |
| 404        | 404 | 대상을 찾을 수 없습니다. | 요청한 리소스가 존재하지 않는 경우       |

### 인증 관련 오류 코드

| HTTP 상태 코드 | 코드  | 메시지                          | 설명                          |
| ---------- | --- | ---------------------------- | --------------------------- |
| 404        | 405 | 리프레쉬 토큰을 찾을 수 없습니다.          | 리프레시 토큰이 존재하지 않는 경우         |
| 404        | 406 | 리프레쉬 토큰이 만료되었습니다.            | 리프레시 토큰이 만료된 경우             |
| 404        | 407 | 유효하지 않은 리프레쉬 토큰입니다.          | 리프레시 토큰이 유효하지 않은 경우         |
| 401        | 408 | 유효하지 않은 토큰입니다.               | 인증 토큰이 유효하지 않은 경우           |
| 401        | 409 | Authorization 헤더가 유효하지 않습니다. | Authorization 헤더 형식이 잘못된 경우 |

### 회원 관련 오류 코드

| HTTP 상태 코드 | 코드   | 메시지                   | 설명                       |
| ---------- | ---- | --------------------- | ------------------------ |
| 404        | 1000 | 회원을 찾을 수 없습니다.        | 해당 ID의 회원이 존재하지 않는 경우    |
| 404        | 1001 | 이력서를 찾을 수 없습니다.       | 해당 ID의 이력서가 존재하지 않는 경우   |
| 400        | 1002 | 프로필 이미지 업로드에 실패하였습니다. | 프로필 이미지 업로드 중 오류가 발생한 경우 |

### 기업 관련 오류 코드

| HTTP 상태 코드 | 코드   | 메시지               | 설명                    |
| ---------- | ---- | ----------------- | --------------------- |
| 400        | 2001 | 기업 목록 조회에 실패했습니다. | 기업 목록 조회 중 오류가 발생한 경우 |
| 400        | 2005 | 문항 목록 조회에 실패했습니다. | 기업 상세 조회 중 오류가 발생한 경우 |

### 질문 관련 오류 코드

| HTTP 상태 코드 | 코드   | 메시지                | 설명                    |
| ---------- | ---- | ------------------ | --------------------- |
| 404        | 3000 | 질문을 찾을 수 없습니다.     | 해당 ID의 질문이 존재하지 않는 경우 |
| 400        | 3001 | 유효하지 않은 질문 유형입니다.  | 질문 유형이 유효하지 않은 경우     |
| 500        | 3002 | 질문 생성에 실패했습니다.     | 질문 생성 중 내부 오류가 발생한 경우 |
| 403        | 3003 | 질문 수정 권한이 없습니다.    | 질문 수정 권한이 없는 경우       |
| 500        | 3004 | 질문 삭제에 실패했습니다.     | 질문 삭제 중 내부 오류가 발생한 경우 |
| 400        | 3005 | 자소서 형식이 올바르지 않습니다. | 자소서 형식이 올바르지 않은 경우    |
| 403        | 3006 | 공통 질문은 삭제할 수 없습니다. | 공통 질문 삭제 시도 시         |

### 리허설 관련 오류 코드

| HTTP 상태 코드 | 코드   | 메시지                  | 설명                        |
| ---------- | ---- | -------------------- | ------------------------- |
| 404        | 4000 | 리허설 결과를 찾을 수 없습니다.   | 해당 ID의 리허설 결과가 존재하지 않는 경우 |
| 403        | 4001 | 리허설 결과에 접근 권한이 없습니다. | 리허설 결과에 접근 권한이 없는 경우      |
| 500        | 4002 | 리허설 결과 생성에 실패했습니다.   | 리허설 결과 생성 중 내부 오류가 발생한 경우 |
| 500        | 4003 | 리허설 결과 수정에 실패했습니다.   | 리허설 결과 수정 중 내부 오류가 발생한 경우 |
| 500        | 4004 | 리허설 결과 삭제에 실패했습니다.   | 리허설 결과 삭제 중 내부 오류가 발생한 경우 |

### 키워드 관련 오류 코드

| HTTP 상태 코드 | 코드   | 메시지             | 설명                     |
| ---------- | ---- | --------------- | ---------------------- |
| 400        | 5001 | 잘못된 요청입니다.      | 키워드 추출 요청이 잘못된 경우      |
| 500        | 5002 | 키워드 추출에 실패했습니다. | 키워드 추출 중 내부 오류가 발생한 경우 |



## 개발 환경

- 언어: Java 17, Python 3.10.11
- 프레임워크: Spring Boot 3.4.4, FastAPI 0.115.9
- 데이터베이스: MySQL
- 빌드 도구: Gradle
- 기타: JPA, Spring Security, JWT