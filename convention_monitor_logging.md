# 모니터링 및 로깅 코딩 컨벤션

## 1. 로깅 기본 원칙

- **목적 명확화**: 로그는 문제 해결, 성능 분석, 보안 모니터링, 사용자 행동 추적 등 명확한 목적을 가져야 합니다.

- **일관성 유지**: 전체 애플리케이션에서 일관된 로깅 스타일과 패턴을 유지합니다.

- **구조화된 로깅**: JSON 등 구조화된 형식으로 로그를 기록하여 분석과 검색을 용이하게 합니다.
  ```java
  // 잘못된 예:
  logger.info("사용자 " + userId + "가 로그인 시도했으나 실패. 원인: " + reason);
  
  // 올바른 예:
  logger.info("로그인 실패", Map.of(
      "userId", userId,
      "reason", reason,
      "ipAddress", ipAddress,
      "timestamp", Instant.now()
  ));
  ```

- **표준 로깅 라이브러리 사용**: 직접 System.out.println() 대신 로깅 프레임워크를 사용합니다.
  - Java: SLF4J + Logback, Log4j2
  - JavaScript: winston, pino
  - Python: logging, structlog

## 2. 로그 레벨 사용 규칙

- **TRACE**: 매우 상세한 디버깅 정보 (개발 환경에서만 사용)
  - 반복문 내부 변수 값, 메서드 진입/종료 추적
  - 예: `logger.trace("변수 값: {}", value);`

- **DEBUG**: 일반적인 디버깅 정보 (개발, 테스트 환경에서 사용)
  - 조건문 결과, 내부 상태, 중간 계산 값
  - 예: `logger.debug("상품 조회 결과: {}", product);`

- **INFO**: 정상적인 애플리케이션 이벤트, 중요 비즈니스 프로세스
  - 애플리케이션 시작/종료, 주요 기능 실행, 사용자 액션
  - 예: `logger.info("주문 #{} 생성됨", orderId);`

- **WARN**: 잠재적 문제, 성능 저하, 비정상 동작 (즉각 대응 불필요)
  - 제한 시간에 가까운 응답, 비권장 API 사용, 재시도 성공
  - 예: `logger.warn("캐시 접근 지연: {}ms", accessTime);`

- **ERROR**: 오류 상황, 예외 발생 (대응 필요)
  - 예외 발생, 중요 기능 실패, 외부 서비스 연결 실패
  - 예: `logger.error("결제 처리 실패", exception);`

- **FATAL**: 애플리케이션 중단 또는 심각한 장애 (즉각 대응 필요)
  - 데이터베이스 연결 불가, 필수 구성 누락, 복구 불가능한 상태
  - 예: `logger.fatal("데이터베이스 연결 실패로 서버 종료");`

- **로그 레벨 설정 가이드**:
  - 개발 환경: DEBUG 이상
  - 테스트 환경: INFO 이상
  - 운영 환경: 기본 INFO 이상, 필요시 특정 패키지만 DEBUG

## 3. 로그 포맷 및 구조화

- **표준 로그 형식**: 모든 로그에 다음 정보를 포함합니다.
  - 타임스탬프 (ISO 8601 형식)
  - 로그 레벨
  - 프로세스/스레드 ID
  - 클래스/컨텍스트 이름
  - 메시지
  - 추가 정보 (구조화된 형태)

- **로그 포맷 예시**:
  ```
  2023-06-15T14:32:10.456Z [INFO] [main] [com.example.OrderService] - 주문 생성 완료 {"orderId":"ORD-123","userId":"USR-456","amount":15000}
  ```

- **컨텍스트 정보 포함**: 요청 ID, 세션 ID, 사용자 ID 등 컨텍스트 정보를 포함합니다.
  ```java
  // MDC(Mapped Diagnostic Context) 활용 예시
  MDC.put("requestId", requestId);
  MDC.put("userId", userId);
  try {
      // 비즈니스 로직 실행
      logger.info("API 요청 처리 완료");
  } finally {
      MDC.clear();
  }
  ```

- **다국어 지원**: 로그 메시지는 가능한 코드나 상수로 관리하여 다국어 지원을 고려합니다.

## 4. 예외 처리 및 추적 표준

- **예외 로깅 원칙**:
  - 예외는 발생 지점에서 바로 로깅하지 않고, 적절히 처리 가능한 상위 레벨로 전파
  - 최종적으로 처리되는 지점에서 로깅
  - 동일 예외의 중복 로깅 방지

- **예외 정보 포함**: 예외 로깅 시 다음 정보를 포함합니다.
  - 예외 타입 및 메시지
  - 스택 트레이스
  - 컨텍스트 정보 (입력값, 시스템 상태 등)
  ```java
  try {
      // 비즈니스 로직
  } catch (Exception e) {
      logger.error("결제 처리 중 오류 발생. 주문ID: {}, 금액: {}", orderId, amount, e);
      throw new PaymentException("결제 처리 실패", e);
  }
  ```

- **커스텀 예외 정의**: 비즈니스 도메인에 맞는 명확한 예외 계층 구조를 정의합니다.
  ```java
  public class PaymentException extends BusinessException {
      private final String orderId;
      
      public PaymentException(String message, String orderId, Throwable cause) {
          super(message, cause);
          this.orderId = orderId;
      }
      
      public String getOrderId() {
          return orderId;
      }
  }
  ```

- **예외 분류**:
  - 복구 가능한 예외 vs 복구 불가능한 예외
  - 비즈니스 예외 vs 시스템 예외
  - 내부 예외 vs 외부 서비스 예외

- **트랜잭션 경계에서의 예외 처리**: 트랜잭션 롤백과 관련된 예외 처리 규칙을 정의합니다.

## 5. 로그 관리 및 모니터링

- **중앙 집중식 로그 시스템**: 모든 로그를 중앙 저장소에 수집합니다.
  - ELK 스택 (Elasticsearch, Logstash, Kibana)
  - Graylog
  - Datadog, New Relic, Splunk 등

- **로그 보존 정책**: 로그 유형별 보존 기간을 정의합니다.
  - 일반 로그: 30일
  - 오류 로그: 90일
  - 보안 감사 로그: 1년 이상 (규제 요구사항에 따라 조정)

- **로그 순환 정책**: 로그 파일 크기 및 순환 주기를 설정합니다.
  ```xml
  <!-- Logback 설정 예시 -->
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
      <file>logs/application.log</file>
      <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
          <fileNamePattern>logs/application.%d{yyyy-MM-dd}.log</fileNamePattern>
          <maxHistory>30</maxHistory>
          <totalSizeCap>3GB</totalSizeCap>
      </rollingPolicy>
  </appender>
  ```

- **로그 검색 및 분석**: 로그 검색을 위한 인덱싱 및 쿼리 방법을 표준화합니다.

## 6. 성능 모니터링

- **주요 지표 정의**: 다음 핵심 지표를 모니터링합니다.
  - 응답 시간 (백분위수: p50, p90, p99)
  - 처리량 (TPS, RPS)
  - 오류율
  - 리소스 사용량 (CPU, 메모리, 디스크, 네트워크)
  - JVM 지표 (힙 메모리, GC 동작)

- **분산 추적**: 마이크로서비스 환경에서 요청 흐름을 추적합니다.
  - OpenTelemetry, Jaeger, Zipkin 등 활용
  ```java
  // OpenTelemetry 예시
  Span span = tracer.spanBuilder("orderService.createOrder").startSpan();
  try (Scope scope = span.makeCurrent()) {
      // 비즈니스 로직
      span.setAttribute("orderId", orderId);
  } catch (Exception e) {
      span.recordException(e);
      span.setStatus(StatusCode.ERROR, e.getMessage());
      throw e;
  } finally {
      span.end();
  }
  ```

- **커스텀 메트릭**: 비즈니스 관련 중요 지표를 정의하고 수집합니다.
  ```java
  // Micrometer 예시
  Counter orderCounter = Counter.builder("orders.created")
      .tag("type", orderType)
      .tag("region", region)
      .register(registry);
  orderCounter.increment();
  ```

- **헬스 체크 엔드포인트**: 애플리케이션 상태 확인을 위한 API를 제공합니다.
  - `/health`: 기본 상태 확인
  - `/health/liveness`: 생존 확인
  - `/health/readiness`: 준비 상태 확인

## 7. 알림 설정

- **알림 수준 정의**:
  - INFO: 정보성 알림, 이메일 또는 대시보드
  - WARNING: 주의 필요, 채팅 채널 알림
  - CRITICAL: 긴급 대응 필요, SMS/전화 알림

- **알림 내용 표준화**: 알림에 다음 정보를 포함합니다.
  - 이벤트 설명
  - 영향받는 시스템/서비스
  - 심각도
  - 발생 시간
  - 조치 가능한 링크 (대시보드, 로그 등)

- **알림 그룹화**: 유사 알림을 그룹화하여 알림 피로를 줄입니다.

- **온콜 일정**: 알림 대응을 위한 온콜 담당자 일정을 관리합니다.

## 8. 로깅 보안 고려사항

- **민감 정보 제외**: 다음 정보는 로그에 포함하지 않습니다.
  - 비밀번호, 토큰, API 키
  - 개인식별정보(PII)
  - 금융 정보
  ```java
  // 잘못된 예:
  logger.info("로그인 요청: username={}, password={}", username, password);
  
  // 올바른 예:
  logger.info("로그인 요청: username={}", username);
  ```

- **로그 접근 제어**: 로그 시스템에 대한 접근 권한을 제한합니다.

- **로그 변조 방지**: 로그의 무결성을 보장하기 위한 조치를 취합니다.

## 9. 개발자 가이드

- **로깅 체크리스트**:
  - 모든 중요 비즈니스 이벤트에 대한 INFO 로그
  - 모든 예외에 대한 적절한 로깅
  - 성능에 영향을 주는 작업에 대한 타이밍 로그
  - 민감 정보 제외 확인
  
- **개발자 로컬 설정**: 개발 환경에서의 로깅 설정 가이드를 제공합니다.

- **로그 분석 가이드**: 일반적인 문제 해결을 위한 로그 분석 방법을 문서화합니다.

## 10. 로깅 코드 리뷰 기준

- **로그 레벨 적절성**: 각 로그 메시지의 레벨이 적절한지 확인
- **로그 메시지 품질**: 명확하고 구체적인 메시지인지 검토
- **성능 영향**: 과도한 로깅으로 인한 성능 저하 여부 확인
- **예외 처리 일관성**: 예외 처리 및 로깅이 표준을 따르는지 검토
