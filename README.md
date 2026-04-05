# simsasukgo

Google Maps 기반 여행 계획 서비스. 북마크한 장소를 지도에서 시각화하고 실시간 영업 여부를 확인한다.

## 주요 기능

- 맛집 / 관광지 / 숙소를 북마크로 저장
- 지도 위에 북마크 위치 시각화
- 방문 여부 및 영업 상태 필터링
- Google Places API 기반 실시간 영업시간 조회

## 기술 스택

| 계층 | 기술 |
|------|------|
| Frontend | Next.js 16 + Vite + TypeScript |
| Backend | Nest.js + TypeORM + PostgreSQL |
| Testing | Jest |
| Monorepo | npm workspaces + Turborepo |
| 배포 | Vercel (Frontend), TBD (Backend) |

## 시작하기

```bash
npm install       # 의존성 설치
npm run dev       # Frontend + Backend 동시 실행
npm run test      # 테스트 실행
npm run build     # 빌드
```

## 프로젝트 구조

```
simsasukgo/
├── apps/
│   ├── web/        # Next.js Frontend
│   └── api/        # Nest.js Backend
├── packages/
│   ├── shared/     # 공유 유틸리티
│   └── database/   # TypeORM 설정 및 Migration
└── docs/           # 기획 / 개발 / 회고 문서
```

## 문서

- [개발 가이드](.claude/CLAUDE.md)
- [기여 규칙](.github/CONTRIBUTING.md)
- [기획 문서](docs/)

## 라이선스

MIT
