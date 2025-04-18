# GitHub Actions를 활용한 CI/CD 코딩 컨벤션 (멀티모듈/모노레포)

## 1. 저장소 구조 및 관리

- **모노레포 구조**:
  ```
  my-monorepo/
  ├── .github/
  │   └── workflows/     # GitHub Actions 워크플로우
  ├── packages/          # 각 패키지/모듈 디렉토리
  │   ├── api/
  │   ├── web/
  │   └── shared/
  ├── scripts/           # 공통 스크립트
  ├── docs/              # 문서
  └── package.json       # 루트 package.json
  ```

- **멀티모듈 구조** (Java/Gradle 예시):
  ```
  multimodule-project/
  ├── .github/
  │   └── workflows/     # GitHub Actions 워크플로우
  ├── core/              # 코어 모듈
  ├── api/               # API 모듈
  ├── web/               # 웹 모듈
  ├── settings.gradle    # 모듈 설정
  └── build.gradle       # 공통 빌드 설정
  ```

- **브랜치 관리**:
  - `main`: 제품 출시 버전
  - `develop`: 개발 통합 브랜치
  - `feature/*`: 기능 개발 브랜치
  - `release/*`: 릴리스 준비 브랜치
  - `hotfix/*`: 긴급 수정 브랜치

## 2. GitHub Actions 워크플로우 구성

- **워크플로우 파일 네이밍**:
  - `ci.yml`: 기본 CI 워크플로우
  - `deploy-{env}.yml`: 환경별 배포 워크플로우
  - `release.yml`: 릴리스 워크플로우
  - `{module}-ci.yml`: 모듈별 CI 워크플로우

- **기본 워크플로우 구조**:
  ```yaml
  name: CI

  on:
    push:
      branches: [ main, develop ]
    pull_request:
      branches: [ main, develop ]
      
  jobs:
    # 작업 정의
  ```

- **모듈별 트리거 설정**:
  ```yaml
  on:
    push:
      paths:
        - 'packages/api/**'
        - 'packages/shared/**'
    pull_request:
      paths:
        - 'packages/api/**'
        - 'packages/shared/**'
  ```

- **작업 캐싱**: 빌드 속도 향상을 위한 캐싱 활용
  ```yaml
  - name: Cache dependencies
    uses: actions/cache@v3
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-node-
  ```

## 3. 테스트 자동화 규칙

- **테스트 실행 위치**: 모든 PR과 주요 브랜치 푸시에서 테스트 실행
  ```yaml
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Run tests
          run: npm test
  ```

- **테스트 범위**:
  - 단위 테스트: 모든 PR에서 실행
  - 통합 테스트: 모든 PR에서 실행
  - E2E 테스트: 배포 전 실행 또는 야간 빌드로 실행

- **경계 상태 테스트 포함**: 에러 케이스 및 경계값 테스트 포함

- **테스트 보고서 생성**: 테스트 결과 아티팩트로 저장
  ```yaml
  - name: Upload test results
    uses: actions/upload-artifact@v3
    with:
      name: test-results
      path: test-results/
  ```

- **코드 커버리지 요구사항**: 최소 코드 커버리지 기준 설정 (예: 80%)

## 4. 코드 품질 관리

- **린팅 및 포맷팅 체크**:
  ```yaml
  - name: Lint
    run: npm run lint
    
  - name: Check formatting
    run: npm run format:check
  ```

- **코드 품질 분석 도구 통합**:
  - SonarQube 또는 SonarCloud 통합
  ```yaml
  - name: SonarCloud Scan
    uses: SonarSource/sonarcloud-github-action@master
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
  ```

- **Pull Request 체크**:
  - 코드 리뷰 승인 필수
  - CI 성공 필수
  - 브랜치 최신화 필수

## 5. 빌드 및 배포 프로세스

- **환경별 설정**:
  - `development`: 개발 환경
  - `staging`: 스테이징 환경
  - `production`: 운영 환경

- **빌드 스크립트 표준화**:
  - 루트 디렉토리에 공통 빌드 스크립트 작성
  ```bash
  #!/bin/bash
  # build.sh
  MODULE=$1
  ENV=$2
  
  echo "Building module $MODULE for $ENV environment"
  cd packages/$MODULE && npm run build:$ENV
  ```

- **배포 워크플로우 예시**:
  ```yaml
  name: Deploy to Staging
  
  on:
    push:
      branches: [ develop ]
  
  jobs:
    deploy:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        
        - name: Set up environment
          run: ./scripts/setup-env.sh staging
          
        - name: Build
          run: ./scripts/build.sh all staging
          
        - name: Deploy
          run: ./scripts/deploy.sh staging
  ```

- **환경 변수 관리**:
  - GitHub Secrets 활용
  - 환경별 변수 정의
  ```yaml
  env:
    NODE_ENV: production
    API_URL: ${{ secrets.PROD_API_URL }}
  ```

## 6. 모노레포/멀티모듈 최적화

- **선택적 빌드**: 변경된 모듈만 빌드/테스트
  ```yaml
  - name: Check changed packages
    id: changed
    run: |
      CHANGED=$(node ./scripts/get-changed-packages.js)
      echo "::set-output name=packages::$CHANGED"
      
  - name: Build changed packages
    if: steps.changed.outputs.packages != ''
    run: npm run build:packages ${{ steps.changed.outputs.packages }}
  ```

- **의존성 관리**:
  - 모듈 간 의존성 명확히 정의
  - 버전 관리 일관성 유지 (예: Lerna, Yarn Workspaces)

- **병렬 작업 실행**:
  ```yaml
  jobs:
    test-api:
      runs-on: ubuntu-latest
      steps:
        - name: Test API module
          run: cd api && npm test
          
    test-web:
      runs-on: ubuntu-latest
      steps:
        - name: Test Web module
          run: cd web && npm test
  ```

## 7. 릴리스 관리

- **버전 관리 자동화**:
  - 시맨틱 버저닝 준수
  - 자동 버전 증가
  ```yaml
  - name: Bump version
    id: version
    run: |
      VERSION=$(./scripts/bump-version.sh)
      echo "::set-output name=new_version::$VERSION"
  ```

- **체인지로그 자동 생성**:
  ```yaml
  - name: Generate changelog
    run: npx standard-version --skip.tag --skip.commit
  ```

- **릴리스 태그 생성**:
  ```yaml
  - name: Create Release
    uses: actions/create-release@v1
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    with:
      tag_name: v${{ steps.version.outputs.new_version }}
      release_name: Release v${{ steps.version.outputs.new_version }}
      body_path: CHANGELOG.md
  ```

- **아티팩트 관리**:
  - 배포 아티팩트 일관된 네이밍
  - 필요 시 아티팩트 저장소 활용 (GitHub Packages)

## 8. 보안 및 사용 권한

- **시크릿 관리**:
  - 환경별 시크릿 분리
  - 권한 최소화 원칙 적용

- **워크플로우 권한 제한**:
  ```yaml
  permissions:
    contents: read
    packages: write
  ```

- **의존성 취약점 검사**:
  ```yaml
  - name: Security audit
    run: npm audit --audit-level=high
  ```

- **이미지 스캔**: 컨테이너 이미지 취약점 스캔
  ```yaml
  - name: Scan Docker image
    uses: aquasecurity/trivy-action@master
    with:
      image-ref: 'my-app:latest'
      format: 'table'
      exit-code: '1'
      severity: 'CRITICAL,HIGH'
  ```

## 9. 성능 최적화

- **워크플로우 실행 시간 모니터링**: 워크플로우 실행 시간을 정기적으로 검토하고 최적화

- **매트릭스 빌드**: 다양한 환경에서 병렬 테스트
  ```yaml
  jobs:
    test:
      strategy:
        matrix:
          node-version: [14.x, 16.x, 18.x]
          os: [ubuntu-latest, windows-latest]
      runs-on: ${{ matrix.os }}
      steps:
        - name: Use Node.js ${{ matrix.node-version }}
          uses: actions/setup-node@v3
          with:
            node-version: ${{ matrix.node-version }}
  ```

- **자체 호스팅 러너 고려**: 빌드 시간이 긴 경우 자체 호스팅 러너 활용

## 10. 모니터링 및 알림

- **워크플로우 상태 알림**:
  ```yaml
  - name: Notify on failure
    if: failure()
    uses: rtCamp/action-slack-notify@v2
    env:
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
      SLACK_COLOR: 'danger'
      SLACK_TITLE: 'CI 실패'
      SLACK_MESSAGE: '${{ github.workflow }} 워크플로우가 실패했습니다'
  ```

- **배포 알림**: 배포 완료 시 팀에 알림

- **에러 추적 통합**: Sentry 등의 에러 추적 도구 통합
