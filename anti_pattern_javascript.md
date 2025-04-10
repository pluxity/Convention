# JavaScript 안티패턴

## `<script>`는 문서 하단에서 포함한다

CSS나 자바스크립트 같은 외부 파일을 `<head>` 태그 안에 모아놓는 경우가 많은데, 이는 좋은 방법이 아니다. 브라우저는 페이지를 렌더링하는 과정에서 CSS나 자바스크립트를 만나면, 렌더링을 멈추고 이 요소들을 1)다운로드하고 2)구문을 분석하고 3)컴파일한다. 자바스크립트를 많이 사용하는 페이지일수록 눈에 띌 정도로 렌더링이 지연된다.

#### 해결책

모든 자바스크립트 코드는 `<body>` 태그 안, 맨 마지막에 작성한다.

```html
<!DOCTYPE html>
<html>
  <head>
    ...
    <!-- Bad: head 태그 안에서 script 삽입 -->
    <script src="../js/jquery-3.3.1.min.js"></script>
    <script src="../js/common.js"></script>
    <script src="../js/applicationMain.js"></script>
  </head>
  <body>
    ...
    <!-- Good: body 태그 안, 맨 마지막에 삽입 -->
    <script src="../js/jquery-3.3.1.min.js"></script>
    <script src="../js/common.js"></script>
    <script src="../js/applicationMain.js"></script>
  </body>
  <html></html>
</html>
```

## 외부 리소스 사용 시 URL을 직접 사용하지 않는다

jQuery 같은 유명 오픈 소스들은 보통 바로 접근할 수 있는 URL 제공한다. 이러한 URL을 직접 사용할 경우 외부 요인(URL 변경, CDN 장애 등)이 서비스에 그대로 반영되어 장애로 이어질 수 있다.

#### 해결책

외부 URL을 직접 사용하지 않고 프로젝트에 해당 소스 파일을 다운로드해서 사용한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>HTML Page</title>
  </head>
  <body>
    ...
    <!-- Bad -->
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>

    <!-- Good -->
    <script src="../js/jquery-3.3.1.min.js"></script>
    ...
  </body>
</html>
```

## 전역 변수를 사용하지 않는다

다른 언어와 마찬가지로 자바스크립트에도 전역이 존재하며, 웹 브라우저에서 전역 객체는 window이다. 선언하지 않고 사용하는 변수, 함수 밖에서 선언한 변수는 전역 변수가 된다. 전역 변수는 어플리케이션 전역에 공유되어 어디서나 읽고 쓸 수 있다. 다른 모듈에서 동일한 이름으로 사용하면 덮어쓸 수 있어 조심해야한다.

#### 해결책

네임스페이스 패턴이나 즉시 실행 함수를 활용한다.

### 네임스페이스 패턴

네임스페이스 패턴은 애플리케이션이나 라이브러리를 위한 전역 객체를 하나 만들고 모든 기능을 이 객체에 추가하는 방법이다. 변수와 함수 선언시 함수 안이나 객체의 프로퍼티로 선언하지 않으면 전역에 추가된다. 전역에 MyApp과 같은 단 하나의 객체를 정의하고, 필요한 변수와 함수를 MyApp 안에 정의한다. 이렇게 하면 name이 window.name을 덮어쓰지 않고, sayName()도 MyApp.sayName()으로 호출되어 오류가 생겼을 때 쉽게 찾을 수 있다.

```javascript
// Bad - 전역(Global)에 name변수와 sayName()함수 추가
const name = 'Nicholas'; // window.name === 'Nicholas'
function sayName() {
  alert(name);
}

// Good - 전역 객체 MyApp을 정의하고 name변수와 sayName()함수를 그 안에 정의
const MyApp = {
  name: 'Nicholas',
  sayName() {
    alert(this.name);
  }
};

MyApp.sayName();

// Tip - 네임스페이스 확장
const nhnCloud = window.ne || {}; // NHN Cloud의 네임스페이스로 ne를 사용

// 그 하위에 서비스명을 2차 네임스페이스로 사용
nhnCloud.serviceName = nhnCloud.serviceName || {};

// 페이지별 또는 기능별 모듈명을 3차 네임스페이스로 사용
nhnCloud.serviceName.util = {...};
nhnCloud.serviceName.component = {...};
nhnCloud.serviceName.model = {...};

// 필요에 따라 4차, 5차 네임스페이스로 확장하여 사용
nhnCloud.serviceName.view.layer = {...};
nhnCloud.serviceName.view.painter = {...};
```

### 즉시 실행 함수

자바스크립트 함수는 지역 스코프(local scope)를 만들어낸다. 함수 내부에서 선언한 변수는 지역 변수가 되고 함수의 라이프 사이클과 함께 유지된다. 즉시 실행 함수는 함수를 생성과 동시에 한번만 실행된다. 참조가 없기 때문에 재실행할 수 없다.

```javascript
// Bad - 변수 today는 전역 변수가 되어 초기화 코드 이후에도 남아 있음
const today = new Date();

alert(`Today is ${today.getDate()}`);

// Good - 변수 today는 익명 함수의 지역 변수가 되므로 즉시 실행 함수 바깥에서 변수에 접근 불가
(function() {
  const today = new Date();

  alert(`Today is ${today.getDate()}`);
})();

alert(today); // Error : today is not defined

// Tip - 즉시 실행 함수로 작성된 함수 내부에 정의된 변수와 함수는 반환되는 객체를 통해 접근 가능
const nhnCloud = (function() {
  const _privateName = "NHN Cloud";

  function getName() {
    return _privateName;
  }

  function sayHello() {
    return `Hello, ${_privateName}`;
  }

  return {
    whoAmI: getName,
    say: sayHello
  };
})();

nhnCloud.whoAmI(); // NHN Cloud
nhnCloud.say(); // Hello, NHN Cloud
```

## import문은 파일의 맨 위에 선언한다

import문은 외부 모듈에서 export한 함수와 객체를 가져오는데 사용한다. import해서 사용하는 모듈을 의존성 모듈이라 부른다. ES6 모듈을 사용하면 모든 의존성 모듈은 코드가 실행되기 전에 로드된다.의존성 모듈은 선언하기 전에 사용해도 오류는 발생하지 않지만, import한 모듈을 사용할 수 있는 시점을 오해할 수 있고 import한 모듈을 직관적으로 알 수 없다.

#### 해결책

import해서 사용하는 모듈은 파일 맨 위에 작성한다.

```javascript
// Bad
foo.init();
bar.getName();

import foo from 'foo';
import bar from 'bar';


// Good - 문서의 맨 위에 작성
import foo from 'foo';
import bar from 'bar';
...
foo.init();
bar.getName();
```

## 변수 선언 없이 바로 변수를 사용하지 않는다

const, let, var 키워드 없이 선언된 변수는 전역으로 처리된다. 오류는 없지만(단, strict모드에서는 같은 코드에서도 참조 오류가 발생) 전역 환경이 오염되고 때때로 매우 찾아내기 어려운 버그를 만든다. var 키워드는 함수 스코프(function-scoped)를 가지며 코드가 실행되기 전에 호이스팅(끌어올림)되므로 변수를 선언하기 전에 호출하여도 오류는 발생하지 않는다. 반면에, const와 let 키워드와 함께 선언한 변수는 블록 스코프(block-scoped)를 가지며 호이스팅에 의한 문제가 발생하지 않는다. 일시적 사각지대(TDZ : Temporal Dead Zone)의 영향을 받아 선언 전에 같은 스코프 내에서 호출하면 오류가 발생한다.

#### 해결책

- 변수 선언 시 반드시 const, let, var 키워드를 사용한다.
- const, let 키워드를 사용할 수 없는 환경이면 var 키워드를 이용하여 변수를 선언한다.

```javascript
// Bad
const foo = "foo";
let bar = "bar";
baz = "var"; // baz는 전역적으로 선언되어 전역 환경을 오염시킴

// Good
const foo = "foo";
let bar = "bar";
let baz = "var";
```

## 배열과 객체 생성 시 생성자 함수를 사용하지 않는다

배열이나 객체를 선언할 때 생성자(constructor)를 사용할 수도 있지만, 리터럴 표기법을 사용하는 것이 간결하고 직관적이며 속도 면에서도 좋다. 실제로 자바스크립트 엔진은 리터럴 표기법에 맞게 최적화되어있다. 배열 생성자를 사용할 때 숫자 한 개를 파라미터로 넘기면 숫자에 해당하는 길이의 배열을 생성하게 된다. API 스펙과 기대하는 동작이 다소 혼동될 수 있으니 주의해야 한다.

#### 해결책

배열과 객체는 리터럴로 선언한다.

```javascript
// Bad
const emptyArr = new Array();
const emptyObj = new Object();

const arr = new Array(1, 2, 3, 4, 5);
const obj = new Object();
obj.prop1 = "val1";
obj.prop2 = "val2";

const arr2 = new Array(5);
arr2.length; // 5

// Good
const emptyArr = [];
const emptyObj = {};

const arr = [1, 2, 3, 4, 5];
const obj = {
  prop1: "val1",
  prop2: "val2"
};
```

> 참고
> - [ESLint - no-array-constructor](https://eslint.org/docs/rules/no-array-constructor)
> - [ESLint - no-new-object](https://eslint.org/docs/rules/no-new-object)

## 동등 비교 연산 시 ==를 사용하지 않는다

자바스크립트는 두 값을 비교 또는 산술하기 전에 암묵적인 형변환을 실행한다. 이 때문에 다른 타입의 데이터 간에 비교와 산술이 가능하다. 하지만 암묵적인 형변환은 전체 코드의 데이터 관리를 어렵게 만들며 때로는 연산 과정에서 발생하는 데이터 타입 오류를 덮어버린다. 등호 연산자(==)를 사용하여 비교 연산 시 코드를 작성하는 사람과 읽는 사람 모두 강제 형변환 규칙을 이해해야 하며 형변환이 발생할 수 있는 모든 경우를 고려해야 하는 단점이 있다.

#### 해결책

- 암묵적인 강제 형변환이 일어나지 않도록 삼중 등호 연산자(=== 또는 !==)를 사용한다.
- 만약 타입이 다른 데이터 간에 비교가 필요하면 명시적으로 강제 형변환 후 삼중 등호 연산자(=== 또는 !==)를 사용한다.

```javascript
// Bad
undefined == null; // true
123 == "123"; // true
true == 1; // true
false == 0; // true

// Good
123 === "123"; // false
Number("123") === 123; // true
String(123) === "123"; // true
1 === true; // false
0 === false; // false
Boolean(1) === true; // true
Boolean(0) === false; // true
undefined === null; // false
Boolean(undefined) === Boolean(null); // true

// 명시적 강제 형변환
Number("10") === 10;
parseInt("10", 10) === 10;
((String(10) === "10" + "10") === 10(123).toString()) === "123";
Boolean(null) === false;
Boolean(undefined) === false;
```

> 참고
> - [ESLint- eqeqeq](https://eslint.org/docs/rules/eqeqeq)

## 중괄호({})를 생략하지 않는다

if/while/do/for 문 사용 시 한 줄짜리 블록이면 중괄호({})를 생략하지 않는 것이 좋다. 중괄호가 없을 경우에 제어문의 동작 범위를 한눈에 파악하기 힘들기 때문이다.

#### 해결책

한 줄짜리 블록에도 {}를 사용하여 명확하게 작성한다.

```javascript
// Bad
if (condition) doSomething();

if (condition) doSomething();
else doAnything();

for (let prop in object) someIterativeFn();

while (condition) iterating += 1;

// Good
if (condition) {
  doSomething();
}

if (condition) {
  doSomething();
} else {
  doAnything();
}

for (let prop in object) {
  someIterativeFn(object[prop]);
}

while (condition) {
  iterating += 1;
}
```

> 참고
> - [ESLint - curly](https://eslint.org/docs/rules/curly)

## parseInt는 두 번째 파라미터인 기수를 생략하지 않는다

parseInt는 문자열을 정수로 바꿔주는 함수이다. 첫 번째 파라미터는 정수로 바꿀 문자열이고 두 번째 파라미터는 기수(진법)이다. 기수가 생략될 경우 브라우저는 변환될 숫자 형식을 자체적으로 판단하며, 브라우저에 따라 다르게 해석될 수 있다. (문자열이 '0x' 또는 '0X'로 시작하면16진수로, '0'으로 시작하면 8진수 또는 10진수로 간주한다.)

#### 해결책

- 항상 두 번째 파라미터를 명시하여 오류를 미리 예방한다.
- 10진수로 변환이 필요한 경우라면 Number()를 사용하는 것이 속도 측면에서 이득이다.

```javascript
// Bad
const month = parseInt("08");
const day = parseInt("09");

// Good
const month = parseInt("08", 10);
const day = parseInt("09", 10);

// Tip : 10진수로 변환하는 경우라면 'Number()'를 사용하거나 '+'연산자를 붙이는 것이 더 빠름
const month = Number("08");
const day = +"09";
```

> 참고
> - [ESLint - radix](https://eslint.org/docs/rules/radix)

## switch문에서 break를 생략하지 않는다

switch문의 각 case절은 break 키워드를 만나면 해당 case절을 벗어난다. break 키워드를 생략하면, break 키워드를 만날때까지 다음 case절을 연달아 실행하는데, 해당 코드를 읽는 사람에게는 생략이 의도인지, 실수인지 확인하기 힘든 코드가 된다. 예를 들어 첫번째 case절에서 A()함수를 호출하고 break을 생략한 뒤 두번째 case절에서 B()를 호출하면 두번째 case절은 A()와 B()를 둘 다 수행한다. 이 경우 코드 가독성에 좋지 않은 영향을 주고 혹시라도 break 키워드를 실수로 작성하지 않았을 때 버그의 원인을 찾기 힘들다. 그러므로 case절 내부에는 반드시 break키워드를 작성하도록 한다. 하지만, 다수 case절을 한번에 처리하는 경우는 예외로 한다.

#### 해결책

- break를 생략하지 않는다. (단, 다수의 case절이 동일한 기능을 수행할 경우는 break를 생략할 수 있다.)
- 어떠한 case에도 해당하지 않으면 default를 사용하여 기본 동작을 설정해주도록 한다.

```javascript
// Bad
switch (foo) {
  case 1:
    A()
  case 2:
    B();
    break;
  ...
}

// Good
switch (foo) {
  case 1:
    A()
    break;
  case 2:
    C();  // A(), B()에서 하는 기능을 모두 포함한 C함수
    break;
  ...
}

// Good - 다수의 case절이 동일한 기능을 수행할 경우 break 생략 가능
switch (foo) {
  case 1:
  case 2:
    doSomething();
    break;
  ...
}

// Good
switch (foo) {
  case 1:
    doSomething();
    break;
  case 2:
    doSomethingElse();
    break;
  ...
  default:
    defaultSomething();
}
```

> 참고
> - [ESLint - no-fallthrough](https://eslint.org/docs/rules/no-fallthrough)
> - [ESLint - default-case](https://eslint.org/docs/rules/default-case)

## 배열의 순회는 for-in을 사용하지 않는다

보통 객체(Object)의 프로퍼티 순회가 필요할때는 for-in을 사용한다. 자바스크립트의 배열(Array)도 객체지만 배열의 요소를 순회할 때는 for-in을 사용하지 않아야 한다. for-in은 프로토타입 체인에 있는 모든 프로퍼티를 순회하므로 for를 사용할 때보다 훨씬 느리다. 게다가 순회 순서는 브라우저에 따라 다르게 구현되어 있어서 배열의 요소 순회가 늘 index 순서대로 수행되지 않을 수 있다.

#### 해결책

배열을 순회할 때는 for문을 사용한다.

```javascript
// Bad
const scores = [70, 75, 80, 61, 89, 56, 77, 83, 93, 66];
let total = 0;

for (let score in scores) {
  total += scores[score];
}

// Good
const scores = [70, 75, 80, 61, 89, 56, 77, 83, 93, 66];
let total = 0;
const { length } = scores;

for (let i = 0; i < length; i += 1) {
  total += scores[i];
}
```

## 배열의 요소를 삭제할 때 delete를 사용하지 않는다

보통 객체(Object)의 프로퍼티를 삭제할 때 delete를 사용한다. 단순히 undefined로 설정되는 것이 아니라 프로퍼티 자체가 완전히 삭제되어 더 이상 존재하지 않는다. 자바스크립트에서는 배열(Array)도 객체이기때문에 delete를 사용할 수 있지만, 객체의 프로퍼티 삭제와 조금 다르게 동작한다. 배열에서 요소가 완전히 삭제되어 배열의 길이가 줄어들 것 같지만, 실제로는 해당 요소 값이 undefined가 될 뿐 배열 길이는 줄어들지 않는다.

#### 해결책

배열의 요소를 삭제할 때는 Array.prototype.splice()를 사용하거나 배열의 length 프로퍼티를 변경한다.

```javascript
// Bad
const numbers = ["zero", "one", "two", "three", "four", "five"];
delete numbers[2]; // ['zero', 'one', undefined, 'three', 'four', 'five'];

// Good
const numbers = ["zero", "one", "two", "three", "four", "five"];
numbers.splice(2, 1); // ['zero', 'one', 'three', 'four', 'five'];

// Tip - 배열 길이를 줄이고 싶다면 length를 사용
const numbers = ["zero", "one", "two", "three", "four", "five"];
numbers.length = 4; // ['zero', 'one', 'two', 'three'];
```

## 순회와 관련 없는 작업을 반복문 안에서 처리하지 않는다

반복문은 주어진 조건 표현식이 true로 평가되는 동안 실행을 반복한다. 반복 작업은 성능에 영향을 미치므로, 코드를 리팩토링할 때 첫 번째로 순환문의 최적화 작업을 고려한다. 동일한 값을 반복적으로 할당하는 것처럼 순회와 상관없는 작업이 반복문 안에서 이뤄지지 않도록 주의한다.

#### 해결책

순회와 관련된 작업만 수행하도록 반복문을 최적화한다.

```javascript
// Bad
for (let i = 0; i < days.length; i += 1) {
  const today = new Date().getDate();
  const element = getElement(i);

  if (today === days[i]) {
    element.className = "today";
  }
}

// Good
const today = new Date().getDate();
const { length } = days;
let element;

for (let i = 0; i < length; i += 1) {
  if (today === days[i]) {
    element = getElement(i);
    element.className = "today";
    break;
  }
}
```

## 반복문에서 continue를 사용하지 않는다

더글라스 크락포드는 "리팩토링을 통해 continue를 제거했을 때 성능이 향상되지 않은 경우를 본 적이 없다"고 말했을 정도로 continue사용 유무는 성능에 큰 영향을 미친다. 반복문 안에서 continue를 사용하면 자바스크립트 엔진에서 별도의 실행 컨텍스트를 만들어 관리한다. 이러한 반복문은 전체 성능에 영향을 주므로 사용하지 않는다. continue를 잘 사용하면 코드를 간결하게 작성할 수 있지만, 과용하면 디버깅 시 개발자의 의도를 파악하기 어렵고 유지 보수가 힘들다.

#### 해결책

반복문 내부에서 특정 코드의 실행을 건너뛸 때는 조건문을 사용한다.

```javascript
// Bad
let loopCount = 0;

for (let i = 1; i < 10; i += 1) {
  if (i > 5) {
    continue;
  }
  loopCount += 1;
}

// Good
for (let i = 1; i < 10; i += 1) {
  if (i <= 5) {
    loopCount += 1;
  }
}
```

## try-catch는 반복문 안에서 사용하지 않는다

흔히 예외 처리를 위해 try-catch를 사용한다. try-catch를 반복문 안에서 사용하면, 순회가 반복될 때마다 런타임의 현재 스코프에서 예외 객체 할당을 위한 새로운 변수가 생성된다.

#### 해결책

try-catch를 감싼 함수를 만들고, 반복문 내부에서 이 함수를 호출한다.

```javascript
// Bad
const {length} = array;

for (let i = 0; i < length; i += 1) {
  try {
    ...
  } catch (error) {
    ...
  }
}

// Good
const {length} = array;

function doSomething() {
  try {
    ...
  } catch (error) {
    ...
  }
}

for (let i = 0; i < length; i += 1) {
  doSomething();
}
```

## 같은 DOM 엘리먼트를 반복해서 탐색하지 않는다

getElementById, getElementsByTagName, querySelector는 DOM 엘리먼트를 탐색하는데 사용하는 API이다. DOM 탐색은 비용이 들기 때문에 한 번 탐색하는 것보다 여러 번 탐색할 경우 성능이 저하된다.

#### 해결책

탐색 비용을 절약하기 위해 이미 탐색한 엘리먼트는 캐시하여 사용한다.

```javascript
// Bad
const className = document.getElementById("result").className;
const clientHeight = document.getElementById("result").clientHeight;
const scrollTop = document.getElementById("result").scrollTop;
document.getElementById("result").blur();

// Good
const el = document.getElementById("result");
const { className, clientHeight, scrollTop } = el;
el.blur();
```

## DOM 변경을 최소화 한다

innerHTML 속성 또는 appendChild 메서드를 사용할 때 DOM 변경이 발생한다. DOM 트리 변경은 비용이 들기 때문에 한 번 DOM을 변경하는 것보다 여러 번 DOM을 변경하는 것이 성능에 좋지 않다.

#### 해결책

여러 번 DOM 변경이 필요한 경우 모든 변경 내용을 한번에 반영하여 DOM 변경을 최소화 한다.

```javascript
const el = document.getElementById("bookmark-list");

// Bad
myBookmarks.forEach(bookmark => {
  el.innerHTML += `<li><a href="${bookmark.url}">${bookmark.name}</a></li>`;
});

// Good
const html = myBookmarks
  .map(bookmark => `<li><a href="${bookmark.url}">${bookmark.name}</a></li>`)
  .join("");

el.innerHTML = html;
```

## 불필요한 레이아웃을 발생시키지 않는다

브라우저에서 생성된 DOM 노드는 레이아웃 값(너비, 높이, 위치)을 변경 시 영향받는 모든 노드(자기 자신, 자식 노드, 부모 노드, 조상 노드)의 값을 재계산하여 렌더 트리를 업데이트 한다. 이러한 과정을 리플로우(reflow) 또는 레이아웃(layout)이라 한다.

레이아웃이 발생될 때:
- 페이지 초기 렌더링 시
- 윈도우 리사이징 시
- 노드 추가 및 삭제 시
- 엘리먼트 크기 및 위치 변경 시
- 특정 프로퍼티(offsetHeight, offsetTop...)를 읽고 쓸 때
- 메서드(getClientRects(), getBoundingClientRect()...)를 호출할 때

강제 레이아웃이 연속하여 빠르게 실행되는 것을 레이아웃 스래싱이라 하며, 브라우저가 레이아웃을 몇 번이고 되풀이해서 재계산하기 때문에 성능에 좋지 않은 영향을 준다

#### 해결책

반복문 안에서 강제 레이아웃을 수행하는 프로퍼티나 메서드를 호출해야 하는 경우, 해당 값을 반복문 밖에서 캐시하여 사용한다.

```javascript
// Bad
function resizeWidth(paragraphs, box) {
  const { length } = paragraphs;

  for (let i = 0; i < length; i += 1) {
    paragraphs[i].style.width = `${box.offsetWidth}px`;
  }
}

// Good
function resizeWidth(paragraphs, box) {
  const { length } = paragraphs;
  const width = box.offsetWidth;

  for (let i = 0; i < length; i += 1) {
    paragraphs[i].style.width = `${width}px`;
  }
}
```

> 참고
> - [레이아웃에 영향을 미치는 요소](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)
> - [FE 가이드] 성능

## 이벤트는 인라인 방식으로 사용하지 않는다

자바스크립트 개발 초기에는 DOM 엘리먼트에 리스너를 등록할 때 인라인 방식으로 사용하였다. 아래 예제 코드에서 doSomething()은 외부 자바스크립트 파일에 함수이다. 만약 doSomething의 이름을 바꾸거나 버튼을 클릭했을 때 호출할 함수를 바꾸려면 자바스크립트와 HTML 파일 모두를 수정해야 한다. HTML과 자바스크립트가 서로 의존 관계를 만들어 작은 변경에도 수정 범위가 커지고 유지보수와 디버깅이 어려워진다. 또한 하나의 엘리먼트에 이벤트 리스너를 여러 개 등록할 수 없다.

#### 해결책

인라인 자바스크립트 코드는 HTML에 분리해서 사용한다.

```html
<!-- Bad -->
<button onclick="doSomething()" class="action-btn">Click Me</button>

<!-- Good -->
<button class="action-btn">Click Me</button>
```

```javascript
// btn-event.js
const btn;

function doSomething() {
  ...
}
...
// Good
btn = el.querySelector('.action-btn');
btn.addEventListener('click', doSomething, false);
```

## eval()을 사용하지 않는다

eval()은 문자로 표현된 자바스크립트 코드를 실행하는 함수이다. 파라미터로 문자열을 받아 호출자(caller)의 지역 스코프에서 즉시 실행한다. eval에서 문자열로 정의한 변수나 함수는 구문 분석 단계가 아니라, 코드가 실행되는 시점에 실제 변수나 함수가 된다. 그래서 프로그램 실행 중 구문분석기가 새로 기동 되어야 하는데 상당한 부하를 만들어 프로그램 실행 속도를 현저히 느리게 한다. 그리고 사용자 입력 또는 네트워크로 들어온 문자열을 eval()로 수행할 경우 서비스 전체에 심각한 보안 문제를 일으킬 수 있다.

#### 해결책

- eval을 절대 사용하지 말아야 한다.
- eval을 사용하지 않아도 의도한 동작을 코드로 작성할 수 있다.

```javascript
const nhnCloud = {
  name: "NHN Cloud",
  lab: "FE Dev Lab",
  memberCount: 10
};
const propName = "name";

// Bad
eval(`nhnCloud.${propName}`); // NHN Cloud

// Good
nhnCloud[propName]; // NHN Cloud
```

> 참고
> - [ESLint - no-eval](https://eslint.org/docs/rules/no-eval)

## with()를 사용하지 않는다

with문은 특정 객체에 반복해서 접근할 때 간편함을 제공한다. 하지만 의도와는 다르게 많은 문제점을 낳고 있어 사용하지 않는 것이 좋다. 다음은 with 사용 예제이다.

```javascript
function doSomething(value, obj) {
  ...
  with(obj) {
    value = 'which scope is this?'
  }
}
```

with절의 구문은 아래 코드와 같은 의미이다.

```javascript
value = "which scope is this?";
obj.value = "which scope is this?";
```

어떤 코드로 실행되는지 코드만 봐서는 알 수 없다. 코드가 실행될 때마다 다르게 실행될 수 있고 프로그램이 실행되는 동안에도 달라질 수 있다. 의도하는 바가 무엇인지 명확히 알 수 없고 어떻게 실행될지 예측할 수 없다. 즉, 프로그램이 원하는 방향으로 제대로 실행될 것이라고 확신할 수 없다. 이 외에도 with문은 실행할 때마다 새로운 스코프를 생성하여 자원을 추가로 소모하고, 자바스크립트 내부에서 수행되는 변수 탐색 최적화를 방해하여 실행 속도를 현저히 떨어뜨린다.

#### 해결책

특정 객체를 반복해서 접근해야 한다면 새로운 변수에 캐시하여 사용한다.

```javascript
// Bad
with (document.getElementById("myDiv").style) {
  background = "yellow";
  color = "red";
  border = "1px solid black";
}

// Good
const { style } = document.getElementById("myDiv");
style.background = "yellow";
style.color = "red";
style.border = "1px solid black";
```

> 참고
> - [ESLint - no-with](https://eslint.org/docs/rules/no-with)

## setTimeout, setInterval 사용 시 콜백 함수는 문자열로 전달하지 않는다

setTimeout과 setInterval은 일정 시간 후에 첫 번째 파라미터로 받은 콜백 함수를 실행한다. 첫 번째 파라미터를 문자열로 전달할 수 있는데, 내부에서 eval로 처리되어 실행 속도가 느려진다.

#### 해결책

setTimeout, setInterval 사용 시 콜백 함수는 함수를 직접 전달한다.

```javascript
// Bad
function callback() {
  ...
}

setTimeout('callback()', 1000);

// Good (1)
function callback() {
  ...
}

setTimeout(callback, 1000);

// Good (2)
setTimeout(() => {
    ...
}, 1000);
```

> 참고
> - [ESLint - no-implied-eval](https://eslint.org/docs/rules/no-implied-eval)

## 함수 생성자 new Function()은 사용하지 않는다

많이 사용하는 방법은 아니지만, 함수 생성자를 이용해서 함수를 선언할 수 있다. 이 경우, 문자열로 전달되는 파라미터가 수행 시점에 eval로 처리되어 실행 속도가 느려진다.

#### 해결책

함수 선언 시 함수 선언식 또는 함수 표현식을 사용한다.

```javascript
// Bad
const doSomething = new Function("param1", "param2", "return param1 + param2;");

// Good (1) - 함수 선언식
function doSomething(param1, param2) {
  return param1 + param2;
}

// Good (2) - 함수 표현식
const doSomething = function(param1, param2) {
  return param1 + param2;
};
```

> 참고
> - [ESLint - no-new-func](https://eslint.org/docs/rules/no-new-func)

## 네이티브 객체는 확장하거나 오버라이드 하지 않는다

자바스크립트는 동적인 특징이 있어 이미 선언된 객체의 프로퍼티를 추가, 삭제, 변경할 수 있다. Object.defineProperty를 사용하면 프로퍼티 속성을 지정할 수 있으며, 설정에 따라 프로퍼티 추가, 수정, 삭제가 불가능하게 할 수도 있다. 네이티브 객체의 프로토타입은 프로퍼티를 추가하거나 기존 프로퍼티를 재정의할 수 있다. 하지만 네이티브 객체를 확장하거나 이미 정의된 메서드를 재정의하면 네이티브 객체의 기본 동작을 기대한 다른 개발자에게 혼란을 줄 수 있다. 같은 이름의 메서드가 어떤 브라우저에서는 지원되고 어떤 브라우저에서는 지원되지 않는 상황에서 충돌이 발생할 수 있다. 자칫하면 네이티브 메서드를 실수로 덮어쓸 수도 있으며, 코드 내 예측할 수 없는 오류를 만들 수 있다.

#### 해결책

- 네이티브 객체는 절대 수정하지 않는다.
- 만약 필요한 메서드가 있다면 네이티브 객체의 프로토타입에 작성하지 않고 함수로 만들거나 새로운 객체를 만들어 네이티브 객체와 상호작용한다.

```javascript
const o = {
  x: 1,
  y: 2
};

// Bad
Object.prototype.getKeys = function() {
  const keys = [];

  for (let key in this) {
    if (this.hasOwnProperty(key)) {
      keys.push(key);
    }
  }

  return keys;
};

o.getKeys();

// Good
function getKeys(obj) {
  const keys = [];

  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      keys.push(key);
    }
  }

  return keys;
}

getKeys(o);
```

> 참고 몽키패칭(monkey-patching): 네이티브 객체나 함수를 다른 객체나 함수로 확장하는 것을 몽키패칭이라 한다. 하지만 이는 캡슐화를 망치고 표준이 아닌 기능을 추가해 네이티브 객체를 오염되므로 사용하지 않는다. 이런 위험에도 불구하고, 신뢰성 있고 매우 중요한 몽키패칭의 특별한 한가지 사용법이 있는데, 바로 폴리필(polyfill)이다. 폴리필은 Array.prototype.map과 같이 자바스크립트 엔진에 새롭게 추가된 기능이 없는 경우, 비슷한 동작을 하는 다른 함수로 대체하는 것을 말한다. 폴리필과 같이 자바스크립트 기능의 호환성 유지 목적을 제외하고는 어떤 경우에도 네이티브 객체의 확장은 옳지 않다.

```javascript
// 폴리필(Polyfill) 예제
if (typeof Array.prototype.map !== "function") {
  Array.prototype.map = function(f, thisArg) {
    const result = [];
    const { length } = this;

    for (let i = 0; i < length; i += 1) {
      result[i] = f.call(thisArg, this[i], j);
    }

    return result;
  };
}
```

## 단항 증감 연산자를 사용하지 않는다

단항 증감 연산자를 사용하면 연산이 먼저인지, 값 할당이 먼저인지, 연산의 결과를 한눈에 파악하기 어렵다.

#### 해결책

값 할당 연산자를 사용하여 읽기 쉬운 코드로 작성한다.

```javascript
let num = 0;
const { length } = arr;

// Bad
for (let i = 0; i < length; i++) {
  num++;
}

// Good
for (let i = 0; i < length; i += 1) {
  num += 1;
}
```

> 참고
> - [ESLint - no-plusplus](https://eslint.org/docs/rules/no-plusplus)

## this에 대한 참조를 저장하지 않는다

this는 함수 실행 시점에 결정된다. 어떤 함수 내부에서 또 다른 함수를 호출하면 그 함수의 this는 상위 함수의 this와 같지 않다. 소스 코드 작성 시, 상위 함수 컨텍스트의 this를 참조해야 하는 경우가 있다. 비슷한 이름의 참조 변수(that, self, me...)를 만들고 내부 함수의 클로저로 사용하여 상위 함수의 this를 내부 함수의 전달할 수 있다. this와 비슷한 이름의 참조 변수를 사용하는 것은 개발자에게 혼란을 줄 수 있다.

#### 해결책

참조 변수를 따로 선언하지 말고 Function.prototype.bind 함수나 화살표 함수를 사용한다.

```javascript
// Bad
function() {
  const self = this;

  return function() {
    console.log(self);
  };
}

function() {
  const that = this;

  return function() {
    console.log(that);
  };
}

function() {
  const _this = this;

  return function() {
    console.log(_this);
  };
}

// Good
function printContext() {
  return function() {
    console.log(this);
  }.bind(this);
}

function printContext() {
  return () => console.log(this);
}
```

## 문장의 끝은 세미콜론(;)을 생략하지 않는다.

문장 끝 세미콜론(;)을 생략하지 않는다. 세미콜론 생략하면 자바스크립트 구문분석기가 세미콜론을 자동으로 삽입해주는데 의도하지 않았던 코드(전혀 다른 코드)로 해석되어 예기치 못한 동작이 발생할 수 있다. 그리고 자바스크립트 구문분석기가 세미콜론의 위치를 계산하고 삽입하는데 추가 비용이 발생한다.

#### 해결책

문장의 끝은 세미콜론을 사용한다.

## 변수와 함수는 선언 전에 사용하지 않는다 (ES5)

ES5 이하 버전에서 블록 구문을 사용하지만, 블록 유효범위를 제공하지 않는다. 블록 내에서 var 키워드를 사용해 변수를 선언되면, 선언된 위치에 상관없이 함수 스코프 내 어느 곳에서든 사용할 수 있다. var 키워드를 사용해 선언한 변수, function 키워드를 사용해 선언한 함수는 자바스크립트가 실행되고 컴파일될 때 호이스팅(끌어올림)이 되기 때문이다. 이로 인해 코드 가독성이 떨어지며 오류를 찾기 힘들다.

#### 해결책

함수는 사용하기 전에 선언하고, 변수는 var 키워드와 함께 상단에 선언한다.

```javascript
// Bad
doSomething();

function doSomething() {
  foo1 = foo2;
  ...
  var foo1 = 'value1';
  foo3 = foo4;
  ...
  var foo3;
  ...
  var foo4 = 'value4';
  var foo2;
}

// Good
function doSomething() {
  var foo1 = 'value1';
  var foo4 = 'value4';
  var foo3 = foo4;
  var foo2;
  ...
  foo1 = foo2;
}

doSomething();
```

## 배열 순회 시 매번 array.length속성을 참조하지 않는다 (Legacy)

for문은 주어진 조건 표현식이 true로 평가되는 동안 실행을 반복한다. 매 순회 시 조건 표현식을 다시 평가한다. 100번의 순회가 있었다면 100번의 평가가 수행된다. for문에서 반복 횟수만큼 매번 array.length속성을 참조하는 것은 비효율적이다. (최신 브라우저에서는 array.length를 캐시하지 않아도 성능에 큰 차이가 없지만 IE10 이하의 구형 브라우저에서는 성능 차이가 크다.)

#### 해결책

IE10 이하의 구형 브라우저에서 for문 사용 시 배열 길이는 캐시하여 사용한다.

```javascript
// Bad
for (var i = 0; i < array.length; i += 1) {
  ...
}

// Good
var len = array.length;

for (var i = 0; i < len; i += 1) {
  ...
}
```