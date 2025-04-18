# Git Flow 기반 Git 컨벤션

## 1. 브랜치 구조

- **main (또는 master)**: 제품 출시 버전을 관리하는 브랜치
- **develop**: 개발 중인 코드의 통합 브랜치
- **feature**: 새로운 기능 개발을 위한 브랜치
- **release**: 출시 준비를 위한 브랜치
- **hotfix**: 출시된 버전의 긴급 수정을 위한 브랜치

## 2. 작업 시작 전 준비

- 모든 작업은 **JIRA 티켓 생성**으로 시작합니다.
- 티켓 번호는 브랜치명과 커밋 메시지에 반드시 포함시킵니다.
- 작업 시작 전 develop 브랜치를 최신 상태로 동기화합니다.
  ```
  git checkout develop
  git pull origin develop
  ```

## 3. 브랜치 명명 규칙

- **feature 브랜치**: `feature/JIRA-123_간략한_기능_설명`
- **bugfix 브랜치**: `bugfix/JIRA-123_간략한_버그_설명`
- **hotfix 브랜치**: `hotfix/JIRA-123_간략한_수정_설명`
- **release 브랜치**: `release/v1.2.3`

## 4. 커밋 규칙

- **하나의 티켓은 되도록 하나의 커밋으로 합니다.**
  - 작업 중간에는 필요에 따라 여러 커밋을 생성해도 됩니다.
  - PR 전에 `git rebase -i`를 사용하여 커밋을 정리합니다.
- 커밋 메시지 형식:
  ```
  [JIRA-123] 기능: 구현한 내용 요약
  
  - 상세 변경 내용 1
  - 상세 변경 내용 2
  ```
- 커밋 유형:
  - `기능`: 새로운 기능 추가
  - `수정`: 버그 수정
  - `리팩토링`: 코드 개선 (기능 변경 없음)
  - `문서`: 문서 변경
  - `테스트`: 테스트 코드 추가/수정
  - `스타일`: 코드 형식, 세미콜론 누락 등 (기능 변경 없음)
  - `설정`: 빌드 프로세스, 라이브러리 등 변경

## 5. 브랜치 워크플로우

### 5.1 기능 개발 (Feature)
1. develop 브랜치에서 feature 브랜치를 생성합니다.
   ```
   git checkout -b feature/JIRA-123_새기능 develop
   ```
2. 작업 완료 후 develop 브랜치에 PR을 생성합니다.
3. **리뷰어에게 꼭 리뷰를 받습니다.**
4. 승인 후 **자신의 Pull Request는 스스로 merge 합니다.**

### 5.2 릴리스 준비 (Release)
1. 출시 준비가 되면 develop에서 release 브랜치를 생성합니다.
   ```
   git checkout -b release/v1.2.3 develop
   ```
2. 버전 번호 수정 및 최종 테스트를 수행합니다.
3. 완료 후 main과 develop 양쪽에 머지합니다.
   ```
   git checkout main
   git merge --no-ff release/v1.2.3
   git tag -a v1.2.3 -m "버전 v1.2.3 출시"
   
   git checkout develop
   git merge --no-ff release/v1.2.3
   ```

### 5.3 긴급 수정 (Hotfix)
1. 출시된 버전에 문제 발생 시 main에서 hotfix 브랜치를 생성합니다.
   ```
   git checkout -b hotfix/JIRA-456_긴급수정 main
   ```
2. 수정 완료 후 main과 develop 양쪽에 머지합니다.
   ```
   git checkout main
   git merge --no-ff hotfix/JIRA-456_긴급수정
   git tag -a v1.2.4 -m "버전 v1.2.4 핫픽스"
   
   git checkout develop
   git merge --no-ff hotfix/JIRA-456_긴급수정
   ```

## 6. 핵심 원칙

- **커밋 그래프는 최대한 단순하게 가져갑니다.**
  - 불필요한 merge 커밋을 피합니다.
  - 로컬 브랜치는 자주 리베이스하여 최신 상태를 유지합니다.

- **서로 공유하는 브랜치의 커밋 그래프는 함부로 변경하지 않습니다.**
  - main, develop 브랜치에는 force push를 절대 사용하지 않습니다.
  - 이미 공유된 feature 브랜치도 가능한 force push를 피합니다.

- **리뷰어에게 꼭 리뷰를 받습니다.**
  - 모든 PR은 최소 1명 이상의 리뷰어 승인이 필요합니다.
  - 리뷰 의견에 대한 대응을 완료하기 전까지는 머지하지 않습니다.

- **자신의 Pull Request는 스스로 merge 합니다.**
  - 리뷰가 완료된 후 작업자 본인이 책임지고 머지합니다.
  - 머지 후 해당 브랜치는 삭제합니다.

## 7. 코드 리뷰 가이드라인

- 리뷰는 코드 품질 향상을 위한 것임을 기억합니다.
- 건설적인 피드백을 제공합니다.
- 코멘트에는 무엇이 문제이고, 왜 문제인지, 어떻게 개선할 수 있는지를 포함합니다.
- 리뷰 요청 시 변경 내용과 테스트 방법을 명확히 설명합니다.
- 모든 팀원들은 모든 PR에 대해서 변경된 부분의 코드를 확인합니다.
- 의견이 있는 코드에 대해서는 comment를 답니다.
- 모든 팀원들의 Review가 Approve되어야 PR을 merge 합니다.
- 따라서 모든 코드의 책임은 모든 구성원들의 책임입니다.

## 8. 충돌 해결

- 충돌 발생 시 원본 브랜치(develop)를 pull 후 로컬에서 충돌을 해결합니다.
- 충돌 해결 후 force push가 필요하다면 팀원들에게 사전 공지합니다.
- merge 충돌 해결은 가능한 원래 작업자가 담당합니다.
