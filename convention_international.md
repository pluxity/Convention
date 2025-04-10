# 국제화 및 접근성 코딩 컨벤션

## 1. 국제화(I18n) 기본 원칙

- **설계 단계부터 국제화 고려**: 국제화는 개발 후반부가 아닌 설계 단계부터 고려해야 합니다.

- **문자열 외부화**: 하드코딩된 문자열을 사용하지 않고 리소스 파일로 분리합니다.
  ```java
  // 잘못된 예:
  label.setText("환영합니다");
  
  // 올바른 예:
  label.setText(messages.get("welcome.message"));
  ```

- **리소스 번들 활용**: 언어별 리소스 파일을 구성하고 관리합니다.
  ```
  messages_ko.properties
  messages_en.properties
  messages_ja.properties
  ```

- **동적 언어 전환**: 사용자가 애플리케이션 실행 중에도 언어를 변경할 수 있도록 지원합니다.

## 2. 다국어 지원 방법

- **표준 라이브러리 활용**: 각 언어/프레임워크의 국제화 표준 라이브러리를 활용합니다.
  - Java: ResourceBundle
  - Spring: MessageSource
  - React: react-i18next
  - Angular: ngx-translate

- **번역 키 네이밍 규칙**:
  - 계층 구조 사용: `section.subsection.element.action`
  - 일관된 형식 유지: `noun.verb` 또는 `verb.noun`
  - 특수 문자 제한: 영문 소문자, 숫자, 마침표만 사용
  ```
  user.login.success
  user.login.failure
  product.detail.title
  ```

- **변수 처리**: 문자열 내 변수는 명명된 플레이스홀더를 사용합니다.
  ```
  // 리소스 파일:
  user.welcome=안녕하세요, {name}님!
  
  // 코드:
  String message = i18n.getMessage("user.welcome", Map.of("name", userName));
  ```

- **번역 관리 시스템**: 대규모 프로젝트는 번역 관리 시스템(TMS)을 도입합니다.
  - Crowdin, Lokalise, Phrase 등 활용

## 3. 지역화(L10n) 고려사항

- **날짜 및 시간 형식**: 지역별 날짜/시간 표시 방식을 지원합니다.
  ```java
  // 잘못된 예:
  String date = "2023-04-01";
  
  // 올바른 예:
  DateTimeFormatter formatter = DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)
      .withLocale(locale);
  String date = formatter.format(localDate);
  ```

- **숫자 및 통화 형식**: 천 단위 구분자, 소수점, 통화 기호를 적절히 처리합니다.
  ```java
  // 잘못된 예:
  String price = product.getPrice() + "원";
  
  // 올바른 예:
  NumberFormat currencyFormatter = NumberFormat.getCurrencyInstance(locale);
  String price = currencyFormatter.format(product.getPrice());
  ```

- **측정 단위**: 국가별 측정 단위 차이를 고려합니다(미터/피트, 섭씨/화씨 등).

- **주소 형식**: 국가별 주소 입력 및 표시 형식을 지원합니다.

- **개인명 처리**: 이름과 성의 순서, 중간 이름 유무를 고려합니다.
  ```java
  // 잘못된 예:
  String fullName = firstName + " " + lastName;
  
  // 올바른 예:
  String fullName = nameFormatter.format(firstName, middleName, lastName, locale);
  ```

## 4. 문자 인코딩 및 폰트

- **UTF-8 사용**: 모든 파일 및 데이터베이스에 UTF-8 인코딩을 사용합니다.

- **다국어 폰트 지원**: 다양한 언어의 글자를 표시할 수 있는 폰트를 사용합니다.
  - 웹: `font-family`에 다국어 지원 폰트 포함
  - 디자인에서 fallback 폰트 지정

- **양방향 텍스트(RTL) 지원**: 아랍어, 히브리어 등 오른쪽에서 왼쪽(RTL)으로 쓰는 언어 지원:
  - CSS: `dir="rtl"` 속성 활용
  - 레이아웃 디자인 시 RTL 고려

## 5. 접근성(a11y) 표준 준수

- **WCAG 가이드라인 준수**: Web Content Accessibility Guidelines 2.1 AA 수준 이상 준수:
  - 인식 가능(Perceivable): 모든 사용자가 콘텐츠를 인식할 수 있게 함
  - 운용 가능(Operable): 다양한 방법으로 UI 사용 가능
  - 이해 가능(Understandable): 콘텐츠와 작동 방식 이해 가능
  - 견고함(Robust): 보조 기술과 호환성 유지

- **시맨틱 마크업**: 적절한 HTML 요소를 의미에 맞게 사용합니다.
  ```html
  <!-- 잘못된 예: -->
  <div class="button" onclick="submit()">제출</div>
  
  <!-- 올바른 예: -->
  <button type="submit">제출</button>
  ```

- **키보드 접근성**: 모든 기능이 키보드로도 사용 가능해야 합니다.
  - 탭 순서 논리적 설정
  - 포커스 표시 시각적 제공
  - 단축키 제공(선택적)

- **스크린 리더 지원**:
  - 이미지에 대체 텍스트 제공
  ```html
  <img src="logo.png" alt="회사 로고">
  ```
  - ARIA 속성 적절히 사용
  ```html
  <div role="alert" aria-live="assertive">로그인 실패</div>
  ```
  - 폼 요소에 레이블 연결
  ```html
  <label for="username">사용자명:</label>
  <input id="username" type="text">
  ```

- **색상 및 대비**: 색맹 및 저시력 사용자를 위한 고려:
  - 색상만으로 정보 구분하지 않기
  - 텍스트와 배경 간 충분한 대비 제공 (WCAG AA: 4.5:1 이상)
  - 다크 모드 지원 고려

- **반응형 디자인**: 다양한 기기와 화면 크기에 적응하는 디자인을 구현합니다.
  - 확대/축소 기능 제한하지 않기
  - 터치 영역 충분히 크게 설정 (최소 44x44px)

- **오류 메시지**: 명확하고 구체적인 오류 메시지와 해결 방법을 제공합니다.
  ```html
  <!-- 잘못된 예: -->
  <p class="error">잘못된 입력</p>
  
  <!-- 올바른 예: -->
  <p class="error" role="alert">
    비밀번호는 8자 이상이어야 하며, 숫자와 특수문자를 포함해야 합니다.
  </p>
  ```

## 6. 문화적 고려사항

- **문화적 상대성**: 아이콘, 색상, 이미지가 모든 문화권에서 적절한지 확인합니다.
  - 종교적 상징 사용 주의
  - 제스처 관련 아이콘 검토

- **날짜 형식의 모호함 방지**: 날짜 표기는 월 이름을 문자로 표시하거나 ISO 형식 사용:
  ```
  // 모호함 가능성:
  01/02/2023 (미국: 1월 2일, 유럽: 2월 1일)
  
  // 명확한 표기:
  2023년 1월 2일 또는 2023-01-02
  ```

- **법적 요구사항**: 지역별 법적 요구사항 준수 (GDPR, CCPA 등).
  - 개인정보 처리방침 다국어 지원
  - 국가별 필수 고지사항 처리

## 7. 테스트 및 품질 관리

- **다국어 테스트**: 지원하는 모든 언어에 대해 테스트를 수행합니다.
  - 문자열 길이 확인 (일부 언어는 번역 시 길이가 크게 증가할 수 있음)
  - 잘린 텍스트, 레이아웃 깨짐 등 확인

- **접근성 테스트**: 자동화 도구 및 실제 사용자 테스트 병행:
  - 자동화 도구: Axe, WAVE, Lighthouse 등
  - 스크린 리더 테스트: NVDA, JAWS, VoiceOver 등
  - 다양한 입력 방식 테스트: 키보드, 음성, 터치 등

- **지속적인 접근성 모니터링**: CI/CD 파이프라인에 접근성 테스트 통합

## 8. 문서화

- **국제화/접근성 가이드**: 개발자와 콘텐츠 제작자를 위한 내부 가이드 작성

- **번역 관리 문서**: 번역 업데이트 프로세스, 키 관리 등 문서화

- **접근성 준수 선언**: 공개 웹사이트의 경우 접근성 준수 선언 페이지 제공
