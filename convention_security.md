# 보안 관련 코딩 컨벤션

## 1. 민감 정보 처리

- **하드코딩 금지**: 소스 코드에 비밀번호, API 키, 토큰 등 민감 정보를 절대 하드코딩하지 않습니다.
  ```java
  // 잘못된 예:
  String apiKey = "1234abcd5678efgh";
  
  // 올바른 예:
  String apiKey = System.getenv("API_KEY");
  ```

- **환경 변수 활용**: 민감 정보는 환경 변수나 암호화된 설정 파일을 통해 관리합니다.
  - 개발 환경: .env 파일 (단, .gitignore에 반드시 포함)
  - 운영 환경: 환경 변수 또는 보안 저장소(AWS Secrets Manager, Hashicorp Vault 등)

- **암호화**: 중요 데이터는 반드시 적절한 암호화 알고리즘을 사용하여 저장합니다.
  - 비밀번호는 단방향 해시(bcrypt, Argon2 등)로 저장
  - 기타 중요 정보는 강력한 대칭/비대칭 암호화 사용

- **로깅 주의**: 로그에 민감 정보가 기록되지 않도록 합니다.
  ```java
  // 잘못된 예:
  logger.info("사용자 로그인: " + username + ", 비밀번호: " + password);
  
  // 올바른 예:
  logger.info("사용자 로그인: " + username);
  ```

## 2. 입력 검증 규칙

- **모든 사용자 입력 검증**: 신뢰할 수 없는 모든 데이터는 서버 측에서 반드시 검증합니다.
  - 클라이언트 측 검증은 UX 향상용일 뿐, 보안 목적으로는 불충분합니다.

- **입력 데이터 타입 및 범위 확인**: 입력값의 타입, 길이, 형식, 범위를 엄격하게 검사합니다.
  ```java
  // 예:
  if (age < 0 || age > 150) {
      throw new ValidationException("유효하지 않은 나이 범위입니다.");
  }
  ```

- **화이트리스트 검증**: 블랙리스트보다 화이트리스트 방식으로 허용 패턴만 검증합니다.
  ```java
  // 잘못된 예(블랙리스트):
  if (input.contains("<script>")) { reject(); }
  
  // 올바른 예(화이트리스트):
  if (!Pattern.matches("^[a-zA-Z0-9\\s]+$", input)) { reject(); }
  ```

- **SQL 인젝션 방지**: 파라미터화된 쿼리(Prepared Statement)를 항상 사용합니다.
  ```java
  // 잘못된 예:
  String query = "SELECT * FROM users WHERE username = '" + username + "'";
  
  // 올바른 예:
  String query = "SELECT * FROM users WHERE username = ?";
  PreparedStatement stmt = connection.prepareStatement(query);
  stmt.setString(1, username);
  ```

- **XSS 방지**: 출력 시 적절한 인코딩을 적용합니다.
  - HTML 컨텍스트: HTML 엔티티 인코딩
  - JavaScript 컨텍스트: JavaScript 이스케이프
  - CSS 컨텍스트: CSS 이스케이프
  - URL 파라미터: URL 인코딩

## 3. 인증 및 인가 구현 표준

- **강력한 비밀번호 정책**: 비밀번호 복잡성 요구사항을 적용합니다.
  - 최소 8자 이상
  - 대문자, 소문자, 숫자, 특수문자 조합
  - 일반적인 패턴 및 사전 단어 금지
  - 주기적 변경 (선택적)

- **다중 인증(MFA)**: 중요 기능에는 다중 인증을 구현합니다.

- **인증 토큰 관리**:
  - JWT 사용 시 만료 시간 설정 (짧게 유지)
  - 서명 알고리즘은 HS256 이상 또는 RS256 사용
  - 토큰에 민감 정보 포함 금지

- **세션 관리**:
  - 세션 ID는 무작위 생성
  - 인증 후 세션 재생성(session fixation 방지)
  - 적절한 세션 타임아웃 설정
  - 로그아웃 시 세션 폐기

- **최소 권한 원칙**: 사용자에게 필요한 최소한의 권한만 부여합니다.
  ```java
  // 예: 권한 검사
  if (!hasPermission(user, "DOCUMENT_EDIT")) {
      throw new AccessDeniedException("문서 편집 권한이 없습니다.");
  }
  ```

- **CORS 설정**: Cross-Origin Resource Sharing 설정을 엄격하게 관리합니다.
  - 신뢰할 수 있는 도메인만 허용
  - 와일드카드(*) 사용 지양

## 4. API 보안

- **레이트 리밋(Rate Limiting)**: API 호출 횟수를 제한하여 DoS 공격을 방지합니다.

- **HTTPS 필수**: 모든 통신은 HTTPS로만 이루어져야 합니다.
  - HTTP Strict Transport Security(HSTS) 헤더 사용

- **API 키 및 토큰 관리**:
  - API 키는 최소 권한으로 발급
  - 주기적인 순환 및 폐기 메커니즘 구현
  - 유효기간 설정

- **에러 메시지**: 에러 응답에 민감 정보나 상세 오류 스택트레이스를 포함하지 않습니다.
  ```java
  // 잘못된 예:
  return ResponseEntity.status(500).body(e.getStackTrace());
  
  // 올바른 예:
  logger.error("데이터베이스 오류", e); // 로깅은 상세하게
  return ResponseEntity.status(500).body("서버 오류가 발생했습니다.");
  ```

## 5. 파일 업로드 및 다운로드 보안

- **파일 검증**: 업로드된 파일의 유형, 크기, 콘텐츠를 엄격하게 검증합니다.
  - 파일 확장자 검사
  - MIME 타입 검사
  - 악성 콘텐츠 스캔 (가능한 경우)

- **안전한 저장**: 업로드된 파일은 웹 루트 외부에 저장합니다.

- **파일 접근 제어**: 다운로드 시 권한 검사를 수행합니다.

## 6. 의존성 관리

- **라이브러리 취약점 정기 점검**: 정기적으로 의존성 취약점을 검사합니다.
  - OWASP Dependency Check 또는 Snyk 등 도구 활용

- **최신 버전 유지**: 보안 패치가 적용된 최신 버전 사용을 권장합니다.

- **사용하지 않는 의존성 제거**: 불필요한 의존성은 제거하여 공격 표면을 줄입니다.

## 7. 배포 및 인프라 보안

- **보안 설정 확인**: 서버, 데이터베이스, 웹 서버 등의 보안 설정을 확인합니다.
  - 기본 계정 및 비밀번호 변경
  - 불필요한 서비스 비활성화
  - 방화벽 및 네트워크 접근 제한

- **컨테이너 보안**: 컨테이너 이미지 취약점 검사를 수행합니다.

- **시크릿 관리**: 배포 파이프라인에서 시크릿 관리 도구를 사용합니다.

## 8. 보안 코드 리뷰

- **보안 관점의 코드 리뷰**: 모든 코드 리뷰에서 보안 문제를 확인합니다.
  - OWASP Top 10 취약점 확인
  - 보안 안티패턴 식별

- **자동화된 보안 검사**: 정적 분석 도구를 CI/CD 파이프라인에 통합합니다.
  - SonarQube, Checkmarx, Fortify 등 활용
