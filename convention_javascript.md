# JavaScript 코딩 컨벤션

> 참고: 이 문서는 ES5 이상을 다루며 ES6를 기준으로 작성하였다. ES5에서 지켜야할 컨벤션은 (ES5)태그를 달아 구분하였다.

## 들여쓰기

space와 tab을 섞어서 사용하지 않는다.

사용하는 개발환경에따라 탭 또는 스페이스의 들여쓰기가 다르게 보일수 있기때문에 이를 통일하지 않으면 가독성이 떨어진다. 따라서 프로젝트를 시작할 때 반드시 공백 문자와 탭 문자 둘 중 하나를 선택해야 한다. 이는 프로젝트의 성격에 따라 선택이 가능한 부분이며, 공백 문자를 사용할 경우 공백 문자 2개 또는 4개를 사용한다. 들여쓰기 규칙이 합의되면 편집기에 합의된 규칙을 적용하여 사용하길 권장한다.

> 참고: FE개발랩은 공백 문자 2개를 사용한다.

참고: [ESLint - indent](https://eslint.org/docs/rules/indent)

## 문장의 종료

한 줄에 하나의 문장만 허용하며, 문장 종료 시에는 반드시 세미콜론(;)을 사용한다.

자바스크립트는 이를 문법으로 강제하지 않지만, 종종 생각지 못한 오류를 만들고 디버깅을 어렵게 한다.

참고: [ESLint - semi](https://eslint.org/docs/rules/semi)

## 명명 규칙

카멜 케이스을 사용한다.

변수와 함수의 이름을 정하는 것도 코딩 컨벤션의 일부이다. 대표적인 표기법으로 카멜 케이스, 파스칼 표기법, 헝가리안 표기법, 스네이크 표기법이 있다. 각각 장단점이 있으며 사용하는 언어에 따라 권장사항이 다르다. FE개발랩에서는 카멜 케이스을 사용한다.

참고: [ESLint - 카멜 케이스](https://eslint.org/docs/rules/camelcase)

### 예약어를 사용하지 않는다.

```javascript
// Bad
let class;
let enum;
let extends;
let super;
let const;
let export;
let import;
```

### 상수는 영문 대문자 스네이크 표기법(Snake case)를 사용.

```javascript
SYMBOLIC_CONSTANTS;
```

### 생성자는 대문자 카멜 케이스을 사용한다.

```javascript
class ConstructorName {
  // ...
};
```

### 변수, 함수에는 카멜 케이스을 사용한다.

```javascript
// 숫자, 문자, 불린
let dog;
let variableName;

// 배열 - 배열은 복수형 이름을 사용
const dogs = [];

// 정규표현식 - 정규표현식은 'r'로 시작
const rDesc = /.*/;

// 함수
function getPropertyName() {
  // ...
}

// 이벤트 핸들러 - 이벤트 핸들러는 'on'으로 시작
const onClick = () => {};
const onKeyDown = () => {};

// 불린 반환 함수 - 반환 값이 불린인 함수는 'is'로 시작
let isAvailable = false;
```

### (지역 변수 or private 변수)명은 '_'로 시작한다.

```javascript
let _privateVariableName;
let _privateFunctionName;

// 객체일 경우
const customObjectName = {};
customObjectName.propertyName;
customObjectName._privatePropertyName;
_privateCustomObjectName;
_privateCustomObjectName._privatePropertyName;
```

### URL, HTML 같은 범용적인 대문자 약어는 대문자 그대로 사용한다.

```javascript
parseHTML
parseXML
```

## 전역 변수

### 전역 변수를 사용하지 않는다.

자바스크립트는 전역 변수에 기반을 둔다. 즉, 모든 컴파일 단위는 하나의 공용 전역 객체(window)에 로딩된다. 전역 변수는 언제든지 프로그램의 모든 부분에서 접근할 수 있기 때문에 편하지만, 바꿔 말하면 프로그램의 모든 부분에서 변경될 수 있고, 그로 인해 프로그램에 치명적인 오류를 발생시킬 수 있다.

```javascript
// Bad
myglobal = "hello";
```

### 암묵적 전역 변수를 사용하지 않는다.

```javascript
// Bad
function sum(x, y) {
  result = x + y;
  return result;
}

// Bad
function foo() {
  let a = b = 0; // let a = (b = 0);와 같다. b가 암묵적 전역이 된다.
}

// Good
function sum(x, y) {
  let result = x + y;
  return result;
}

// Good
function foo() {
  let a, b;
  a = b = 0;
}
```

## 선언과 할당

### 변수

#### 값이 변하지 않는 변수는 const를, 값이 변하는 변수는 let을 사용하여 선언한다. var는 절대로 사용하지 않도록 한다.

const를 우선하여 선언하면 "이 변수는 결코 재 할당되지 않습니다"라고 알려줌으로써 코드를 읽기 쉽게 하여 유지보수에 도움이 된다. let은 블록 범위로 할당되기 때문에 다른 많은 프로그래밍 언어에서와 같은 규칙으로 적용되어 실수를 피하는데 도움이 된다.

참고: [ESLint - no-var](https://eslint.org/docs/rules/no-var)

#### const를 let 보다 위에 선언한다.

코드가 정리되어 가독성이 좋아진다.

참고: [ESLint - prefer-const](https://eslint.org/docs/rules/prefer-const)

```javascript
// Bad - 그룹화 없음
let foo;
let i = 0;
const len = this._array.length;
let bar;

// Good
const len = this._array.length;
const len2 = this._array2.length;
let i = 0;
let j = 0;
let foo, bar;
```

#### const와 let은 사용 시점에 선언 및 할당을 한다.

const와 let으로 선언한 변수는 블록 스코프이므로 호이스팅(hoisting) 되지 않는다.

```javascript
// Bad - 블록 스코프 밖에서 변수 선언
function foo() {
  const len = this._array.length;
  let i = 0;
  let j = 0;
  let len2, item;

  for (; i < len; i += 1) {
      // ...
  }
  
  len2 = this._array2.length;
  for (j = 0, len2 = this._array2.length; j < len2; j += 1) {
      item = this._array2[j];
      // ...
  }
}

// Good 
function foo() {
  const len = this._array.length;
  for (let i = 0; i < len; i += 1) {
      // ...
  }

  // 사용 시점에 선언 및 할당
  const len2 = this._array2.length;
  for (let j = 0; j < len2; j += 1) {
      const item = this._array2[j];
      // ...
  }
}
```

#### 외부 모듈과 내부 모듈을 구분하여 사용한다.

외부 모듈과 내부 모듈을 변수 참조할 때, 선언 사이에 공백을 두면 가독성이 좋아진다.

```javascript
const lodash = require('lodash');
const $ = require(jquery);
const handlebars = require('handlebars');
const d3 = require('d3');

const pluginFactory from '../../factories/pluginFactory';
const predicate from '../../helpers/predicate';
const raphaelRenderUtil from '../../plugins/raphaelRenderUtil';
```

#### 자기 참조 할당은 하지 않는다.

자기 참조는 효과가 없으며, 불완전한 리팩터링으로 인한 오류일 수 있다.

참고: [ESLint - no-self-assign](https://eslint.org/docs/rules/no-self-assign)

```javascript
// Bad
foo = foo;

[a, b] = [a, b];

[a, ...b] = [x, ...b];

({a, b} = {a, x});

// Good
foo = bar;

let foo = foo;

[foo = 1] = [foo];
```

#### var 사용 시 반드시 함수 스코프의 시작 지점에서 선언한다. (ES5)

자바스크립트는 블록 구문을 사용하기는 하지만, 블록 유효 범위를 제공하지는 않는다. 즉, 블록 내에서 선언되기만 하면 선언된 위치에 상관없이 블록 내 어느 곳에서든 사용이 가능하다. 자바스크립트가 컴파일될 때 내부적으로 호이스팅이 발생하기 때문인데, 이로 인해 가독성이 떨어지고 오류를 찾기 어려워진다.

참고: 
- [ESLint - vars-on-top](https://eslint.org/docs/rules/vars-on-top)
- [ESLint - no-inner-declarations](https://eslint.org/docs/rules/no-inner-declarations)

```javascript
// Bad - 스코프의 시작 지점이 아닌 곳에 변수 선언
function foo() {
  // ...
  var bar = '';
  var quux = '';
}

// Good
function foo() {
  var bar = '';
  var quux = '';
  // ...
}
```

#### ES5 환경에서 변수는 var 키워드와 함께 선언되어야 하며 선언과 동시에 할당되어야 한다. (ES5)

하나의 var로 너무 많은 변수를 선언할 경우 var문을 읽지 못하거나 코드가 쉽게 지저분 해질 수 있으므로 FE개발랩에서는 항상 하나의 var에 하나의 변수를 선언한다.

```javascript
// Bad - var를 한 번만 사용하여 선언
var foo = '',
  bar = '',
  quux = '';

// Good - 변수 별 var 선언
var foo = '';
var bar = '';
var quux = '';
```

#### var 사용 시 블록 스코프 안에서 변수를 선언하지 않는다. (ES5)

var로 선언한 변수는 함수 스코프이다. for문이나 if문 블록 내에서 변수를 선언하면 블록 스코프가 적용된다고 착각하여 의도치 않은 실수를 할 수 있다.

```javascript
// Bad
var length = 100;
for (var i = 0; i < length; i += 1) {
  // ...
}

// Good
var length = 100;
var i;
for (i = 0; i < length; i += 1) {
  // ...
}

// Good
var i = 0;
var length = 100;
for (; i < length; i += 1) {
  // ...
}
```

#### var의 선언 시점과 사용 시점이 크게 떨어져 가독성이 낮아지는 경우에는 선언과 할당을 분리를 허용한다. (ES5)

선언부와 사용시점이 크게 떨어져 가독성이 낮아지는 경우에는 선언과 할당을 분리할 수 있다. 할당은 사용 시점에 수행하며, 이 경우에도 선언은 반드시 스코프의 시작 지점에서 수행한다.

```javascript
// Bad
function foo() {
  var i = 0;
  var len = this._array.length;

  for (; i < len; i += 1) {
    // ...
  }

  // statement 내에서의 var 사용 제한
  for (var j = 0, len2 = this._array2.length; j < len2; j += 1) {
    // statement 내에서의 var 사용 제한
    var item = this._array2[j];
    // ...
  }
}

// Good - 선언과 할당을 분리
function foo() {
  var i;
  var j;
  var len;
  var len2;
  var item;

  i = 0;
  len = this._array.length;
  for (; i < len; i += 1) {
    item = this._array[i];
    // ...
  }

  // 선언은 진입부에서 하고, 할당은 사용 시점에 수행
  j = 0;
  len2 = this._array2.length;
  for (; j < len2; j += 1) {
    item = this._array2[j];
    // ...
  }
}
```

#### 함수 중간에 예외처리가 있을때, 예외 처리 이후에 사용되는 var변수는 경우 선언만 집입부에서 하고 할당은 사용 시점에 수행한다. (ES5)

이러한 경우에도 선언부와 사용시점이 크게 떨어져 가독성이 낮아지는 경우이므로 변수를 사용시점에 할당한다.

```javascript
// Bad
function foo(isValid) {
  var i = 0;
  var len = this._array.length;

  if (!isValid) {
    return false;
  }

  for (; i < len; i += 1) {
    // ...
  }
}

// Good
function foo(isValid) {
  var i, len;

  if (!isValid) {
    return false;
  }

  // 선언은 진입부에서 하고, 할당은 사용 시점에 수행
  i = 0;
  len = this._array.length;
  for (; i < len; i += 1) {
    // ...
  }
}
```

#### 선언과 할당의 분리를 허용하는 경우 선언만 하는 변수는 var을 한 번만 사용하는 방식을 허용한다. (ES5)

하나의 var로 여러줄에 걸쳐 변수를 선언할 경우 코드가 쉽게 지저분해질 수 있으므로 한 줄로 선언한다.

```javascript
// Bad - 불필요하게 개행
var foo,
  bar,
  quux;

// Good - 선언만 하는 변수, 한 줄로 연결
var foo, bar, quux;

// Good
var foo;
var bar;
var quux;
```

#### 선언과 동시에 할당을 하는 변수 먼저 선언한다. (ES5)

선언과 할당을 함께하는 변수와 선언만 하는 변수가 함께 사용될 때, 선언과 동시에 할당을 하는 변수를 그룹화하여 먼저 선언하는 것이 가독성에 좋다.

```javascript
// Bad
var foo;
var bar;
var qux;
var i = 0;
var j = 0;
var len = this._array.length;
var len2 = this._array2.length;
var item;

// Bad
var i = 0, length = ary.length, j, k;

// Good
var i = 0;
var j = 0;
var len = this._array.length;
var len2 = this._array2.length;
var foo, bar, quux, item;
```

### 배열과 객체

#### 배열과 객체는 반드시 리터럴로 선언한다.

리터럴 표기법은 생성자 함수보다 짧고 명확하며 실수를 줄일 수 있다.

참고: [ESLint - no-new-object](https://eslint.org/docs/rules/no-new-object)

```javascript
// Bad
const emptyArr = new Array();
const arr = new Array(1, 2, 3, 4, 5);

// Bad - 객체 생성자 사용
const emptyObj = new Object();
const obj = new Object();

// Good
const emptyArr = [];
const arr = [1, 2, 3, 4, 5];

// Good
const emptyObj = {};
const obj = {
  pro1: 'val1', 
  pro2: 'val2'
};
```

#### 배열 복사 시 순환문을 사용하지 않는다.

복잡한 객체를 복사할 때 전개 연산자를 사용하면 좀 더 명확하게 정의할 수 있고 가독성이 좋아진다.

참고: [mdn – Spread Operator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

```javascript
const len = items.length;
let i;

// Bad
for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}

// Good
const itemsCopy = [...items];
```

#### ES5의 환경에서는 Array.prototype.slice를 사용한다. (ES5)

```javascript
// Good
itemsCopy = items.slice();
```

#### 배열의 시작 괄호 안에 요소가 줄 바꿈으로 시작되었다면 끝 괄호 이전에도 일관된 줄 바꿈 해야한다.

일관된 줄 바꿈 스타일은 협업 개발자 간 코드 가독성을 높혀준다.

참고: [ESLint - array-bracket-newline](https://eslint.org/docs/rules/array-bracket-newline)

```javascript
// Bad
var a = [1
];

// Good
var c = [1];
var d = [
    1
];
```

#### 배열의 요소중 하나라도 줄 바꿈이 있다면 배열 안의 요소는 일관되게 모두 줄 바꿈을 해주어야 한다.

참고: [ESLint - array-element-newline](https://eslint.org/docs/rules/array-element-newline)

```javascript
// Bad
const d = [1,
  2, 3];
const e = [
  function foo() {
    dosomething();
  }, function bar() {
    dosomething();
  }
];

// Good
const a = [1, 2, 3];
const b = [
  1, 
  2, 
  3
];
```

#### 객체의 프로퍼티가 1개인 경우에만 한 줄 정의를 허용하며, 2개 이상일 경우에는 개행을 강제한다.

참고: [ESLint - object-property-newline](https://eslint.org/docs/rules/object-property-newline)

```javascript
// Bad - 개행
const obj = {foo: 'a', bar: 'b'}

// Good
const obj = {foo: 'a'};

// Good
const obj = {
  foo: 'a'
};
```

#### 객체 리터럴 정의 시 콜론 앞은 공백을 허용하지 않으며 콜론 뒤는 항상 공백을 강제한다.

```javascript
// Bad
var obj = {
  foo : 'a'
}

// Good
var obj = {
  foo: 'a'
}
```

#### 객체의 메서드 표현 시 축약 메소드 표기를 사용한다.

복잡한 객체 리터럴을 보다 명확하게 정의할 수 있다.

참고: [ESLint - object-shorthanded](https://eslint.org/docs/rules/object-shorthand)

```javascript
// Bad
const atom = {
  value: 1,

  addValue: function(value) {
    return atom.value + value;
  }
};

// Good
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  }
};
```

#### 메서드 문법 사용 시 메서드 사이에 개행을 추가한다.

참고: [ESLint - lines-between-class-members](https://eslint.org/docs/rules/lines-between-class-members)

```javascript
// Bad
class MyClass {
  foo() {
    //...
  }
  bar() {
    //...
  }
}

// Good
class MyClass {
  foo() {
    //...
  }

  bar() {
    //...
  }
}
```

## 함수

### 함수 생성자를 사용하여 선언하지 않는다.

문자열로 전달되는 파라미터가 수행 시점에 eval로 처리되어 실행 속도가 느려진다.

참고: [ESLint - no-new-func](https://eslint.org/docs/rules/no-new-func)

```javascript
// Bad - 함수 생성자 사용
const doSomething = new Function('param1', 'param2', 'return param1 + param2;');

// Good - 함수 선언식 사용
function doSomething(param1, param2) {
  return param1 + param2;
}

// Good - 함수 표현식 사용
const doSomething = function(param1, param2) {
  return param1 + param2;
};
```

### 함수는 사용 전에 선언해야 하며, 함수 선언문은 변수 선언문 다음에 오도록 한다.

함수 표현식으로 생성된 함수는 호이스팅 시 값이 할당되지 않으므로 선언 이전에 사용하면 오류가 발생한다.

```javascript
// Bad - 선언 이전에 사용
const sumedValue = sum(1, 2);
const sum = function(param1, param2) {
  return param1 + param2;
};

// Bad - 선언 이전에 사용
const sumedValue = sum(1, 2);
function sum(param1, param2) {
  return param1 + param2;
};

// Good
const sum = function(param1, param2) {
  return param1 + param2;
};
const sumedValue = sum(1, 2);
```

### 즉시 실행 함수는 권장되는 패턴으로만 사용한다.

즉시 실행 함수에서 사용하는 괄호는 여러가지 형태로 표현할 수 있지만 혼란을 줄 수 있음으로 아래와 같이 한 가지 스타일로 작성한다.

```javascript
// Bad
(function() {
  // ...
})();

// Good
(function() {
  // ...
}());
```

### 블록 스코프에서는 함수 선언식을 사용하지 않는다. (ES5)

ES6 이전의 자바스크립트에서 스코프는 함수 단위였다. 블록 스코프 안에서 선언식으로 정의된 함수는 유효 범위가 함수 스코프이지만 블록 스코프 내에서만 유효하다는 오해를 준다. 반면 블록스코프 안에서 표현식으로 정의된 함수의 경우, 호이스팅(Hoisting)에 의해 선언 자체는 끌어올려지지만 할당은 블록 스코프에서 일어나기 때문에 오해의 여지가 없다.

참고: [ESLint - no-inner-declarations](https://eslint.org/docs/rules/no-inner-declarations)

```javascript
// Bad
if (condition) {
  function someFunction() {
  
  }
} else {
  function someFunction() {
  
  }
}

// Good
var someFunction;

if (condition) {
  someFunction = function() {
    // ...
  }
} else {
  someFunction = function() {
    // ...
  }
}
```

## 화살표 함수

### 함수 표현식 대신 화살표 함수를 사용한다.

화살표 함수는 별도의 this 바인딩 없이 상위 컨텍스트에 바인딩되기 때문에 함수 표현식보다 혼란이 적으며 덜 장황하고 추론이 쉽다.

참고: [ESLint - prefer-arrow-callback](https://eslint.org/docs/rules/prefer-arrow-callback)

```javascript
// Bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});

// Good
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});
```

### 화살표 함수의 파라미터가 하나이면 괄호를 생략한다.

파라미터가 하나일 때 괄호를 생략하면 화살표 함수의 장점을 살릴 수 있다.

참고: [ESLint - arrow-parens](https://eslint.org/docs/rules/arrow-parens)

```javascript
// Bad
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});

// Good
[1, 2, 3].map(x => x * x);

// Good
[1, 2, 3].reduce((y, x) => x + y);
```

### 암시적 반환을 최대한 활용한다.

함수의 본체가 하나의 표현식이면 중괄호를 생략하고 암시적 반환을 사용할 수 있다. 그 외에는 return문을 명시해야 한다.

```javascript
// Bad
[1, 2, 3].map(number => {
  const nextNumber = number + 1;
  `A string containing the ${nextNumber}.`;
});

// Good - 암시적 return을 사용
[1, 2, 3].map(number => `A string containing the ${number + 1}.`);
```

### 암시적 반환을 사용할 경우 함수 본문 전에 개행을 하지 않는다.

실수로 인한 return문 누락과 암시적 반환을 판단하는데 혼란을 피할 수 있다.

참고: [ESLint - implicit-arrow-linebreak](https://eslint.org/docs/rules/implicit-arrow-linebreak)

```javascript
// Bad
(foo) =>
  bar;

(foo) =>
  (bar);

(foo) =>
  bar =>
    baz;

// Good
(foo) => bar;

(foo) => (bar);

(foo) => bar => baz;

(foo) => (
  bar()
);
```

### Promise Executor 함수

#### Promise Executor 함수에 async 함수를 사용하지 않는다.

비동기 Excutor 함수가 throw한 오류를 잡을 수 없고, Promise가 reject되지 않아 디버깅이 어렵다.

참고: [ESLint - no-async-promise-executor](https://eslint.org/docs/rules/no-async-promise-executor)

```javascript
// Bad
const result = new Promise(async (resolve, reject) => {
  resolve(await foo);
});

// Good
const result = new Promise((resolve, reject) => {
  readFile('foo.txt', function(err, result) {
    if (err) {
      reject(err);
    } else {
      resolve(result);
    }
  });
});
```

## Destructuring

### 오브젝트의 프로퍼티에 접근할 때는 Destructuring을 이용한다.

오브젝트에서 필요한 값만을 추출하여 변수에 할당하는 방법은 직관적이며 코드를 이해하기 쉬어진다.

참고: [ESLint - prefer-destructuring](https://eslint.org/docs/rules/prefer-destructuring)

```javascript
// Bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// Bad
const first = arr[0];
const second = arr[1];

// Good
function getFullName(obj) {
  const {firstName, lastName} = obj;

  return `${firstName} ${lastName}`;
}

// Good
const [first, second] = arr;

// Good
function getFullName({firstName, lastName}) {
  return `${firstName} ${lastName}`;
}
```

### 새로운 이름으로 변수에 할당 할 때는 꼭 Destructuring을 사용하지 않아도 된다.

```javascript
// Good
const changeFirstName = user.firstName;

// Good
const {firstName: changeFirstName} = user;
```

## 템플릿 문자열

### 변수 등을 조합해서 문자열을 생성하는 경우 템플릿 문자열을 이용한다.

자바스크립트에서 문자열을 보다 쉽고 명확하게 다룰 수 있어 코드 복잡도가 낮아진다.

참고: [ESLint - prefer-template](https://eslint.org/docs/rules/prefer-template)

```javascript
// Bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// Bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// Bad - 일반적인 경우, 홑따옴표를 사용
function sayHi(name) {
  return `How are you name?`;
}

// Good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

## 클래스와 생성자

### class와 extends를 이용해서 객체 생성 및 상속을 구현한다.

prototype기반으로 상속을 구현한 것보다 문법이 훨씬 간단하다.

```javascript
// Bad
function Queue(contents = []) {
  this._queue = [...contents];
}
Queue.prototype.pop = function() {
  const value = this._queue[0];
  this._queue.splice(0, 1);
  return value;
};

// Good
class Queue {
  constructor(contents = []) {
    this._queue = [...contents];
  }
  pop() {
    const {value} = this._queue;
    this._queue.splice(0, 1);
    return value;
  }
}
```

### mixin을 제외하고 명시적으로 prototype을 호출하지 않는다.

미리 약속한 방법으로만 객체를 확장하여 예측 가능한 코드가 되도록 한다.

```javascript
// Bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function() {
  return this._queue[0];
};

// Good
class PeekableQueue extends Queue {
  peek() {
    return this._queue[0];
  }
}
```

## 모듈

### 항상 import와 export를 이용한다.

다른 모듈 로드 방법과 혼용하여 사용하면 코드의 일관성이 없어진다.

```javascript
// Best
import {es6} from './AirbnbStyleGuide';
export default es6;

// Bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;

// Good
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;
```

### wildcard import는 사용하지 않는다.

with문법을 지양해야 하는 것과 같은 이유로, 이름을 지정하지 않으면 모듈이 변경될 때마다 식별자 충돌이 발생할 수 있다.

```javascript
// Bad
import * from './AirbnbStyleGuide';

// Good
import * as AirbnbStyleGuide from './AirbnbStyleGuide';
```

### import문으로부터 직접 export하지 않는다.

한 줄로 표현되어 간결하기는 하지만, import와 export 하는 방법을 명확하게 구분함으로써 일관성을 유지하자.

```javascript
// Bad
export {es6 as default} from './airbnbStyleGuide';
```

## 블록 구문

### 한 줄짜리 블록일 경우라도 {}를 생략하지 않으며 명확히 줄 바꿈 하여 사용한다.

한 줄짜리 블록일 경우 {}를 생략할 수 있지만, 이는 코드 구조를 애매하게 만든다. 당장은 두 줄을 줄일 수 있겠지만 이후 오류 발생 확률이 높아 잠재된 위험 요소가 된다.

참고: 
- [ESLint - brace-style](https://eslint.org/docs/rules/brace-style)
- [ESLint - curly](https://eslint.org/docs/rules/curly)

```javascript
// Bad
if(condition) doSomething();

// Bad
if (condition) doSomething();
else doAnything();

// Bad
for(let prop in object) someIterativeFn();

// Bad
while(condition) iterating += 1;

// Good
if (condition) {
  // ...
}

// Good
if (condition) {
  // ...
} else {
  // ...
}
```

### 키워드와 조건문 사이에 빈칸을 사용한다.

키워드와 조건문 사이가 빼곡하면 코드를 읽기 어렵다.

참고: [ESLint - keyword-spacing](https://eslint.org/docs/rules/keyword-spacing)

```javascript
// Bad
var i = 0;
for(;i<100;i+=1) {
  someIterativeFn();
} 

// Good
var i = 0;
for(; i < 100; i+=1) {
  someIterativeFn();
} 
```

### do-while문 사용 시 while문 끝에 세미콜론을 쓴다.

```javascript
// Bad
do statement while(condition)

// Good
do {
  // ...
} while (condition);
```

### switch-case 사용 시 첫 번째 case문을 제외하고 case문 사용 이전에 개행한다.

```javascript
// Good
switch (value) {
  case 1:
    doSomething1();
    break;

  case 2:
    doSomething2();
    break;

  case 3:
    return true;

  default:
    throw new Error('This shouldn\'t happen.');
}
```

### switch-case 사용 시 각 구문은 break, return, throw 중 한 개로 끝나야 하며 default문이 없으면 // no default 표시를 해준다.

여러 케이스가 하나의 기능을 한다면 break를 생략해도 좋지만, 조금이라도 다른 기능을 포함한다면 break를 생략하지 말고 다른 방식으로 코드를 수정한다.

참고: [ESLint - no-fallthrough](https://eslint.org/docs/rules/no-fallthrough)

```javascript
// Bad - 케이스 1 과 2 가 서로 다른 처리를 하지만 break가 생략됨
switch (value) {
  case 1:
    doSomething1();

  case 2:
    doSomething2();
    break;

  case 3:
    return true;

  // no default
}

// Bad - default문이 없지만 아무런 표기가 없음
switch (value) {
  case 1:
    doSomething1();
    break;

  case 2:
    doSomething2();
    break;

  case 3:
    return true;
}

// Good - 여러 케이스가 하나의 처리를 할 때는 break생략 허용
switch (value) {
  case 1:
  case 2:
    doSomething();
    break;

  case 3:
    return true;

  // no default
}
```

## 데이터형 확인하기

### 미리 약속된 데이터형 확인법을 사용한다.

미리 약속한 판별법으로 코드를 예측하기 쉽도록 한다. FE개발랩의 코드 유틸리티인 Toast UI CodeSnippet사용을 권장한다.

```javascript
// 문자열
typeof variable === 'string'
tui.util.isString(variable)


// 숫자
typeof variable === 'number'
tui.util.isNumber(variable)


// 불린
typeof variable === 'boolean'
tui.util.isBoolean(variable)


// 객체
typeof variable === 'object'
tui.util.isObject(variable)


// 배열
Array.isArray(arrayObject)
tui.util.isArray(variable)


// 널 Null
variable === null
tui.util.isNull(variable)


// 미할당 Undefined
typeof variable === 'undefined'
variable === undefined
tui.util.isUndefined(variable)


// 엘리먼트 노드
element.nodeType === 1
tui.util.isHTMLNode(element)
```

## 조건 확인하기

### 삼중 등호 연산자인 ===, !==만 사용한다.

==이나 !=는 암묵적 캐스팅으로 타입에 관계없이 판단되어 조건문의 의도를 파악하기 어렵고 버그로 이어진다.

참고: [ESLint - eqeqeq](https://eslint.org/docs/rules/eqeqeq)

```javascript
const numberB = 777;

// Bad
if (numberB == '777') {
  // ...
}

// Good
if (numberB === 777) {
  // ...
}
```

### 미리 약속된 조건 확인법을 사용한다.

미리 약속한 판별법을 사용하면 코드를 예측하기 쉽다. FE개발랩에서 만든 코드 유틸리티인 Toast UI CodeSnippet을 사용하는 것을 권장한다.

```javascript
// 문자열 - 빈 문자열이 아닌가?
if (string) ...
if (tui.util.isNotEmpty(string)) ...

// 문자열 - 빈 문자열인가?
if (!string) ...
if (tui.util.isEmpty(string)) ...

// 배열 - 순회할 요소가 있는가?
if (array.length) ...
if (tui.util.isNotEmpty(array)) ...

// 배열 - 빈 배열인가?
if (!array.length) ...
if (tui.util.isEmpty(array)) ...

// 객체 - 순회할 속성이 있는가?
if (tui.util.isNotEmpty(object)) ...

// 객체 - 빈 객체인가?
if (tui.util.isEmpty(object)) ...

// 할당된 값이 있는가?
if (tui.util.isExisty(variable)) ...

// 참조변수가 참(true)인가?
if (variable) ...

// 참조변수가 거짓(false)인가?
if (!variable) ...
```

## 반환하기

### 함수 내에서 반환은 한 번만 한다.

특정 값을 반환해야 하는 경우, 함수 맨 마지막에서 한 번만 반환한다. 단, 예외로 빠져나가는 경우는 제외한다. 코드를 예측하기 쉬우며 흐름이 간결한 함수를 작성할 수 있다.

```javascript
// Bad
function getResult() {
  // ...
  if (condition) {
    // ...
    return someDataInTrue;
  }
  // ...
  return someDataInFalse;
}

// Allow
function foo(isValid) {
  // ...
  // 예외처리로 바로 빠져나감
  if (!isValid) {
    return;
  }
  // ...
  
  return someDataInTrue;
}

// Good
function getResult() {
  let resultData;
  // ...

  if (condition) {
    // ...
    resultData = someDataInTrue;
  } else {
    // ...
    resultData = someDataInFalse;
  }

  return resultData;
}
```

### return문 바로 위는 한 칸 비워 놓는다.

다른 명령과 return문이 붙어있으면 가독성이 좋지 않으므로 return문 전에 한 줄 띄운다

참고: [ESLint - padding-line-between-statements](https://eslint.org/docs/rules/padding-line-between-statements)

```javascript
// Bad
function getResult() {
  // ...
  return someDataInFalse;
}

// Good
function getResult() {
  // ...

  return someDataInFalse;
}
```

## 순회하기

### 반복문 사용은 일반화된 순회 메서드 사용을 권장한다.

일반화된 순회 메서드를 사용하면 실수를 줄일 수 있다.

```javascript
// Good
var i, len
for (i = 0, len = array.length; i < len; i += 1) ...

// Good
[1, 2, 3].forEach(array, function(value, index) {
  // ...
});
```

### 순회 메서드가 지원되지 않는 환경에서는 code-snippet.js의 순회 메서드 사용을 권장한다. (ES5)

```javascript
// Good
tui.util.forEachArray(array, function(value, index) {
  // ...
});
```

### for-in문 안에서는 hasOwnProperty 조건 검사를 수행한다.

예상치 않게 상속받은 프로퍼티로 인해 문제가 생길 수 있다.

```javascript
// Good
for (const prop in object) {
  if (object.hasOwnProperty(prop)) {
    // ...
  }
}
```

### 반복을 위한 변수를 미리 선언한다. (ES5)

반복을 위한 변수가 초기화 안된 상태에서 사용되는 실수를 미연에 방지할 수 있다.

```javascript
// Bad
for (var i = 0; i < array.length; i += 1) ...

// Bad
for (var i in array) ...

// Good
var i, len
for (i = 0, len = array.length; i < len; i += 1) ...

// good
var key;
for (key in object) ...
```

## 콜백 함수의 스코프

### 콜백 등 익명 함수를 사용하는 경우, 최대한 클로저 사용을 피하고 각 스코프에 알맞게 변수를 선언한다.

꼭 필요하지 않은 클로저를 사용할 경우 스코프 체인의 참조가 늘어남으로 성능이 저하되고 가독성을 떨어뜨릴 수 있다.

```javascript
// bad
let data1, data2, ...;

forEach(arr, function() {
  data1 = someFunction1();
  data2 = someFunction2();
  // ...
});

// Allow
function foo() {
  const length = ary.length;
  let i = 0;
    // ...

  forEach(ary, function(data1, data2) {
    // ...

    // 필요에 따라 외부에 변수 선언 가능 (클로저 사용 허용)
    i += (length + 2);
    // ...
  });
}

// Good
function foo() {
  // ...

  // 익명 함수의 스코프 안에서 변수 선언
  forEach(ary, function(data1, data2) {
    const data1 = someFunction1(data1);
    const data2 = someFunction2(data2);
    // ...
  });
}
```

## 주석

### 주석은 설명하려는 구문에 맞춰 들여쓰기 한다.

```javascript
// Bad
function someFunction() {
  // ...

// statement에 관한 주석
  statements
}

// Good
function someFunction() {
  // ...

  // statement에 관한 주석
  statements
}
```

### 문장 끝에 주석을 작성할 경우, 한 줄 주석을 사용하며 공백을 추가한다.

```javascript
// Bad
var someValue = data1;//주석 표시 전후 공백

// Bad
var someValue = data1; /* 여러 줄 주석 */

// Good
var someValue = data1; // 주석 표시 전후 공백
```

### 여러 줄 주석을 작성할 때는 *의 들여쓰기를 맞춘다. 주석의 첫 줄과 마지막 줄은 비워둔다.

```javascript
// Bad - '*' 표시의 정렬
/*
* 주석내용
*/

// Bad - 주석의 첫 줄에는 기술하지 않는다
...
/* var foo = '';
 * var bar = '';
 * var quux;
 */

// Good - '*' 표시의 정렬을 맞춘다
/*
 * 주석내용 
 */
```

### 코드 블럭 주석 처리를 위해서는 한 줄 주석을 사용한다.

```javascript
// Bad - 여러 줄 주석을 사용
...
/*
 * var foo = '';
 * var bar = '';
 * var quux;
 */

// Good - 한 줄 주석 사용
...
// var foo = '';
// var bar = '';
// var quux;
```

## 공백

### 키워드, 연산자와 다른 코드 사이에 공백이 있어야 한다.

빼곡한 연산자와 키워드가 있는 코드는 읽기 어렵다.

```javascript
// Bad
var value;
if(typeof str==='string') {
  value=(a+b);
}

// Good
var value;
if (typeof str === 'string') {
  value = (a + b);
}
```

### 시작 괄호 바로 다음과 끝 괄호 바로 이전에 공백이 있으면 안 된다.

```javascript
// Bad - 괄호 안에 공백
if ( typeof str === 'string' )

// Bad - 괄호 안 공백
var arr = [ 1, 2, 3, 4 ];

// Good
if (typeof str === 'string') {
  // ...
}

// Good
var arr = [1, 2, 3, 4];
```

### 콤마 다음에 값이 올 경우 공백이 있어야 한다.

콤마로 구분된 아이템 간에 간격을 두면 가독성이 향상된다. FE개발랩에서는 콤마 바로 뒤에 공백을 추가한다.

```javascript
// Bad - 콤마 뒤 공백
var arr = [1,2,3,4];

// Good
var arr = [1, 2, 3, 4];
```