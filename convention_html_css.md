# HTML/CSS 코딩 컨벤션

## HTML

### 기본 스타일

#### 들여쓰기는 공백(space) 2문자를 사용한다.

```html
<!-- Bad -->
<table>
    <tr>
        <th>Name</th>
        <th>Favorite Color</th>
    </tr>
 ...
</table>

<!-- Good -->
<table>
  <tr>
    <th>Name</th>
    <th>Favorite Color</th>
  </tr>
 ...
</table>
```

#### 속성값에는 반드시 큰따옴표("")를 사용한다.

```html
<!-- Bad -->
<a href='/' class='home'>Home</a>

<!-- Good -->
<a href="/" class="home">Home</a>
```

#### 태그명, 속성명, 속성값 등에는 모두 소문자만 사용한다.

```html
<!-- Bad -->
<A HREF="/">Home</A>

<!-- Good -->
<a href="/">Home</a>
```

#### HTML 문서 형식을 명확하게 지정한다.

브라우저의 일관된 렌더링을 위해 상단에 HTML5 DOCTYPE을 명시한다. XHTML은 브라우저나 도구 지원이 HTML보다 많이 부족하며, 웹 표준의 방향성과도 더는 맞지 않으므로 권장하지 않는다.

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
</html>
```

#### 최상위 `<html>` 태그에는 lang 속성을 주어 문서의 기본 언어를 지정한다. 

스크린 리더는 lang을 통해 언어를 인식하여 자동으로 음성을 변환하거나 해당 언어에 적합한 발음을 제공한다.

> 참고: lang 속성 설명은 [HTML5 스펙](https://www.w3.org/TR/html5/dom.html#the-lang-and-xml:lang-attributes)에서 확인할 수 있다. [각 국가별 코드 리스트](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry)

```html
<html lang="ko">
  <!-- ... -->
</html>
```

#### 인터넷 익스플로러에서는 메타태그를 사용해 페이지가 특정 버전에 맞게 렌더링 되도록 지정한다.

상황에 따라 매번 다르게 렌더링 되는 것을 방지 하기 위해 최신 Edge 모드를 사용한다.

> 참고: IE 호환 모드에 대해 더 알고 싶다면 [stackoverflow 글](https://stackoverflow.com/questions/6771258/what-does-meta-http-equiv-x-ua-compatible-content-ie-edge-do)을 참고한다.

```html
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
```

#### 메타태그 charset을 사용하여 문자 인코딩 방식을 지정한다.

브라우저나 개발 도구는 해당 문서의 인코딩을 빠르고 정확하게 인식한다. 문자 인코딩은 유니코드 기반으로 하위 호환이 뛰어나 가장 널리 쓰이는 UTF-8을 권장한다.

> 참고: HTML과 CSS 인코딩 방식을 다루고 싶다면 [W3C 가이드](https://www.w3.org/International/tutorials/tutorial-char-enc/)를 참고한다.

```html
<html lang="ko">
  <meta charset="UTF-8">
</head>
```

#### Boolean 속성은 값을 따로 명시하지 않는다.

selected, disabled, checked 등의 Boolean 속성은 값을 따로 명시하지 않는다.

```html
<!-- Bad -->
<input type="text" disabled=true>

<!-- Good -->
<input type="text" disabled>

<!-- Good -->
<input type="checkbox" value="1" checked>

<!-- Bad -->
<input type="checkbox" value="1" checked=true>

<!-- Good -->
<select>
  <option value="1" selected>1</option>
</select>

<!-- Bad -->
<select>
  <option value="1" selected=true>1</option>
</select>
```

#### 불필요한 태그 작성을 피한다.

가능하다면 불필요한 태그 작성을 피해야 한다. HTML 문서의 크기가 작을수록 네트워크를 통해 전송되는 데이터 양은 줄어든다. HTML 문서의 깊이가 얕을수록 브라우저가 렌더링할 때 사용하는 렌더 트리가 가벼워지므로, 렌더링 속도가 빨라진다. 렌더링 성능에 대한 더 자세한 설명은 [FE 가이드] 성능 가이드 문서에서 확인할 수 있다.

```html
<!-- Bad -->
<span class="tui">
  <img src="...">
</span>

<!-- Good -->
<img class="tui" src="...">
```

#### 목적에 맞는 HTML 태그를 사용한다.

목적에 맞는 HTML 태그를 사용한다. 예를 들어 제목을 나타낼 경우 `<h>` 태그, 문단을 나타낼 경우 `<p>` 태그, 링크를 나타낼 경우 `<a>` 태그 등을 사용한다. 또한, HTML5에서 추가된 `<header>`, `<nav>`, `<article>` 등 시멘틱 태그를 사용하여 문서를 작성하면 접근성, 재사용성, 검색엔진 최적화(Search Engine Optimization) 등에 도움이 된다.

```html
<!-- Bad -->
<p><strong>All recommendations</strong></p>

<!-- Good -->
<h3>All recommendations</h3>

<!-- Bad -->
Some text
<br />
<br />
Some more text

<!-- Good -->
<p>Some text</p>
<p>Some more text</p>

<!-- Bad -->
<div onclick="bad();">go</div>

<!-- Good -->
<a href="good/">go</a>
```

#### 엔티티 참조는 사용하지 않는다.

엔티티 참조(Entity References)는 사용하지 않는다. 모두 같은 인코딩 방식을 따르면 작성한 문제가 그대로 나타나기 때문에 &mdash;, &rdquo;, &#x263a; 등의 엔티티 참조를 사용할 필요가 없다. 다만 HTML에서 의미가 있는 `<`나 `&` 등 문자는 엔티티 참조를 사용해야 한다.

```html
<!-- Bad -->
The currency symbol for the Euro is `"&eur;"`.

<!-- Good -->
The currency symbol for the Euro is "€".
```

#### CSS/자바스크립트 파일을 불러오는 경우 type을 명시하지 않는다.

HTML5 스펙에 따르면 CSS나 자바스크립트 파일을 불러오는 경우 type 속성의 기본값이 각각 text/css, text/javascript로 지정되어 있으므로, 속성값을 명시하지 않는다.

> 참고: [link HTML5 스펙](https://www.w3.org/TR/html5/semantics.html#the-link-element) [style HTML5 스펙](https://www.w3.org/TR/html5/semantics.html#the-style-element) [script HTML5 스펙](https://www.w3.org/TR/html5/scripting-1.html#the-script-element)

```html
<!-- Bad -->
<link rel="stylesheet" href="//uicdn.toast.com/tui.chart/latest/tui-chart.min.css" type="text/css">

<!-- Good --> 
<link rel="stylesheet" href="//uicdn.toast.com/tui.chart/latest/tui-chart.min.css">

<!-- Bad -->
<script src="//uicdn.toast.com/tui.chart/latest/tui-chart.min.js" type="text/javascript"></script>

<!-- Good -->
<script src="//uicdn.toast.com/tui.chart/latest/tui-chart.min.js"></script>
```

#### CSS는 상단, 자바스크립트는 하단에서 불러온다.

자바스크립트를 `<head>`에 포함할 경우, 자바스크립트 파일을 전부 다운로드하고, 파싱, 컴파일 할 때까지 페이지 렌더링이 지연되기 때문에 `<body>`하단에 포함 시킨다.(관련 문서: [FE 가이드] script는 문서 하단에서 include 하라)

```html
<!-- Bad -->
<!DOCTYPE html>
<html>
    <head>
        <title>HTML Page</title>
        <link rel="stylesheet" href="//uicdn.toast.com/tui.chart/latest/tui-chart.min.css" type="text/css">
        <script src="//uicdn.toast.com/tui.chart/latest/tui-chart.min.js"></script>
    </head>
    ...
</html>

<!-- Good -->
<!DOCTYPE html>
<html>
    <head>
        <title>HTML Page</title>
        <link rel="stylesheet" href="//uicdn.toast.com/tui.chart/latest/tui-chart.min.css" type="text/css">
    </head>
    <body>
        ...
        <!-- body 요소 안, 맨 마지막에 씀 -->
        <script src="//uicdn.toast.com/tui.chart/latest/tui-chart.min.js"></script>
    </body>
</html>
```

## CSS

### 기본 스타일

#### 들여쓰기는 공백 2문자를 사용한다. 

클래스, 아이디명은 카멜 케이스(camelCase), 스네이크 케이스(snake_case) 방식보다 -를 사용하는 케밥 케이스(kebab-case)를 사용한다. 가독성을 위해 선언 블록을 여는 중괄호({) 앞에는 공백 1문자를 공백으로 넣고 닫는 중괄호(})는 새로운 행에 배치한다.

```css
/* Bad */
.firstSelector {}
#buttonId {}
#button-id{
...}

/* Good */
.first-selector {}
#button-id {
  ...
}
```

#### 선언 시 : 이후 공백 1문자를 추가한다.

```css
/* Bad */
.selector {
  padding:15px; 
  margin : 15px;  
}


/* Good */
.selector {
  padding: 15px;
  margin: 15px;
}
```

#### 단일 스타일은 한 줄에 표시한다. 반대로 다중 스타일은 정확한 디버깅을 위해 한 줄에 속성을 하나씩 표시한다.

```css
/* Bad */
.selector {
  padding:15px;
}
.selector {padding: 15px;margin: 15px;}

/* Good */
.selector {padding:15px;}
.selector {
  padding: 15px;
  margin: 15px;
}
```

#### 다중 선택 시 한 줄에 선택자를 하나씩 표시한다.

```css
/* Bad */
.selector, .selector-secondary, .selector[type=text] {
  ...
}

/* Good */
.selector,
.selector-secondary,
.selector[type="text"] {
  ...
}
```

#### 모든 스타일 선언 뒤에는 세미콜론을 붙인다. 

마지막 선언은 선택 사항이지만 예외를 두어 작성할 경우 오류가 발생하기 쉽다.

```css
/* Bad */
.selector {
  color: blue;
  margin: 20px;
  padding:15px
}


/* Good */
.selector {
  color: blue;
  margin: 20px;
  padding: 15px;
}
```

#### 스타일 지정 시 아이디 대신 클래스를 사용한다.

스타일 지정을 위해서는 무조건 클래스를 사용한다. 아이디를 사용한다면 재사용성 측면뿐 아니라 선택자 우선순위가 매우 높기 때문에 예측하지 못한 동작을 야기할 수 있다. 만약 문서 내 링크 이동이나 for를 사용하는 특별한 경우에만 아이디를 사용한다.

> 참고: 우선순위를 다루는 방식을 더 알고 싶다면 [CSS wizardry의 글](https://csswizardry.com/2014/10/the-specificity-graph/)에서 확인할 수 있다.

#### 자바스크립트 Hook에서 스타일 지정을 위해 만들어진 클래스를 사용하지 않는다.

자바스크립트를 사용해 DOM 이벤트 핸들러를 등록할 때, 스타일 지정을 위해 사용된 클래스를 사용하지 않는다. CSS 스타일 지정과 자바스크립트 동작 제어는 서로 다른 책임을 갖기 때문에, 각각을 분리해서 관리하는 것이 유지보수 측면에서 유리하다. 이 경우 자바스크립트에서 쓸 클래스는 js-접두어를 붙이는 것을 권장한다.

```html
<button class="btn js-submit">submit</button>
```

#### 숫자 0 값 이후에는 불필요한 단위를 작성하지 않는다.

```css
/* Bad */
margin: 1px 0px 2px 0px;
padding: 0px;

/* Good */
margin: 1px 0 2px 0;
padding: 0;

flex: 0px; // flex-basis 컴포넌트의 경우 단위가 필요함
```

#### border가 없을 경우 none대신 0을 사용한다.

```css
/* Bad */
.no-border {border: none;}

/* Good */
.no-border {border: 0;}
```

### 색상 표현

#### 16진법으로 색상을 표현할 경우 다음 예시와 같이 가능하다면 3글자로 표현한다.

```css
/* Bad */
color: #eebbcc;

/* Good */
color: #ebc;
```

### 태그 선택자 대신 클래스 선택자를 사용한다.

#### 렌더링 성능을 위해 가능한 한 태그 선택자보다 클래스 선택자를 사용한다.

```css
/* Bad */
span { ... }
```

태그 선택자와 함께 속성 선택자(attribute selectors, eg> [class^="..."])를 사용할 수 있지만 성능 이슈가 발생하므로 피해야한다. type 선택자를 사용할 경우 큰따옴표를 사용한다.(eg> input[type="text"])

```css
/* Bad */
span[class^="main"] { ... }

/* Good */
.main { ... }
```

> 참고: type 선택자 추가 예시를 보고 싶은 경우 [해당 글](https://mdn.io/selectors)을 참고한다.

#### 선택자 길이를 짧게 유지하고 최대 3개 이상 넘지 않게 제한한다. 부모 선택자를 표시해야 한다면 꼭 필요한 경우에만 작성한다.

```css
/* Bad */
.page-container #stream .stream-item .tweet .tweet-header .username { ... }

/* Good */
.tweet-header .username { ... }
```

### 가능한 한 축약형을 사용한다.

#### padding, margin, font, background, border, border-radius 등 축약형 사용이 가능한 프라퍼티는 가능한 한 축약형을 사용한다.

```css
/* Bad */
font-family: 'Open Sans', 'Noto Serif ', sans-serif;
font-size: 100%;
line-height: 1.6;
padding-bottom: 2px;
padding-left: 1px;
padding-right: 1px;
padding-top: 0;

/* Good */
font: 100%/1.6 'Open Sans', 'Noto Serif ', sans-serif;
padding: 0 1px 2px;
```

#### 불필요하게 축약형을 과용하는 것은 피한다.

```css
/* Bad */
.element {
  margin: 0 0 10px 0;
  background: #c83636;
  background: url("back.jpg");
}

/* Good */
.element {
  margin-bottom: 10px;
  background-color: #c83636;
  background-image: url("back.jpg");
}
```

#### @import 대신 `<link>`를 사용한다.

`<link>`와 비교하여 훨씬 느리기 때문에 @import를 사용하지 않는다. 만약 `<link>` 내에서 @import를 사용할 경우 @import한 자원을 받는 동안 브라우저는 CSS를 처리할 수 없어 로딩 시간이 길어진다. 또한, 해당 CSS의 다운로드 순서로 인해 문제가 생겼을 경우 추적이 힘들다.

```html
<!-- Bad -->
<style>
  @import url("more.css");
</style>

<!-- Good -->
<link rel="stylesheet" href="core.css">
```

> 참고: 추가적으로 import 에 관한 구체적인 실험 결과를 보고 싶다면 [Steve Souders가 작성한 글](http://www.stevesouders.com/blog/2009/04/09/dont-use-import/)을 참고한다.

## Sass

### .scss 문법을 사용한다.

들여쓰기 기반 .sass문법 대신 CSS에서 사용하는 모든 문법과 기능이 완전히 호환되도록 만든 .scss 문법을 사용하도록 한다.

```scss
/* .sass 문법 */
.black-div
  background: black
  border: 1px solid #ccc
  span 
    padding: 10px;

/* .scss 문법 */
.black-div {
  background: black;
  border: 1px solid #ccc;
  span {
    padding: 10px;
  }
}
```

### 선언 순서

속성, @include, 중첩 선택자 순으로 선언한다. @include 선언 이후에는 개행을 한다.

```scss
.black-div {
  background: black;
  border: 1px solid #ccc;
  @include transition(background 0.5s ease)
    
  .icon {
    padding: 10px;
  }
}
```

### 변수명은 케밥 케이스를 사용한다.

변수명은 카멜 케이스(camelCase), 스네이크 케이스(snakecase) 방식보다 -를 사용하는 케밥 케이스(kebab-case)를 사용한다. 해당 파일에서만 쓰이는 경우 변수에 underscore(`_`)를 추가해서 사용할 수 있다.

```scss
/* Bad */
$mainColor: blue;
$main_color: blue;

/* Good */
$main-color: blue;
```

### 믹스인

믹스인은 중복되는 스타일을 분리하거나 복잡한 스타일을 추상화하는 역할 등을 위해 마치 함수처럼 사용해야 한다. 특히 인자가 없는 믹스인은, Gzip과 같은 압축 과정 없이는 불필요한 중복 코드를 만들어낼 수 있으므로 주의한다.

```scss
@mixin button($color) {
  background-color: $color;
  border-radius: 5px;
  padding: .25em .5em;
  &:hover {
    cursor: pointer;
	background-color: $color;
	border-color: $color;
  }
}
.button-a {
  @include button(#b4d455);
}
.button-b {
  @include button(#c0ffee);
}
```

위 코드는 아래 코드와 같은 의미다.

```css
.button-a {
  background-color: #b4d455;
  border-radius: 5px;
  padding: .25em .5em;
}
.button-a:hover {
  cursor: pointer;
  background-color: #b4d455;
  border-color: #b4d455;
}

.button-b {
  background-color: #c0ffee;
  border-radius: 5px;
  padding: .25em .5em;
}
.button-b:hover {
  cursor: pointer;
  background-color: #c0ffee;
  border-color: #c0ffee;
}
```

### Extend 지시자는 사용하지 않는다.

@extend는 직관적이지 않고 중첩 선택자와 함께 사용하게 될 때 문제가 발생할 수 있기 때문에 사용하지 않는다. Gzip과 믹스인을 사용하면 @extend 지시자 장점을 충분히 얻을 수 있다.

### 선택자 중첩은 최대 3단계까지만 사용한다.

3단계를 넘으면 HTML과 너무 밀접하게 엮여있거나 재사용할 수 없는 CSS를 작성하고 있을 가능성이 크다.

```scss
.wrapper {
  .container {
    .content {
      ...
    }
  }
}
```

## 정적 분석 도구

정적 분석 도구를 사용하면 코딩컨벤션에 대한 검증을 자동화할 수 있으며 문제가 되는 패턴이나 기본적인 구문 검사를 수행한다. 개발 도구와 연동하여 빠른 피드백을 얻을 수 있고 규칙 추가, 제거가 쉬워 널리 쓰이고 있다.

### stylelint

https://stylelint.io

CSS 정적 분석 도구 중 하나인 stylelint는 많이 사용되는 CSS 린트 툴 중 하나로 최신 CSS 문법을 지원하며 규칙을 쉽게 추가할 수 있다. 이 문서에서는 설치와 커맨드 라인 인터페이스(CLI)를 통한 사용법을 간략하게 알아본다.

#### 설치

npm을 이용하여 간단하게 설치 할 수 있다.

```bash
$ npm install --save-dev stylelint stylelint-config-recommended
```

이후 package.json에 스크립트를 추가하여 npm 명령어로 stylelint를 실행할 수 있다.

```json
"scripts": {
  "stylelint": "stylelint style.css"
}
```

#### 옵션

옵션은 .stylelintrc, stylelint.config.js 혹은 package.json을 이용해 적용할 수 있으며, 사용 가능한 규칙(rules) 목록과 각각에 대한 설명은 공식 규칙 문서를 통해 확인할 수 있다. ESLint처럼 기존에 정의된 설정을 가져와서 원하는 규칙만 확장해서 사용할 수도 있다.

- stylelint-config-recommend: stylelint에서 권장하는 발생 가능한 에러 규칙을 제공하는 설정 확장 모듈이다. 추가로 이 설정을 기반으로 사용자 설정을 구성하여 사용할 수 있다. 만약 Prettier를 사용중이라면 stylelint-config-recommended 사용을 권장한다.

예) 추천 설정인 stylelint-config-recommended을 적용한 후 단위로 px만 사용할 수 있는 규칙을 추가하기

```javascript
module.exports = {
  "extends": "stylelint-config-recommended",
  "rules": {
    "unit-whitelist": ["px"]
  }
}
```

#### 사용

예) 간단한 CSS 예시 파일을 만들어 em 단위를 사용했을 때 린트룰에 위반되는지 확인하기

```css
// style.css
p {
  border: 1em solid #ccc;
}
```

작성 후 `$ npm run stylelint`를 CLI에서 실행하면 Unexpected unit "em"을 검출한다.