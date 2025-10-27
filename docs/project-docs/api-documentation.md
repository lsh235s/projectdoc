# API 문서

## 개요
이 문서는 프로젝트 관리 시스템의 RESTful API에 대한 상세한 설명을 제공합니다.

## 기본 정보
- **Base URL**: `https://api.project-management.com/v1`
- **인증 방식**: JWT Bearer Token
- **응답 형식**: JSON
- **문자 인코딩**: UTF-8

## 공통 응답 형식

### 성공 응답
```json
{
  "success": true,
  "data": {
    // 실제 데이터
  },
  "message": "요청이 성공적으로 처리되었습니다.",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 에러 응답
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "입력값이 올바르지 않습니다.",
    "details": [
      {
        "field": "email",
        "message": "이메일 형식이 올바르지 않습니다."
      }
    ]
  },
  "timestamp": "2024-01-15T10:30:00Z"
}
```

## 인증 API

### 로그인
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

**응답:**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "email": "user@example.com",
      "username": "john_doe",
      "role": "USER"
    }
  }
}
```

### 회원가입
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "newuser@example.com",
  "password": "password123",
  "username": "new_user",
  "name": "New User"
}
```

### 토큰 갱신
```http
POST /api/auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

## 사용자 관리 API

### 사용자 목록 조회
```http
GET /api/users?page=1&size=10&sort=createdAt,desc
Authorization: Bearer {accessToken}
```

**응답:**
```json
{
  "success": true,
  "data": {
    "content": [
      {
        "id": 1,
        "email": "user1@example.com",
        "username": "user1",
        "name": "User One",
        "role": "USER",
        "createdAt": "2024-01-15T10:30:00Z"
      }
    ],
    "pageable": {
      "pageNumber": 0,
      "pageSize": 10,
      "totalElements": 1,
      "totalPages": 1
    }
  }
}
```

### 사용자 상세 조회
```http
GET /api/users/{id}
Authorization: Bearer {accessToken}
```

## 프로젝트 관리 API

### 프로젝트 목록 조회
```http
GET /api/projects?page=1&size=10&status=ACTIVE
Authorization: Bearer {accessToken}
```

### 프로젝트 생성
```http
POST /api/projects
Authorization: Bearer {accessToken}
Content-Type: application/json

{
  "name": "새 프로젝트",
  "description": "프로젝트 설명",
  "status": "ACTIVE"
}
```

**응답:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "새 프로젝트",
    "description": "프로젝트 설명",
    "status": "ACTIVE",
    "ownerId": 1,
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T10:30:00Z"
  }
}
```

### 프로젝트 수정
```http
PUT /api/projects/{id}
Authorization: Bearer {accessToken}
Content-Type: application/json

{
  "name": "수정된 프로젝트명",
  "description": "수정된 설명",
  "status": "COMPLETED"
}
```

## 회의록 관리 API

### 회의록 목록 조회
```http
GET /api/projects/{projectId}/meetings?page=1&size=10
Authorization: Bearer {accessToken}
```

### 회의록 생성
```http
POST /api/projects/{projectId}/meetings
Authorization: Bearer {accessToken}
Content-Type: application/json

{
  "title": "프로젝트 킥오프 회의",
  "content": "회의 내용...",
  "meetingDate": "2024-01-15T14:00:00Z",
  "attendees": [
    {
      "userId": 1,
      "role": "진행자"
    },
    {
      "userId": 2,
      "role": "참석자"
    }
  ],
  "agenda": [
    {
      "title": "프로젝트 목표 설정",
      "description": "프로젝트의 목표와 범위를 정의합니다."
    }
  ],
  "actionItems": [
    {
      "title": "요구사항 정의서 작성",
      "assigneeId": 1,
      "dueDate": "2024-01-20T00:00:00Z",
      "priority": "HIGH"
    }
  ]
}
```

### 회의록 상세 조회
```http
GET /api/meetings/{id}
Authorization: Bearer {accessToken}
```

**응답:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "projectId": 1,
    "title": "프로젝트 킥오프 회의",
    "content": "회의 내용...",
    "meetingDate": "2024-01-15T14:00:00Z",
    "createdBy": {
      "id": 1,
      "username": "john_doe",
      "name": "John Doe"
    },
    "attendees": [
      {
        "user": {
          "id": 1,
          "username": "john_doe",
          "name": "John Doe"
        },
        "role": "진행자"
      }
    ],
    "agenda": [
      {
        "title": "프로젝트 목표 설정",
        "description": "프로젝트의 목표와 범위를 정의합니다."
      }
    ],
    "actionItems": [
      {
        "id": 1,
        "title": "요구사항 정의서 작성",
        "assignee": {
          "id": 1,
          "username": "john_doe",
          "name": "John Doe"
        },
        "dueDate": "2024-01-20T00:00:00Z",
        "priority": "HIGH",
        "status": "PENDING"
      }
    ],
    "createdAt": "2024-01-15T15:00:00Z",
    "updatedAt": "2024-01-15T15:00:00Z"
  }
}
```

## 멘토링 일지 관리 API

### 멘토링 일지 목록 조회
```http
GET /api/projects/{projectId}/mentoring?page=1&size=10
Authorization: Bearer {accessToken}
```

### 멘토링 일지 생성
```http
POST /api/projects/{projectId}/mentoring
Authorization: Bearer {accessToken}
Content-Type: application/json

{
  "title": "백엔드 개발 멘토링",
  "content": "멘토링 내용...",
  "mentoringDate": "2024-01-16T16:00:00Z",
  "mentorId": 1,
  "menteeId": 2,
  "mentoringType": "REGULAR",
  "duration": 90,
  "goals": [
    "RESTful API 설계 원칙 이해",
    "데이터베이스 정규화 개념 학습"
  ],
  "topics": [
    {
      "title": "API 설계",
      "description": "RESTful API 설계 원칙에 대해 설명"
    }
  ],
  "actionItems": [
    {
      "title": "Spring Security 학습",
      "assigneeId": 2,
      "dueDate": "2024-01-20T00:00:00Z",
      "priority": "HIGH"
    }
  ],
  "mentorFeedback": "잘한 점: 적극적인 질문과 학습 자세",
  "menteeFeedback": "도움이 된 부분: 실제 코드를 통한 설명"
}
```

## 에러 코드

### HTTP 상태 코드
- `200 OK`: 요청 성공
- `201 Created`: 리소스 생성 성공
- `400 Bad Request`: 잘못된 요청
- `401 Unauthorized`: 인증 실패
- `403 Forbidden`: 권한 없음
- `404 Not Found`: 리소스 없음
- `409 Conflict`: 리소스 충돌
- `500 Internal Server Error`: 서버 오류

### 비즈니스 에러 코드
- `VALIDATION_ERROR`: 입력값 검증 실패
- `AUTHENTICATION_FAILED`: 인증 실패
- `AUTHORIZATION_FAILED`: 권한 없음
- `RESOURCE_NOT_FOUND`: 리소스 없음
- `DUPLICATE_RESOURCE`: 중복 리소스
- `BUSINESS_LOGIC_ERROR`: 비즈니스 로직 오류

## 인증 및 권한

### JWT 토큰 구조
```json
{
  "sub": "user@example.com",
  "iat": 1642248000,
  "exp": 1642251600,
  "role": "USER",
  "userId": 1
}
```

### 권한 레벨
- `ADMIN`: 모든 권한
- `MANAGER`: 프로젝트 관리 권한
- `USER`: 기본 사용자 권한
- `GUEST`: 읽기 전용 권한

## Rate Limiting
- 기본 제한: 분당 100 요청
- 인증된 사용자: 분당 500 요청
- 관리자: 분당 1000 요청

## API 버전 관리
- 현재 버전: v1
- 버전 지정: URL 경로에 포함 (`/api/v1/`)
- 하위 호환성 유지 원칙

---
**작성일**: 2024-01-15  
**최종 수정일**: 2024-01-15  
**작성자**: 개발팀
