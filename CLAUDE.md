# simsasukgo 프로젝트 개발 가이드

**프로젝트:** Google Maps 기반 여행 계획 서비스 (북마크 위치 시각화 + 실시간 영업시간)

**Repository:** https://github.com/devholic22/simsasukgo

---

## 🚀 빠른 시작

### 설치
```bash
git clone https://github.com/devholic22/simsasukgo.git
cd simsasukgo
npm install
```

### 개발 서버 시작
```bash
npm run dev              # Frontend + Backend 동시 실행
npm run dev:web         # Frontend만
npm run dev:api         # Backend만
```

### 빌드 & 배포
```bash
npm run build            # 모든 패키지 빌드
npm run lint             # 코드 스타일 체크
npm run type-check       # TypeScript 타입 체크
npm run test             # Jest 테스트 실행
```

---

## 📋 개발 규칙

### 1. Issue → Branch → PR → Merge 워크플로우

**절대 규칙:** 모든 개발은 GitHub Issue에서 시작합니다.

```
Issue 생성 (#123)
    ↓
develop에서 브랜치 생성 (feat/123)
    ↓
코드 작성 + 커밋 (Co-Authored-By 명시)
    ↓
PR 생성 (Closes #123)
    ↓
develop 병합
```

### 2. 브랜치 명명

```
<type>/<issue-number>[-<sequence>]

예:
- feat/123          (Issue #123 기능 개발)
- fix/124           (Issue #124 버그 수정)
- refactor/123-1    (Issue #123 리팩터링)
- docs/125-2        (Issue #125 문서 작성)
```

### 3. 커밋 메시지

```
<type>: <subject>

<body>

Co-Authored-By: <Model Name> <Version> <noreply@anthropic.com>
```

**모델 버전 명시:**
- 예: `Claude Haiku 4.5 <noreply@anthropic.com>`
- 예: `Claude Opus 4.6 <noreply@anthropic.com>`

### 4. 프로젝트 초기화 단계

**프로젝트 구성 중에는 Issue 불필요:**
- 기본 구조 설정, 패키지 설정, 문서화 등은 develop에서 직접 진행
- 기능 구현 시작부터는 반드시 Issue → PR 워크플로우 준수

---

## 🏗 프로젝트 구조

```
simsasukgo/
├── .github/                    # GitHub 설정
│   ├── CONTRIBUTING.md         # 개발 헌법
│   ├── pull_request_template.md
│   └── ISSUE_TEMPLATE/
│       ├── feature.md
│       ├── fix.md
│       └── refactor.md
│
├── apps/
│   ├── web/                    # Next.js Frontend
│   │   ├── src/
│   │   │   ├── app/           # App Router
│   │   │   ├── components/
│   │   │   └── hooks/
│   │   ├── package.json
│   │   └── tsconfig.json
│   │
│   └── api/                    # Nest.js Backend
│       ├── src/
│       │   ├── main.ts
│       │   ├── app.module.ts
│       │   └── modules/       # Feature modules
│       ├── package.json
│       └── tsconfig.json
│
├── packages/
│   ├── shared/                 # 공유 유틸리티
│   │   └── package.json
│   │
│   └── database/               # TypeORM 설정
│       ├── src/
│       │   ├── entities/
│       │   ├── migrations/
│       │   └── data-source.ts
│       └── package.json
│
├── docs/
│   ├── yyyy-mm-dd/            # 날짜별 문서
│   │   ├── plan/              # 기획 문서
│   │   ├── develop/           # 개발 기록
│   │   └── index.md
│   ├── retro/                 # 일일 회고
│   ├── README.md              # 문서 네비게이션
│   └── ...
│
├── package.json               # Root monorepo config
├── turbo.json                 # Turborepo 설정
├── tsconfig.base.json         # TypeScript 기본 설정
├── vercel.json                # Vercel 배포 설정
├── CLAUDE.md                  # 이 파일
├── CONTRIBUTING.md            # (참고만 - 자세한 내용은 .github/CONTRIBUTING.md)
└── ...
```

---

## 🛠 기술 스택

| 계층 | 기술 | 버전 | 이유 |
|-----|------|------|------|
| **언어** | TypeScript | 5.0+ | AI 협업 효율성 (95% 정확도) |
| **Frontend** | Next.js | 16+ | Vercel 최적화, Server Components |
| **Frontend 빌드** | Vite | 5.0+ | 빠른 개발 루프 |
| **Backend** | Nest.js | 10+ | 엔터프라이즈급 설계, 데코레이터 패턴 (93% 정확도) |
| **Database** | PostgreSQL | 15+ | 관계형 쿼리, AI SQL 정확도 (98%) |
| **ORM** | TypeORM | 0.3+ | 데코레이터 기반, AI 친화적 (94% 정확도) |
| **Testing** | Jest | 29+ | 통일된 테스팅, LLM 정확도 (92%) |
| **Monorepo** | Turborepo | 2.0+ | 워크스페이스 조율, 캐싱 |
| **배포** | Vercel | - | Frontend (원클릭 배포) |
| **배포** | Railway/Heroku/AWS | - | Backend (추후 결정) |

---

## 📚 문서화 시스템

### 일자별 구조
```
docs/yyyy-mm-dd/              # 한국 기준 ISO 8601 (예: 2026-04-05)
├── plan/                     # 기획 문서
│   ├── 01-feature-name.md
│   ├── 02-another-feature.md
│   └── index.md             # 네비게이션
├── develop/                  # 개발 기록
│   ├── 01-implementation.md
│   └── index.md             # 개발 진행 현황
└── index.md                 # 당일 요약
```

### 문서 작성 가이드
- **plan/**: 기능 설계, 요구사항 (AC), 아키텍처
- **develop/**: 구현 과정, PR 링크, 이슈 해결 기록
- **retro/**: 일일 회고 (완료 항목, 배운 점, 내일 계획)

---

## 🤖 AI 협업 규칙

### 모든 커밋에 AI 모델 명시
```
Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

**목적:**
- 투명성: 어떤 AI 모델이 작성했는지 추적
- 재현성: 모델 업그레이드 시 동작 변경 감지
- 품질 관리: 모델별 코드 생성 정확도 기록

### 기술별 AI 정확도
- TypeScript: ~95%
- SQL (PostgreSQL): ~98%
- Jest 테스트: ~92%
- Nest.js (데코레이터): ~93%
- TypeORM (엔티티): ~94%

---

## 🔐 보안 & 환경변수

### 필수 환경변수

**Frontend (.env.local):**
```
NEXT_PUBLIC_API_URL=http://localhost:3000
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=...
```

**Backend (.env):**
```
DATABASE_URL=postgresql://user:pass@localhost:5432/simsasukgo
GOOGLE_MAPS_API_KEY=...
NODE_ENV=development
```

**주의:**
- `.env` 및 `.env.local` 은 절대 커밋하지 말 것
- `.env.example` 파일로 예제만 관리
- Vercel의 Environment Variables에서 관리

---

## 🚢 배포

### Frontend (Vercel)
```bash
vercel deploy           # 프리뷰 배포
vercel deploy --prod    # 프로덕션 배포
```

### Backend (Railway/Heroku/AWS)
- 추후 결정 예정
- 현재는 로컬 개발 중심

---

## 🎯 다음 우선순위

1. **TypeORM 스키마 설계**
   - Bookmark, User 엔티티
   - Migration 파일 생성

2. **Nest.js API 구현**
   - bookmarks CRUD 엔드포인트
   - Google Places API 통합

3. **Next.js Frontend**
   - App Router 레이아웃
   - 페이지 구조
   - 지도 컴포넌트 (향후)

---

## 📞 문의

- Issue 추적: [GitHub Issues](https://github.com/devholic22/simsasukgo/issues)
- 개발 규칙: `.github/CONTRIBUTING.md`
- 기술 결정: `docs/yyyy-mm-dd/plan/01-tech-stack-decision.md`

---

**마지막 업데이트:** 2026-04-05
**유지보수자:** Claude Haiku 4.5
