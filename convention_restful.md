
# RESTful API 설계 컨벤션

이 문서는 RESTful API를 설계하고 구현하기 위한 모범 사례와 가이드라인을 제공합니다. 일관되고 직관적인 API는 개발자 경험을 향상시키고 시스템 통합을 용이하게 합니다.

## 목차

- [핵심 원칙](#핵심-원칙)
- [URI 설계](#uri-설계)
- [HTTP 메서드](#http-메서드)
- [상태 코드](#상태-코드)
- [요청 및 응답 형식](#요청-및-응답-형식)
- [필터링, 정렬, 페이징](#필터링-정렬-페이징)
- [버전 관리](#버전-관리)
- [인증 및 권한](#인증-및-권한)
- [에러 처리](#에러-처리)
- [HATEOAS](#hateoas)
- [캐싱](#캐싱)
- [문서화](#문서화)

## 핵심 원칙

REST(Representational State Transfer)는 다음 원칙을 기반으로 합니다:

1. **리소스 중심 설계**: 모든 것은 리소스로 식별되며 URI로 표현됩니다.
2. **HTTP 메서드 활용**: 표준 HTTP 메서드를 사용하여 리소스에 대한 작업을 표현합니다.
3. **무상태(Stateless)**: 각 요청은 독립적으로 처리되며, 서버는 클라이언트 상태를 저장하지 않습니다.
4. **표현(Representation)**: 리소스는 다양한 형식(JSON, XML 등)으로 표현될 수 있습니다.
5. **연결성(HATEOAS)**: 응답에는 관련 리소스로의 링크가 포함될 수 있습니다.

## URI 설계

URI는 리소스를 명확하게 식별하고 일관된 패턴을 따라야 합니다.

### 기본 규칙

- 명사 복수형을 사용하여 리소스 컬렉션 표현
- 소문자 사용
- 하이픈(`-`)을 사용하여 단어 구분 (언더스코어 지양)
- 파일 확장자 사용 지양
- 마지막 슬래시(`/`) 생략

```
// Bad
GET /User/1
GET /get-user/1
GET /users/get/1
GET /api/v1/users/1/

// Good
GET /users/1
GET /users/1/orders
```

### 계층 관계

자원 간의 계층 관계는 경로로 표현합니다.

```
// Bad
GET /users?id=1&getOrders=true

// Good
GET /users/1/orders
```

### 컨트롤러 리소스

표준 CRUD 작업으로 표현하기 어려운 작업은 컨트롤러 리소스를 사용할 수 있습니다.

```
// Bad
GET /users/1/make-admin

// Good
POST /users/1/roles
PATCH /users/1 (body: {"role": "admin"})

// 복잡한 작업의 경우 컨트롤러 사용 가능
POST /users/1/actions/activate
POST /orders/1/actions/cancel
```

## HTTP 메서드

각 HTTP 메서드를 올바른 의미로 사용합니다.

| 메서드 | 설명 | 멱등성 | 안전성 |
|--------|------|--------|--------|
| GET | 리소스 조회 | Yes | Yes |
| POST | 리소스 생성 또는 프로세스 실행 | No | No |
| PUT | 리소스 전체 교체 | Yes | No |
| PATCH | 리소스 부분 업데이트 | No | No |
| DELETE | 리소스 삭제 | Yes | No |

### 메서드 사용 예시

```
// 사용자 목록 조회
GET /users

// 특정 사용자 조회
GET /users/123

// 새 사용자 생성
POST /users
Body: { "name": "John Doe", "email": "john@example.com" }

// 사용자 정보 전체 교체
PUT /users/123
Body: { "name": "John Doe", "email": "john@example.com", "role": "user" }

// 사용자 정보 부분 업데이트
PATCH /users/123
Body: { "email": "newemail@example.com" }

// 사용자 삭제
DELETE /users/123
```

## 상태 코드

적절한 HTTP 상태 코드를 사용하여 응답 상태를 표현합니다.

### 주요 상태 코드

- **2xx**: 성공
  - 200 OK: 요청 성공
  - 201 Created: 리소스 생성 성공
  - 204 No Content: 성공했지만 반환할 내용 없음

- **4xx**: 클라이언트 오류
  - 400 Bad Request: 잘못된 요청
  - 401 Unauthorized: 인증 필요
  - 403 Forbidden: 권한 없음
  - 404 Not Found: 리소스 없음
  - 409 Conflict: 리소스 충돌

- **5xx**: 서버 오류
  - 500 Internal Server Error: 서버 내부 오류

### 상태 코드 사용 예시

```
// Bad
POST /users
Response: 200 OK
Body: { "success": true, "message": "User created" }

// Good
POST /users
Response: 201 Created
Body: { "id": 123, "name": "John Doe", "email": "john@example.com" }
```

## 요청 및 응답 형식

일관된 데이터 형식을 사용합니다.

### 기본 규칙

- JSON 형식을 기본으로 사용
- 요청 헤더에 `Content-Type: application/json` 명시
- camelCase 사용 (일관성 유지)
- 응답 구조 표준화

### 응답 형식 예시

```json
// 기본 응답
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2023-09-15T14:30:00Z"
}

// 컬렉션 응답
{
  "data": [
    {
      "id": 123,
      "name": "John Doe"
    },
    {
      "id": 124,
      "name": "Jane Smith"
    }
  ],
  "pagination": {
    "totalItems": 50,
    "totalPages": 5,
    "currentPage": 1,
    "pageSize": 10
  }
}
```

## 필터링, 정렬, 페이징

컬렉션 리소스 조회 시 필터링, 정렬, 페이징 기능을 제공합니다.

### 쿼리 파라미터 사용

```
// 필터링
GET /users?status=active
GET /users?role=admin&status=active

// 정렬
GET /users?sort=name
GET /users?sort=+name (오름차순)
GET /users?sort=-createdAt (내림차순)

// 페이징
GET /users?page=2&pageSize=10
GET /users?offset=20&limit=10
```

## 버전 관리

API 변경 시 이전 버전과의 호환성을 위해 버전 관리가 필요합니다.

### 버전 관리 방법

1. **URI 경로 사용**
```
GET /api/v1/users
GET /api/v2/users
```

2. **헤더 사용**
```
GET /api/users
Accept: application/vnd.company.api+json;version=1
```

3. **쿼리 파라미터 사용**
```
GET /api/users?version=1
```

URI 경로를 통한 버전 관리가 가장 명시적이고 사용하기 쉽습니다.

## 인증 및 권한

API 접근 제어를 위한 인증 및 권한 방식을 표준화합니다.

### 일반적인 방법

1. **API 키 인증**
```
GET /api/users
X-API-Key: abcd1234
```

2. **JWT 인증**
```
GET /api/users
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

3. **OAuth 2.0**
```
GET /api/users
Authorization: Bearer ACCESS_TOKEN
```

## 에러 처리

일관된 에러 응답 형식을 사용하여 클라이언트가 쉽게 처리할 수 있도록 합니다.

### 에러 응답 형식

```json
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "Invalid email format",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ],
    "requestId": "request-123456",
    "timestamp": "2023-09-15T14:30:00Z"
  }
}
```

## HATEOAS

HATEOAS(Hypermedia as the Engine of Application State)는 클라이언트가 리소스와 관련된 작업을 동적으로 발견할 수 있게 합니다.

### 링크 제공 예시

```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "_links": {
    "self": { "href": "/users/123" },
    "orders": { "href": "/users/123/orders" },
    "update": { "href": "/users/123", "method": "PATCH" },
    "delete": { "href": "/users/123", "method": "DELETE" }
  }
}
```

## 캐싱

캐싱을 사용하여 API 성능을 최적화합니다.

### 캐싱 헤더 사용

```
// 응답 헤더
Cache-Control: max-age=3600
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
Last-Modified: Wed, 15 Sep 2023 14:30:00 GMT

// 조건부 요청
GET /users/123
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

## 문서화

API를 명확하게 문서화하여 사용자가 쉽게 이해하고 사용할 수 있도록 합니다.

### 문서화 도구

- OpenAPI (Swagger)
- API Blueprint
- Postman Documentation

### 문서 내용

- 엔드포인트 설명
- 요청 파라미터 및 바디
- 응답 형식 및 상태 코드
- 인증 방법
- 예제 코드

## 결론

RESTful API를 설계할 때는 위 가이드라인을 참고하되, 프로젝트의 특성과 요구사항에 맞게 조정하는 것이 중요합니다. 일관성을 유지하고 개발자 경험을 최우선으로 고려하여 직관적이고 사용하기 쉬운 API를 설계해야 합니다.
