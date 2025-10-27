# 프로젝트 가이드라인

## 프로젝트 개요
이 문서는 프로젝트 관리 시스템의 개발 가이드라인을 정의합니다.

## 개발 환경 설정

### 필수 요구사항
- Java 17 이상
- Node.js 18 이상
- Docker 및 Docker Compose
- Git

### 개발 도구
- IDE: IntelliJ IDEA 또는 VS Code
- 데이터베이스: PostgreSQL 14+
- 캐시: Redis 6+
- 메시지 큐: RabbitMQ

## 코딩 컨벤션

### Java (Backend)
- Java 코딩 컨벤션 준수
- 클래스명: PascalCase (예: UserService)
- 메서드명: camelCase (예: getUserById)
- 상수명: UPPER_SNAKE_CASE (예: MAX_RETRY_COUNT)

### JavaScript/TypeScript (Frontend)
- ESLint 및 Prettier 설정 준수
- 컴포넌트명: PascalCase (예: UserProfile)
- 함수명: camelCase (예: handleSubmit)
- 상수명: UPPER_SNAKE_CASE

## Git 워크플로우

### 브랜치 전략
- `main`: 프로덕션 배포 브랜치
- `develop`: 개발 통합 브랜치
- `feature/*`: 기능 개발 브랜치
- `hotfix/*`: 긴급 수정 브랜치
- `release/*`: 릴리스 준비 브랜치

### 커밋 메시지 규칙
```
type(scope): subject

body

footer
```

**Type 종류:**
- `feat`: 새로운 기능
- `fix`: 버그 수정
- `docs`: 문서 수정
- `style`: 코드 스타일 변경
- `refactor`: 코드 리팩토링
- `test`: 테스트 코드 추가/수정
- `chore`: 빌드 과정 또는 보조 도구 변경

**예시:**
```
feat(auth): add JWT token validation

- Implement JWT token validation middleware
- Add token refresh functionality
- Update authentication error handling

Closes #123
```

## 코드 리뷰 가이드라인

### 리뷰어 체크리스트
- [ ] 코드가 요구사항을 충족하는가?
- [ ] 코드가 프로젝트 컨벤션을 따르는가?
- [ ] 적절한 테스트가 포함되어 있는가?
- [ ] 보안 취약점은 없는가?
- [ ] 성능에 문제는 없는가?

### 리뷰이 체크리스트
- [ ] 코드가 정상적으로 동작하는가?
- [ ] 테스트가 모두 통과하는가?
- [ ] 문서가 업데이트되었는가?
- [ ] 불필요한 코드는 제거되었는가?

## 테스트 가이드라인

### 테스트 종류
1. **Unit Test**: 개별 함수/메서드 테스트
2. **Integration Test**: 컴포넌트 간 통합 테스트
3. **E2E Test**: 전체 사용자 시나리오 테스트

### 테스트 작성 원칙
- AAA 패턴 (Arrange, Act, Assert) 사용
- 테스트명은 명확하고 구체적으로 작성
- Mock 객체 적절히 활용
- 테스트 커버리지 80% 이상 유지

## 배포 가이드라인

### 환경별 배포
- **Development**: 개발자 로컬 환경
- **Staging**: 테스트 환경
- **Production**: 운영 환경

### 배포 프로세스
1. 코드 리뷰 완료
2. 모든 테스트 통과
3. Staging 환경 배포 및 테스트
4. Production 환경 배포
5. 배포 후 모니터링

## 문서화 가이드라인

### API 문서
- Swagger/OpenAPI 스펙 사용
- 모든 엔드포인트에 대한 상세 설명
- 요청/응답 예시 포함
- 에러 코드 및 메시지 정의

### 코드 문서
- 클래스 및 메서드에 Javadoc 작성
- 복잡한 로직에 주석 추가
- README 파일에 프로젝트 설정 방법 포함

## 보안 가이드라인

### 데이터 보호
- 민감한 정보는 환경변수로 관리
- 데이터베이스 연결 정보 암호화
- API 키 및 토큰 안전하게 저장

### 인증/인가
- JWT 토큰 기반 인증
- 역할 기반 접근 제어 (RBAC)
- 세션 타임아웃 설정

## 성능 가이드라인

### 데이터베이스
- 적절한 인덱스 설정
- N+1 쿼리 문제 방지
- 연결 풀 설정 최적화

### 캐싱
- Redis를 활용한 응답 캐싱
- 적절한 캐시 TTL 설정
- 캐시 무효화 전략 수립

## 모니터링 및 로깅

### 로깅
- 구조화된 로그 형식 사용 (JSON)
- 로그 레벨 적절히 설정
- 민감한 정보 로깅 금지

### 모니터링
- 애플리케이션 성능 모니터링 (APM)
- 서버 리소스 모니터링
- 에러 추적 및 알림 설정

---
**작성일**: 2024-01-15  
**최종 수정일**: 2024-01-15  
**작성자**: 개발팀
