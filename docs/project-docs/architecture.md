# 프로젝트 아키텍처 문서

## 시스템 아키텍처 개요

### 전체 시스템 구조
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Backend       │    │   Database      │
│   (React)       │◄──►│   (Spring Boot) │◄──►│   (PostgreSQL)  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   CDN           │    │   Redis Cache   │    │   File Storage  │
│   (Static Files)│    │   (Session)     │    │   (AWS S3)      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 기술 스택

### Frontend
- **Framework**: React 18
- **Language**: TypeScript
- **State Management**: Redux Toolkit
- **UI Library**: Material-UI
- **Build Tool**: Vite
- **Testing**: Jest, React Testing Library

### Backend
- **Framework**: Spring Boot 3.x
- **Language**: Java 17
- **Database**: PostgreSQL 14
- **ORM**: Spring Data JPA
- **Security**: Spring Security + JWT
- **Testing**: JUnit 5, Mockito
- **Documentation**: Swagger/OpenAPI

### Infrastructure
- **Container**: Docker, Docker Compose
- **Cache**: Redis 6
- **Message Queue**: RabbitMQ
- **File Storage**: AWS S3
- **CDN**: CloudFront
- **Monitoring**: Prometheus, Grafana

## 데이터베이스 설계

### 주요 엔티티
```sql
-- 사용자 테이블
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'USER',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 프로젝트 테이블
CREATE TABLE projects (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    owner_id BIGINT REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 회의록 테이블
CREATE TABLE meeting_notes (
    id BIGSERIAL PRIMARY KEY,
    project_id BIGINT REFERENCES projects(id),
    title VARCHAR(200) NOT NULL,
    content TEXT NOT NULL,
    meeting_date TIMESTAMP NOT NULL,
    created_by BIGINT REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 멘토링 일지 테이블
CREATE TABLE mentoring_logs (
    id BIGSERIAL PRIMARY KEY,
    project_id BIGINT REFERENCES projects(id),
    mentor_id BIGINT REFERENCES users(id),
    mentee_id BIGINT REFERENCES users(id),
    title VARCHAR(200) NOT NULL,
    content TEXT NOT NULL,
    mentoring_date TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## API 설계

### RESTful API 엔드포인트

#### 인증 관련
```
POST   /api/auth/login          # 로그인
POST   /api/auth/logout         # 로그아웃
POST   /api/auth/refresh        # 토큰 갱신
POST   /api/auth/register       # 회원가입
```

#### 사용자 관리
```
GET    /api/users               # 사용자 목록 조회
GET    /api/users/{id}          # 사용자 상세 조회
PUT    /api/users/{id}          # 사용자 정보 수정
DELETE /api/users/{id}          # 사용자 삭제
```

#### 프로젝트 관리
```
GET    /api/projects            # 프로젝트 목록 조회
POST   /api/projects            # 프로젝트 생성
GET    /api/projects/{id}       # 프로젝트 상세 조회
PUT    /api/projects/{id}       # 프로젝트 수정
DELETE /api/projects/{id}       # 프로젝트 삭제
```

#### 회의록 관리
```
GET    /api/projects/{id}/meetings           # 프로젝트 회의록 목록
POST   /api/projects/{id}/meetings           # 회의록 생성
GET    /api/meetings/{id}                    # 회의록 상세 조회
PUT    /api/meetings/{id}                    # 회의록 수정
DELETE /api/meetings/{id}                    # 회의록 삭제
```

#### 멘토링 일지 관리
```
GET    /api/projects/{id}/mentoring          # 프로젝트 멘토링 일지 목록
POST   /api/projects/{id}/mentoring          # 멘토링 일지 생성
GET    /api/mentoring/{id}                   # 멘토링 일지 상세 조회
PUT    /api/mentoring/{id}                   # 멘토링 일지 수정
DELETE /api/mentoring/{id}                   # 멘토링 일지 삭제
```

## 보안 아키텍처

### 인증 및 인가
- JWT 토큰 기반 인증
- Access Token (15분) + Refresh Token (7일)
- 역할 기반 접근 제어 (RBAC)
- API 엔드포인트별 권한 설정

### 데이터 보호
- HTTPS 통신 강제
- 민감한 데이터 암호화 저장
- SQL Injection 방지 (JPA 사용)
- XSS 공격 방지 (입력값 검증)

## 성능 최적화

### 캐싱 전략
- Redis를 활용한 세션 저장
- 자주 조회되는 데이터 캐싱
- CDN을 통한 정적 파일 캐싱

### 데이터베이스 최적화
- 적절한 인덱스 설정
- 쿼리 최적화
- 연결 풀 설정

### 프론트엔드 최적화
- 코드 스플리팅
- 이미지 최적화
- 번들 크기 최적화

## 모니터링 및 로깅

### 애플리케이션 모니터링
- Prometheus를 통한 메트릭 수집
- Grafana를 통한 대시보드 구성
- 알림 설정 (Slack, Email)

### 로깅 전략
- 구조화된 로그 (JSON 형식)
- 로그 레벨별 분리
- 중앙 집중식 로그 관리

## 배포 아키텍처

### 환경 구성
- **Development**: 개발자 로컬 환경
- **Staging**: 테스트 환경 (프로덕션과 유사)
- **Production**: 운영 환경

### CI/CD 파이프라인
1. 코드 커밋 → GitHub
2. 자동 테스트 실행
3. Docker 이미지 빌드
4. Staging 환경 배포
5. 수동 승인 후 Production 배포

### 컨테이너 오케스트레이션
- Docker Compose (로컬 개발)
- Kubernetes (프로덕션 환경 고려)

## 확장성 고려사항

### 수평적 확장
- 무상태(Stateless) 애플리케이션 설계
- 로드 밸런서 활용
- 데이터베이스 샤딩 고려

### 수직적 확장
- 서버 리소스 모니터링
- 성능 병목 지점 식별
- 적절한 하드웨어 스펙 선택

---
**작성일**: 2024-01-15  
**최종 수정일**: 2024-01-15  
**작성자**: 아키텍처팀
