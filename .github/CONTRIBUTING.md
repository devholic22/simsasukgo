# simsasukgo 개발 헌법 (Constitution)

이 문서는 simsasukgo 프로젝트의 모든 개발 참여자가 따라야 하는 핵심 원칙과 워크플로우를 정의합니다.

## 1. 개발 워크플로우

### 1.1 시작: GitHub Issue 생성

**모든 개발 활동은 GitHub Issue에서 시작합니다.**

- Issue에는 다음 정보를 포함해야 합니다:
  - **설명**: 무엇을 개발/수정할 것인가?
  - **AC (Acceptance Criteria)**: 완료 조건은 무엇인가?
  - **라벨**: `feature`, `fix`, `refactor`, `docs` 등
  - **예상 소요 시간** (선택): 예상 난이도/시간

**Issue 번호 예시:**
- #123: 새로운 기능 개발
- #124: 버그 수정
- #125: 리팩터링

---

### 1.2 브랜치 전략

Issue가 생성되면 **`develop` 브랜치에서 새로운 브랜치를 생성**합니다.

#### 브랜치 명명 규칙

```
<type>/<issue-number>[-<sequence>]
```

**타입 (type):**
- `feat/` - 새로운 기능
- `fix/` - 버그 수정
- `refactor/` - 코드 리팩터링
- `docs/` - 문서 작성/수정
- `test/` - 테스트 추가/수정
- `chore/` - 설정, 의존성 등

**예시:**
- `feat/123` - Issue #123: 지도 표시 기능
- `fix/124` - Issue #124: 버그 수정
- `refactor/123-1` - Issue #123의 리팩터링 (순서 #1)
- `fix/123-2` - Issue #123의 두 번째 수정
- `docs/125-3` - Issue #125의 세 번째 문서 작성

#### 같은 Issue의 다중 브랜치

같은 Issue에서 여러 브랜치가 필요한 경우:
- 순차 번호 추가: `-1`, `-2`, `-3` 등
- 각 브랜치는 독립적인 PR로 진행
- Issue에서 모든 관련 브랜치/PR을 추적 (Issue 설명에 링크)

---

### 1.3 커밋 메시지 규칙

#### 커밋 메시지 형식

```
<type>: <subject>

<body>

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

**타입:** feat, fix, refactor, docs, test, chore

**예시:**
```
feat: Add map visualization for bookmarked locations

- Implement Google Maps integration
- Display real-time business hours status
- Add marker clustering for UX improvement

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

**중요:**
- 모든 커밋에는 `Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>` 하단부에 추가
  - Haiku를 사용할 때는 Claude Haiku
  - Sonnet을 사용할 때는 Claude Sonnet
  - Opus를 사용할 때는 Claude Opus
- AI가 주도한 개발임을 명시
- Conventional Commits 스타일 준수 (나중에 자동 버전 관리에 사용 가능)

---

### 1.4 Pull Request

#### PR 생성 시 체크리스트

- [ ] 브랜치가 최신 `develop`과 동기화됨
- [ ] 커밋 메시지가 규칙을 따름
- [ ] Issue 번호를 PR 설명에 포함 (`Closes #123`)
- [ ] 관련 문서 업데이트 (`docs/yyyy-mm-dd/develop/`)

#### PR 템플릿

PR 생성 시 자동으로 제공되는 템플릿을 따릅니다.
(`.github/pull_request_template.md` 참조)

---

### 1.5 Issue 완료 및 종합

Issue의 모든 관련 작업이 완료되면:

1. **Issue 설명에 모든 관련 PR/브랜치 링크 추가**
   ```markdown
   ## 관련 브랜치/PR
   - feat/123 (PR #456)
   - refactor/123-1 (PR #457)
   - docs/123-2 (PR #458)
   ```

2. **develop 브랜치로 모든 PR 병합**

3. **Issue 종료** (자동으로 `Closes` 키워드로 종료 가능)

---

## 2. 문서화 시스템

모든 개발 활동은 일자별로 기록됩니다.

### 2.1 디렉토리 구조

```
docs/
├── yyyy-mm-dd/        # 한국 기준 ISO 8601 형식 (예: 2026-04-05)
│   ├── plan/          # 기획 문서 (기능 설계, 요구사항, 아키텍처)
│   │   ├── 01-feature-name.md
│   │   └── README.md
│   ├── develop/       # 개발 기록 (구현 과정, PR 추적)
│   │   ├── 01-implementation.md
│   │   └── index.md
│   └── index.md       # 당일 요약
├── retro/             # 회고
│   ├── 2026-04-05.md  # 일일 회고
│   └── index.md
└── README.md          # 문서 네비게이션
```

### 2.2 기획 문서 (docs/yyyy-mm-dd/plan/)

각 기능/버그 수정의 상세 설명:

```markdown
# [번호]. 기능명 기획

## 📌 개요
- 무엇을 할 것인가?
- 왜 필요한가?

## 🎯 요구사항 (AC)
- [ ] AC 1: ...
- [ ] AC 2: ...

## 🏗 아키텍처/설계
- 전체 구조
- 핵심 컴포넌트
```

### 2.3 개발 기록 (docs/yyyy-mm-dd/develop/)

실제 구현 과정 기록

### 2.4 일일 회고 (docs/retro/yyyy-mm-dd.md)

개발을 마치는 시점에 하루를 정리

---

## 3. AI 협업 규칙

이 프로젝트는 **AI와 함께 개발**됩니다.

### 3.1 코드 소유권
- 모든 커밋에 `Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>` 명시
- AI와의 협업 과정을 문서로 기록 (선택사항)

### 3.2 코드 리뷰
- AI가 생성한 코드도 기존 규칙 준수 확인
- Issue의 AC(Acceptance Criteria) 충족 여부 확인

---

**마지막 업데이트:** 2026-04-05
