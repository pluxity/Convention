
# 명명 규칙 컨벤션

이 문서는 프론트엔드와 백엔드 개발에서 일관성 있게 사용할 수 있는 명명 규칙에 대한 가이드라인을 제공합니다. 명확하고 일관된 명명 규칙은 코드의 가독성을 높이고 팀원 간 협업을 원활하게 합니다.

## 목차

- [기본 원칙](#기본-원칙)
- [케이스 스타일](#케이스-스타일)
- [변수 명명](#변수-명명)
- [함수 명명](#함수-명명)
- [클래스 및 인터페이스 명명](#클래스-및-인터페이스-명명)
- [상수 명명](#상수-명명)
- [파일 및 디렉토리 명명](#파일-및-디렉토리-명명)
- [데이터베이스 관련 명명](#데이터베이스-관련-명명)
- [약어 및 축약어 사용](#약어-및-축약어-사용)
- [언어별 고려사항](#언어별-고려사항)

## 기본 원칙

1. **의미 있는 이름 사용**: 직관적이고 설명적인 이름을 사용하여 코드 자체가 문서 역할을 하도록 합니다.
2. **일관성 유지**: 프로젝트 전체에서 동일한 명명 패턴을 유지합니다.
3. **간결성**: 너무 길거나 복잡한 이름은 피하고 간결하면서도 설명적인 이름을 선택합니다.
4. **발음 가능한 이름**: 토론 시 발음하기 쉬운 이름을 사용합니다.
5. **검색 가능한 이름**: 코드 내에서 쉽게 검색할 수 있는 이름을 사용합니다.

## 케이스 스타일

각 요소 유형에 따라 일관된 케이스 스타일을 적용합니다:

| 요소 | 케이스 스타일 | 예시 |
|------|--------------|------|
| 변수 | camelCase | `userAge`, `isActive` |
| 함수/메소드 | camelCase | `calculateTotal()`, `getUserInfo()` |
| 클래스/인터페이스 | PascalCase | `UserService`, `ProductController` |
| 상수 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_BASE_URL` |
| 컴포넌트(프론트엔드) | PascalCase | `LoginForm`, `UserProfile` |
| HTML 요소 ID/클래스 | kebab-case | `nav-bar`, `user-profile` |
| 파일명(백엔드) | PascalCase 또는 camelCase | `UserService.java`, `orderRepository.js` |
| 파일명(프론트엔드) | PascalCase 또는 kebab-case | `LoginForm.jsx`, `user-profile.vue` |
| 데이터베이스 테이블 | snake_case | `user_accounts`, `order_items` |
| CSS 클래스 | kebab-case | `header-navigation`, `form-control` |

## 변수 명명

### 일반 변수
```javascript
// Bad
const x = 'John Doe';
const data = ['apple', 'banana', 'orange'];
const thing = new User();

// Good
const userName = 'John Doe';
const fruits = ['apple', 'banana', 'orange'];
const currentUser = new User();
```

### 불리언 변수
불리언 변수는 `is`, `has`, `can`, `should` 등의 접두사를 사용합니다.

```javascript
// Bad
const active = true;
const loggedIn = false;
const admin = true;

// Good
const isActive = true;
const isLoggedIn = false;
const isAdmin = true;
const hasPermission = true;
const canEdit = false;
const shouldRefresh = true;
```

### 배열 및 컬렉션
배열과 컬렉션은 복수형 명사를 사용합니다.

```javascript
// Bad
const fruit = ['apple', 'banana', 'orange'];
const userList = [user1, user2, user3];

// Good
const fruits = ['apple', 'banana', 'orange'];
const users = [user1, user2, user3];
const activeUserIds = [1, 2, 5, 9];
```

### 맵 및 객체
맵과 객체는 명사 또는 명사구를 사용합니다.

```javascript
// Bad
const userData = { name: 'John', age: 30 };

// Good
const user = { name: 'John', age: 30 };
const usersByRole = { admin: [...], editor: [...], viewer: [...] };
```

## 함수 명명

### 일반 함수
함수 이름은 동사 또는 동사구로 시작합니다.

```javascript
// Bad
function userData(userId) { /*...*/ }
function login(username, password) { /*...*/ }

// Good
function getUserData(userId) { /*...*/ }
function authenticateUser(username, password) { /*...*/ }
function calculateTotalPrice(items) { /*...*/ }
```

### 접근자 함수
getter와 setter 함수는 `get`/`set` 접두사를 사용합니다.

```javascript
// Bad
function userName() { return this.name; }
function updateAge(age) { this.age = age; }

// Good
function getName() { return this.name; }
function setAge(age) { this.age = age; }
```

### 불리언 반환 함수
불리언을 반환하는 함수는 `is`, `has`, `can` 등의 접두사를 사용합니다.

```javascript
// Bad
function admin(user) { return user.role === 'admin'; }
function completed(task) { return task.status === 'completed'; }

// Good
function isAdmin(user) { return user.role === 'admin'; }
function isCompleted(task) { return task.status === 'completed'; }
function hasPermission(user, action) { /*...*/ }
function canAccessResource(user, resource) { /*...*/ }
```

### 이벤트 핸들러
이벤트 핸들러는 `handle` 또는 `on` 접두사를 사용합니다.

```javascript
// 프론트엔드 예시
// Bad
function click() { /*...*/ }
function submit(form) { /*...*/ }

// Good
function handleClick() { /*...*/ }
function onSubmit(form) { /*...*/ }
function onUserRegistered(user) { /*...*/ }
```

### 비동기 함수
Promise나 async/await를 반환하는 함수는 접미사 또는 접두사로 명확히 표시할 수 있습니다.

```javascript
// 접미사 사용 예시
async function fetchUserData(userId) { /*...*/ }
function getUserDataAsync(userId) { /*...*/ }

// 백엔드에서 일반적으로 사용되는 방식
CompletableFuture<User> findUserByIdAsync(Long userId);
```

## 클래스 및 인터페이스 명명

### 클래스
클래스는 명사구를 사용하며 PascalCase로 작성합니다.

```javascript
// Bad
class user { /*...*/ }
class manageUsers { /*...*/ }

// Good
class User { /*...*/ }
class UserManager { /*...*/ }
class OrderProcessor { /*...*/ }
```

### 인터페이스
인터페이스는 형용사, 명사, 또는 드물게 동사로 작성할 수 있습니다.

```java
// Bad
interface UserStuff { /*...*/ }

// Good
interface Readable { /*...*/ }
interface UserRepository { /*...*/ }
interface Processor { /*...*/ }
```

## 상수 명명

상수는 UPPER_SNAKE_CASE를 사용하고, 명사구로 작성합니다.

```javascript
// Bad
const max_users = 50;
const apiUrl = 'https://api.example.com';

// Good
const MAX_USERS = 50;
const DEFAULT_TIMEOUT_MS = 5000;
const API_BASE_URL = 'https://api.example.com';
```

## 파일 및 디렉토리 명명

### 백엔드 파일
```
// Java
UserService.java
OrderRepository.java
SecurityConfig.java

// Node.js
userService.js
orderRepository.js
securityConfig.js
```

### 프론트엔드 파일
```
// React
UserProfile.jsx
LoginForm.tsx
useAuthHook.js

// Vue
UserProfile.vue
login-form.vue
auth-plugin.js
```

### 디렉토리
```
// 백엔드 (Java/Spring)
com/example/project/user/
com/example/project/order/
com/example/project/security/

// 백엔드 (Node.js)
src/modules/users/
src/modules/orders/
src/config/

// 프론트엔드
src/components/
src/pages/
src/hooks/
src/services/
```

## 데이터베이스 관련 명명

### 테이블
테이블 이름은 복수형 명사를 사용하고, snake_case로 작성합니다.

```sql
-- Bad
CREATE TABLE user (id INT PRIMARY KEY, name VARCHAR(255));
CREATE TABLE orderItem (id INT PRIMARY KEY, order_id INT);

-- Good
CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(255));
CREATE TABLE order_items (id INT PRIMARY KEY, order_id INT);
```

### 컬럼
컬럼 이름은 단수형 명사를 사용하고, snake_case로 작성합니다.

```sql
-- Bad
CREATE TABLE users (userID INT PRIMARY KEY, userName VARCHAR(255), createdAt TIMESTAMP);

-- Good
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(255),
  email VARCHAR(255),
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);
```

### 외래 키
외래 키는 참조하는 테이블과 컬럼을 명확히 나타냅니다.

```sql
-- Bad
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user INT,
  FOREIGN KEY (user) REFERENCES users(id)
);

-- Good
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## 약어 및 축약어 사용

### 일반 가이드라인
- 널리 알려진 약어를 제외하고 완전한 단어 사용을 권장합니다.
- 사용하는 약어는 프로젝트 내에서 일관되게 사용합니다.

### 허용되는 일반적인 약어
```
id - identifier
max - maximum
min - minimum
avg - average
info - information
init - initialize
auth - authentication/authorization
admin - administrator
config - configuration
params - parameters
```

### 약어 사용 예시
```javascript
// Bad
const u = getUser();
const authTkn = 'xyz123';
const n = items.length;

// Good
const user = getUser();
const authToken = 'xyz123';
const itemCount = items.length;
```

## 언어별 고려사항

### JavaScript/TypeScript
- 프론트엔드 프레임워크의 컴포넌트는 PascalCase (React: `UserProfile`, Vue: `UserProfile`)
- 파일 확장자를 포함한 파일 이름은 컴포넌트와 일치 (`UserProfile.jsx`, `UserProfile.vue`)
- React 훅은 'use' 접두사 사용 (`useState`, `useEffect`, 커스텀 훅: `useAuth`)

### Java
- 인터페이스 이름에 접두사 'I'를 붙이지 않음 (`Repository`가 `IRepository`보다 선호됨)
- 구현 클래스는 인터페이스 이름 + 구현 설명 (`UserRepository` 인터페이스의 구현은 `JpaUserRepository`)
- 테스트 클래스는 테스트 대상 클래스 이름 + 'Test' (`UserService`의 테스트는 `UserServiceTest`)

### Python
- 변수와 함수는 snake_case 사용 (`user_name`, `calculate_total()`)
- 클래스는 PascalCase 사용 (`UserService`)
- 상수는 UPPER_SNAKE_CASE 사용 (`MAX_RETRY_COUNT`)


### 데이터베이스
- MySQL/PostgreSQL: snake_case (`user_accounts`, `order_items`)
- MongoDB 컬렉션: camelCase 또는 PascalCase, 보통 복수형 사용 (`users`, `orderItems`)

