# Java 코딩 컨벤션

## 1. 파일 공통 요건

### 1.1. 파일 인코딩은 UTF-8
**[encoding-utf8]**

모든 소스, 텍스트 문서 파일의 인코딩은 UTF-8로 통일한다.

### 1.2. 새줄 문자는 LF
**[newline-lf]**

Unix 형식의 새줄 문자(newline)인 LF(Line Feed, 0x0A)을 사용한다. Windows 형식인 CRLF가 섞이지 않도록 편집기와 GIT 설정 등을 확인한다.

Git을 쓴다면 .gitattributes 파일 안에 정책을 선언해서 지정된 새줄 문자로 강제 변환하거나 예외가 될 확장자를 지정할 수 있다. 아래는 그 예시이다.

```
*.c text eol=lf
*.cpp text eol=lf
*.h text eol=lf

# exception for visual studio project configuration
*.sln text eol=crlf
*.vs text eol=crlf
*.csproj eol=crlf
*.props eol=crlf
*.filters eol=crlf
```

.gitattributes 파일은 디렉토리별로 지정할 수 있다. 전체 프로젝트에 적용한다면 최상위 디렉토리에 둔다.


파일의 마지막은 새줄 문자 LF로 끝나야 한다.

## 2. 이름 (Naming)

### 2.1. 식별자에는 영문/숫자/언더스코어만 허용
**[identifier-char-scope]**

변수명, 클래스명, 메서드명 등에는 영어와 숫자만을 사용한다. 상수에는 단어 사이의 구분을 위하여 언더스코어(_)를 사용한다. 정규표현식 `[^A-Za-z0-9_]`에 부합해야 한다.

### 2.2. 한국어 발음대로의 표기 금지
**[avoid-korean-pronounce]**

식별자의 이름을 한글 발음을 영어로 옮겨서 표기하지 않는다. 한국어 고유명사는 예외이다.

**나쁜 예:** 
```java
moohyungJasan (무형자산)
```

**좋은 예:** 
```java
intangibleAssets (무형자산)
```

### 2.3. 대문자로 표기할 약어 명시
해당 사항은 정리 후에 Confluence에 문서화를 진행한다.
**[list-uppercase-abbr]**

클래스명, 변수명에 쓰일 단어 중 모든 글자를 대문자로 표기할 약어의 목록을 프로젝트별로 명시적으로 정의한다.

약어의 대소문자 표기는 JDK의 클래스나 라이브러리들 사이에서도 일관된 규칙이 없다. `javax.xml.bind.annotation.XmlElement`처럼 약어를 소문자로 표기하기도 하고, `java.net.HttpURLConnection`처럼 한 클래스 안에서 단어별로 다르게 쓰기도 했다. 그러나 단일 프로젝트에서는 규칙이 명확하지 않으면 같은 단어의 조합을 다른 이름으로 표기할 수 있어서 혼동을 유발한다.

약어가 클래스명에서 대문자로 들어가면 단어 간의 구분을 인지하기에 불리하다. 약어가 연속된 경우 더욱 가독성을 해친다. 예를 들면 XMLRPCHTTPAPIURL과 같은 경우이다. 그래서 기본 정책으로는 약어의 중간단어를 소문자로 표기하고 프로젝트별로 모두 대문자로 표기할 약어의 목록을 명시하는 방식이 가독성을 높이고 규칙을 단순화하는데 유리하다. 즉 프로젝트 내에서 정의한 단어 목록이 없다면 'XmlRpcHttpApiUrl'과 같이 쓴다.

**좋은 예:**

HTTP + API + URL 의 클래스 이름의 경우

- 대문자로 표기할 약어의 목록을 정의하지 않는 경우: `HttpApiUrl`
- API만 대문자로 표기할 약어의 목록에 있을 경우: `HttpAPIUrl`
- HTTP, API, URL이 대문자로 표기할 약어의 목록에 있을 경우: `HTTPAPIURL`

### 2.4. 패키지 이름은 소문자로 구성
**[package-lowercase]**

패키지 이름은 소문자를 사용하여 작성한다. 단어별 구문을 위해 언더스코어(_)나 대문자를 섞지 않는다.

**나쁜 예:**
```java
package com.navercorp.apiGateway

package com.navercorp.api_gateway
```

**좋은 예:**
```java
package com.navercorp.apigateway
```

### 2.5. 클래스/인터페이스 이름에 대문자 카멜표기법 적용
**[class-interface-lower-camelcase]**

클래스 이름은 단어의 첫 글자를 대문자로 시작하는 대문자 카멜표기법(Upper camel case)을 사용한다. 파스칼표기법(Pascal case)으로도 불린다.

**나쁜 예:**
```java
public class reservation

public class Accesstoken
```

**좋은 예:**
```java
public class Reservation

public class AccessToken
```

### 2.6. 클래스 이름에 명사 사용
**[class-noun]**

클래스 이름은 명사나 명사절로 짓는다.

### 2.7. 인터페이스 이름에 명사/형용사 사용
**[interface-noun-adj]**

인터페이스(interface)의 이름은 클래스 이름은 명사/명사절로 혹은 형용사/형용사절로 짓는다.

**좋은 예:**
```java
public interface RowMapper {

public interface AutoClosable {
```

### 2.8. 테스트 클래스는 'Test'로 끝남
**[test-class-suffix]**

JUnit 등으로 작성한 테스트 코드를 담은 클래스는 'Test'을 마지막에 붙인다.

**좋은 예:**
```java
public class WatcherTest {
```

### 2.9. 메서드 이름에 소문자 카멜표기법 적용
**[method-lower-camelcase]**

메서드의 이름에는 첫 번째 단어를 소문자로 작성하고, 이어지는 단어의 첫 글자를 대문자로 작성하는 소문자 카멜표기법(Lower camel case)를 사용한다. 테스트 클래스의 메서드 이름에서는 언더스코어를 허용한다.

### 2.10. 메서드 이름은 동사/전치사로 시작
**[method-verb-preposition]**

메서드명은 기본적으로는 동사로 시작한다. 다른 타입으로 전환하는 메서드나 빌더 패턴을 구현한 클래스의 메서드에는 전치사를 쓸 수 있다.

**좋은 예:**
```java
동사사용 : renderHtml()

전환메서드의 전치사 : toString()

Builder 패턴 적용한 클래스의 메서드의 전치사 : withUserId(String id)
```

### 2.11. 상수는 대문자와 언더스코어로 구성
**[constant_uppercase]**

상태를 가지지 않는 자료형이면서 static final`로 선언되어 있는 필드일 때를 상수로 간주한다. 상수 이름은 대문자로 작성하며, 복합어는 언더스코어(`_)를 사용하여 단어를 구분한다.

**좋은 예:**
```java
public final int UNLIMITED = -1;
public final String POSTAL_CODE_EXPRESSION = "POST";
```

### 2.12. 변수에 소문자 카멜표기법 적용
**[var-lower-camelcase]**

상수가 아닌 클래스의 멤버변수/지역변수/메서드 파라미터에는 소문자 카멜표기법(Lower camel case)을 사용한다.

**나쁜 예:**
```java
private boolean Authorized;
private int AccessToken;
```

**좋은 예:**
```java
private boolean authorized;
private int accessToken;
```

### 2.13. 임시 변수 외에는 1 글자 이름 사용 금지
**[avoid-1-char-var]**

메서드 블럭 범위 이상의 생명 주기를 가지는 변수에는 1글자로 된 이름을 쓰지 않는다. 반복문의 인덱스나 람다 표현식의 파라미터 등 짧은 범위의 임시 변수에는 관례적으로 1글자 변수명을 사용할 수 있다.

**나쁜 예:**
```java
HtmlParser p = new HtmlParser();
```

**좋은 예:**
```java
HtmlParser parser = new HtmlParser();
```

## 3. 선언 (Declarations)
클래스, 필드, 메서드, 변수값, import문 등의 소스 구성요소를 선언할 때 고려해야할 규칙이다.

### 3.1. 소스파일당 1개의 탑레벨 클래스를 담기
**[1-top-level-class]**

탑레벨 클래스(Top level class)는 소스 파일에 1개만 존재해야 한다. ( 탑레벨 클래스 선언의 컴파일타임 에러 체크에 대해서는 Java Language Specification 7.6 참조 )

**나쁜 예:**
```java
public class LogParser {
}

class LogType {
}
```

**좋은 예:**
```java
public class LogParser {
    // 굳이 한 파일안에 선언해야 한다면 내부 클래스로 선언
    class LogType {
    }
}
```

### 3.2. static import에만 와일드 카드 허용
**[avoid-star-import]**

클래스를 import할때는 와일드카드(*) 없이 모든 클래스명을 다 쓴다. static import에서는 와일드카드를 허용한다.

**나쁜 예:**
```java
import java.util.*;
```

**좋은 예:**
```java
import java.util.List;
import java.util.ArrayList;
```

### 3.3. 제한자 선언의 순서
**[modifier-order]**

클래스/메서드/멤버변수의 제한자는 Java Language Specification에서 명시한 아래의 순서로 쓴다.

public protected private abstract static final transient volatile synchronized native strictfp

( Java Language Specification - Chapter 18. Syntax 참조)

### 3.4. 애너테이션 선언 후 새줄 사용
**[newline-after-annotation]**

클래스, 인터페이스, 메서드, 생성자에 붙는 애너테이션은 선언 후 새줄을 사용한다. 이 위치에서도 파라미터가 없는 애너테이션 1개는 같은 줄에 선언할 수 있다.

**좋은 예:**
```java
@RequestMapping("/guests")
public void findGuests() {}

@Override public void destroy() {}
```

### 3.5. 한 줄에 한 문장
**[1-state-per-line]**

문장이 끝나는 ; 뒤에는 새줄을 삽입한다. 한 줄에 여러 문장을 쓰지 않는다.

**나쁜 예:**
```java
int base = 0; int weight = 2;
```

**좋은 예:**
```java
int base = 0;
int weight = 2;
```

### 3.6. 하나의 선언문에는 하나의 변수만
**[1-var-per-declaration]**

변수 선언문은 한 문장에서 하나의 변수만을 다룬다.

**나쁜 예:**
```java
int base, weight;
```

**좋은 예:**
```java
int base;
int weight;
```

### 3.7. 배열에서 대괄호는 타입 뒤에 선언
**[array-square-after-type]**

배열 선언에 오는 대괄호([])는 타입의 바로 뒤에 붙인다. 변수명 뒤에 붙이지 않는다.

**나쁜 예:**
```java
String names[];
```

**좋은 예:**
```java
String[] names;
```

### 3.8. `long`형 값의 마지막에 `L`붙이기
**[long-value-suffix]**

long형의 숫자에는 마지막에 대문자 'L'을 붙인다. 소문자 'l'보다 숫자 '1'과의 차이가 커서 가독성이 높아진다.

**나쁜 예:**
```java
long base = 54423234211l;
```

**좋은 예:**
```java
long base = 54423234211L;
```

### 3.9. 특수 문자의 전용 선언 방식을 활용
**[special-escape]**

\b, \f, \n,\r,\t, `\", \\ 와 같이 특별히 정의된 선언 방식이 있는 특수 문자가 있다. 이런 문자들은 숫자를 이용한 \008 이나 `\u0008`와 같은 숫자를 넣은 선언보다 전용 방식을 활용한다.

**나쁜 예:**
```java
System.out.println("---\012---");
```

**좋은 예:**
```java
System.out.println("---\n---");
```

## 4. 들여쓰기 (Indentation)
들여쓰기는 코드의 계층을 구분하기 위해 추가하는 문자이다.

### 4.1. 하드탭 사용
**[indentation-tab]**

탭(tab) 문자를 사용하여 들여쓴다. 탭 대신 스페이스를 사용하지 않는다. 이를 잘 준수하기 위해서 스페이스와 탭을 구별해서 보여주도록 에디터를 설정한다.

### 4.2. 탭의 크기는 4개의 스페이스
**[4-spaces-tab]**

1개의 탭의 크기는 스페이스 4개와 같도록 에디터에서 설정한다.

### 4.3. 블럭 들여쓰기
**[block-indentation]**

클래스, 메서드, 제어문 등의 코드 블럭이 생길 때마다 1단계를 더 들여쓴다.

## 5. 중괄호 (Braces)
중괄호({,}) 는 클래스, 메서드, 제어문의 블럭을 구분한다.

### 5.1. K&R 스타일로 중괄호 선언
**[braces-knr-style]**

클래스 선언, 메서드 선언, 조건/반복문 등의 코드 블럭을 감싸는 중괄호에 적용되는 규칙이다. 중괄호 선언은 K&R 스타일(Kernighan and Ritchie style)을 따른다. 줄의 마지막에서 시작 중괄호`{`를 쓰고 열고 새줄을 삽입한다. 블럭을 마친후에는 새줄 삽입 후 중괄호를 닫는다.

**나쁜 예:**
```java
public class SearchConditionParser
{
    public boolean isValidExpression(String exp)
    {

        if (exp == null)
        {
            return false;
        }

        for (char ch : exp.toCharArray())
        {
             ....
        }

        return true;
    }
}
```

**좋은 예:**
```java
public class SearchConditionParser {
    public boolean isValidExpression(String exp) {

        if (exp == null) {
            return false;
        }

        for (char ch : exp.toCharArray()) {
            ....
        }

        return true;
    }
}
```

### 5.2. 닫는 중괄호와 같은 줄에 else, catch, finally, while 선언
**[sub-flow-after-brace]**

아래의 키워드는 닫는 중괄호(}) 와 같은 줄에 쓴다.

else

catch, finaly

do-while 문에서의 while

**나쁜 예:**
```java
if (line.startWith(WARNING_PREFIX)) {
    return LogPattern.WARN;
}
else if (line.startWith(DANGER_PREFIX)) {
    return LogPattern.DANGER;
}
else {
    return LogPattern.NORMAL;
}
```

**좋은 예:**
```java
if (line.startWith(WARNING_PREFIX)) {
    return LogPattern.WARN;
} else if (line.startWith(DANGER_PREFIX)) {
    return LogPattern.NORMAL;
} else {
    return LogPattern.NORMAL;
}
```

**나쁜 예:**
```java
try {
    writeLog();
}
catch (IOException ioe) {
    reportFailure(ioe);
}
finally {
    writeFooter();
}
```

**좋은 예:**
```java
try {
    writeLog();
} catch (IOException ioe) {
    reportFailure(ioe);
} finally {
    writeFooter();
}
```

**나쁜 예:**
```java
do {
    write(line);
    line = readLine();
}
```

**좋은 예:**
```java
do {
    write(line);
    line = readLine();
} while (line != null);
```

### 5.3. 빈 블럭에 새줄 없이 중괄호 닫기 허용
**[permit-concise-empty-block]**

내용이 없는 블럭을 선언할 때는 같은 줄에서 중괄호를 닫는 것을 허용한다.

**좋은 예:**
```java
public void close() {}
```

### 5.4. 조건/반복문에 중괄호 필수 사용
**[need-braces]**

조건, 반복문이 한 줄로 끝더라도 중괄호를 활용한다. 이 문서에 언급된 중괄호의 전후의 공백, 제어문 앞 뒤의 새줄 규칙도 함께 고려한다.

**나쁜 예:**
```java
if (exp == null) return false;

for (char ch : exp.toCharArray()) if (ch == 0) return false;
```

**좋은 예:**
```java
if (exp == null) {
    return false;
}

for (char ch : exp.toCharArray()) {

    if (ch == 0) {
        return false;
    }

}
```

## 6. 줄바꿈 (Line-wrapping)
줄바꿈은 작성한 명령어가 줄 너비를 초과했을 경우 코드 가독성을 위해서 강제로 줄을 바꾸는 것을 말한다.

### 6.1. 최대 줄 너비는 120
**[line-length-120]**

최대 줄 사용 너비는 120자까지 가능하다.

### 6.2. package,import 선언문은 한 줄로
**[1-line-package-import]**

package,import 선언문 중간에서는 줄을 바꾸지 않는다. 최대 줄수를 초과하더라도 한 줄로 쓴다.

### 6.3. 줄바꿈 후 추가 들여쓰기
**[indentation-after-line-wrapping]**

줄바꿈 이후 이어지는 줄에서는 최초 시작한 줄에서보다 적어도 1단계의 들여쓰기를 더 추가한다. IDE의 자동 포메팅 기능으로 이를 동일하게 맞추러면 Appendix C의 각 IDE별 설정을 참고한다.

**좋은 예:**
```java
AbstractAggregateRootTest.AggregateRoot proxyAggregateRoot =
        em.getReference(AbstractAggregateRootTest.AggregateRoot.class, aggregateRoot.getId());
```

### 6.4. 줄바꿈 허용 위치
**[line-wrapping-position]**

가독성을 위해 줄을 바꾸는 위치는 다음 중의 하나로 한다.

extends 선언 후

implements 선언 후

throws 선언 후

시작 소괄호(() 선언 후

콤마(,) 후

. 전

연산자 전

+, -, *, /, %

==, !=, >=, >,⇐, <, &&, ||

&, |, ^, >>>, >>, <<, ?

instanceof

**좋은 예:**
```java
public boolen isAbnormalAccess (
    User user, AccessLog log) {

    String message = user.getId() + "|" | log.getPrefix()
        + "|" + SUFFIX;
}
```

## 7. 빈 줄(Blank lines)
빈 줄은 명령문 그룹의 영역을 표시하기 위하여 사용한다.

### 7.1. package 선언 후 빈 줄 삽입
**[blankline-after-package]**

**좋은 예:**
```java
package com.naver.lucy.util;

import java.util.Date;
```

### 7.2. import 선언의 순서와 빈 줄 삽입
**[import-grouping]**

import 구절은 아래와 같은 순서로 그룹을 묶어서 선언한다.

static imports

java.

javax.

org.

net.

8~10을 제외한 com.*

1~6, 8~10을 제외한 패키지에 있는 클래스

com.nhncorp.

com.navercorp.

com.naver.

각 그룹 사이에는 빈줄을 삽입한다. 같은 그룹 내에서는 알파벳 순으로 정렬한다.

**좋은 예:**
```java
import java.util.Date;
import java.util.List;

import javax.naming.NamingException;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.util.Assert;

import com.google.common.base.Function;

import com.naver.lucy.util.AnnotationUtils;
```

이 규칙은 대부분 IDE에서 자동으로 정리해주는 대로 쓰기 때문에 IDE 설정을 일치시키는데 신경을 써야 한다.

### 7.3. 메소드 사이에 빈 줄 삽입
**[blankline-between-methods]**

메서드의 선언이 끝난 후 다음 메서드 선언이 시작되기 전에 빈줄을 삽입한다.

**좋은 예:**
```java
public void setId(int id) {
    this.id = id;
}

public void setName(String name) {
    this.name = name;
}
```

## 8. 공백 (Whitespace)
공백은 코드의 가독성을 높이기 위해 사용한다.

### 8.1. 공백으로 줄을 끝내지 않음
**[no-trailing-spaces]**

빈줄을 포함하여 모든 줄은 탭이나 공백으로 끝내지 않는다.

### 8.2. 대괄호 뒤에 공백 삽입
**[space-after-bracket]**

닫는 대괄호(]) 뒤에 `;`으로 문장이 끝나지 않고 다른 선언이 올 경우 공백을 삽입한다.

**나쁜 예:**
```java
int[]masks = new int[]{0, 1, 1};
```

**좋은 예:**
```java
int[] masks = new int[] {0, 1, 1};
```

### 8.3. 중괄호의 시작 전, 종료 후에 공백 삽입
**[space-around-brace]**

여는 중괄호({) 앞에는 공백을 삽입한다. 닫는 중괄호(}) 뒤에 else ,catch 등의 키워드가 있을 경우 중괄호와 키워드 사이에 공백을 삽입한다.

**좋은 예:**
```java
public void printWarnMessage(String line) {
    if (line.startsWith(WARN_PREFIX)) {
        ...
    } else {
        ...
    }
}
```

### 8.4. 제어문 키워드와 여는 소괄호 사이에 공백 삽입
**[space-between-keyword-parentheses]**

if, for, while, catch, synchronized, switch`와 같은 제어문 키워드의 뒤에 소괄호((,)`)를 선언하는 경우, 시작 소괄호 앞에 공백을 삽입한다.

**좋은 예:**
```java
if (maxLine > LIMITED) {
    return false;
}
```

### 8.5. 식별자와 여는 소괄호 사이에 공백 미삽입
**[no-space-between-identifier-parentheses]**

식별자와 여는 소괄호(() 사이에는 공백을 삽입하지 않는다. 생성자와 메서드의 선언, 호출, 애너테이션 선언 뒤에 쓰이는 소괄호가 그에 해당한다.

**나쁜 예:**
```java
public StringProcessor () {} // 생성자

@Cached ("local")
public String removeEndingDot (String original) {
    assertNotNull (original);
    ...
}
```

**좋은 예:**
```java
public StringProcessor() {} // 생성자

@Cached("local")
public String removeEndingDot(String original) {
    assertNotNull(original);
    ...
}
```

### 8.6. 타입 캐스팅에 쓰이는 소괄호 내부 공백 미삽입
**[no-space-typecasting]**

타입캐스팅을 위해 선언한 소괄호의 내부에는 공백을 삽입하지 않는다.

**나쁜 예:**
```java
String message = ( String ) rawLine;
```

**좋은 예:**
```java
String message = (String)rawLine;
```

### 8.7. 제네릭스 산괄호의 공백 규칙
**[generic-whitespace]**

제네릭스(Generics) 선언에 쓰이는 산괄호(<,>) 주위의 공백은 다음과 같이 처리한다.

제네릭스 메서드 선언 일 때만 < 앞에 공백을 삽입한다.

< 뒤에 공백을 삽입하지 않는다.

> 앞에 공백을 삽입하지 않는다.

아래의 경우를 제외하고는 `>`뒤에 공백을 삽입한다.

메서드 레퍼런스가 바로 이어질 때

여는 소괄호('(')가 바로 이어질 때

메서드 이름이 바로 이어질 때

**좋은 예:**
```java
public static <A extends Annotation> A find(AnnotatedElement elem, Class<A> type) { // 제네릭스 메서드 선언
    List<Integer> l1 = new ArrayList<>(); // '(' 가 바로 이어질때
    List<String> l2 = ImmutableList.Builder<String>::new; // 메서드 레퍼런스가 바로 이어질 때
    int diff = Util.<Integer, String>compare(l1, l2); // 메서드 이름이 바로 이어질 때
}
```

### 8.8. 콤마/구분자 세미콜론의 뒤에만 공백 삽입
**[space-after-comma-semicolon]**

콤마(,)와 반복문(while, for)의 구분자로 쓰이는 세미콜론(;)에는 뒤에만 공백을 삽입한다.

**나쁜 예:**
```java
for (int i = 0;i < length;i++) {
    display(level,message,i)
}
```

**좋은 예:**
```java
for (int i = 0; i < length; i++) {
    display(level, message, i)
}
```

### 8.9. 콜론의 앞 뒤에 공백 삽입
**[space-around-colon]**

반복문과 삼항연산자에서 콜론(:)의 앞 뒤에는 공백을 삽입한다. 라벨 선언 뒤에는 아무런 문자열이 없으므로 앞에만 공백을 삽입한다.

**좋은 예:**
```java
for (Customer customer : visitedCustomers) {
    AccessPattern pattern = isAbnormal(accessLog) ? AccessPattern.ABUSE : AccessPattern.NORMAL;
    int grade = evaluate(customer, pattern);

    switch (grade) {
        case GOLD :
            sendSms(customer);
        case SILVER :
            sendEmail(customer);
        default :
            inreasePoint(customer)
    }
}
```

### 8.10. 이항/삼항 연산자의 앞 뒤에 공백 삽입
**[space-around-binary-ternary-operator]**

이항/삼항 연산자의 앞 뒤에는 공백을 삽입한다.

**좋은 예:**
```java
if (pattern == Access.ABNORMAL) {
    return 0;
}

finalScore += weight * rawScore - absentCount;

if (finalScore > MAX_LIMIT) {
    return MAX_LIMIT;
}
```

### 8.11. 단항 연산자와 연산 대상 사이에 공백을 미삽입
**[no-space-increament-decrement-operator]**

단항 연산자와 연산 대상의 사이에는 공백을 삽입하지 않는다.

전위 연산자 : 연산자 뒤에 공백을 삽입하지 않는다.

전위 증감/감소 연산자 : ++,--

부호로 쓰이는 +, -

NOT 연산자 : ~, !

후위 연산자 : 연산자 앞에 공백을 삽입하지 않는다.

후위 증감/감소 연산자 : ++,--

**나쁜 예:**
```java
int point = score[++ index] * rank -- * - 1;
```

**좋은 예:**
```java
int point = score[++index] * rank-- * -1;
```

### 8.12. 주석문 기호 전후의 공백 삽입
**[space-around-comment]**

주석의 전후에는 아래와 같이 공백을 삽입한다.

명령문과 같은 줄에 주석을 붙일 때 // 앞

주석 시작 기호 // 뒤

주석 시작 기호 /* 뒤

블록 주석을 한 줄로 작성시 종료 기호 */ 앞

**좋은 예:**
```java
/*
 * 공백 후 주석내용 시작
 */

System.out.print(true); // 주석 기호 앞 뒤로 공백

/* 주석내용 앞에 공백, 뒤에도 공백 */