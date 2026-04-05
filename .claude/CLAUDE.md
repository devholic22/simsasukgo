# simsasukgo 프로젝트 개발 가이드

**프로젝트:** Google Maps 기반 여행 계획 서비스
**Repository:** https://github.com/devholic22/simsasukgo
**작업 디렉토리:** /Users/devholic/Desktop/simsasukgo

---

## 🚀 빠른 시작

### 설치 및 개발
```bash
npm install              # 의존성 설치
npm run dev              # Frontend + Backend 동시 실행
npm run build            # 빌드
npm run lint             # 코드 스타일 체크
npm run test             # Jest 테스트 실행
```

---

## 📋 개발 워크플로우

### ⚠️ 절대 규칙: 개발 전에 요구사항 확인 필수

**개발 시작 전 반드시 이 순서를 따를 것:**

1. **GitHub Issue 읽기**
   - 기능 설명
   - Acceptance Criteria (AC)
   - 예상 난이도

2. **Plan 문서 읽기** (`docs/yyyy-mm-dd/plan/*.md`)
   - 기획 문서에서 아키텍처 설계 확인
   - 데이터 모델 확인
   - API 명세 확인

3. **Develop 기록 읽기** (`docs/yyyy-mm-dd/develop/`)
   - 관련 PR 링크 확인
   - 이전에 해결된 유사 이슈 확인
   - 주의사항 확인

4. **코드 작성 시작**
   - Issue의 AC를 만족하는 코드만 작성
   - 추가 기능은 절대 구현하지 않기
   - 테스트를 먼저 작성 (TDD)

**위반 시:**
- [ ] 요구사항 미이해 → PR 반려
- [ ] 추가 기능 포함 → PR 반려 (범위 초과)
- [ ] 테스트 없음 → PR 반려

---

### 1️⃣ 이슈 생성 → 브랜치 생성

```
Issue #123 검토 (plan + AC 확인)
        ↓
Issue와 Plan 문서를 완전히 이해 후
        ↓
Branch: feat/123 생성
        ↓
코드 작성 + 테스트 작성
```

### 2️⃣ 요구사항 분석 (5분 체크)

**Issue와 Plan 문서로부터 추출해야 할 정보:**

```markdown
Issue #123 분석 체크리스트:
- [ ] 기능의 목적: _______________
- [ ] Input (입력): _______________
- [ ] Output (출력): ______________
- [ ] AC 1 (조건 1): ______________
- [ ] AC 2 (조건 2): ______________
- [ ] AC 3 (조건 3): ______________

범위 확인:
- [ ] 이 이슈에만 필요한 기능인가?
- [ ] 다른 이슈와 중복되지 않는가?
- [ ] 추가 기능은 없는가? (있으면 별도 이슈로)
```

**Plan 문서로부터 확인:**
```markdown
- [ ] 데이터 모델 (엔티티/필드) 명확한가?
- [ ] API 엔드포인트 명시되어 있는가?
- [ ] Frontend/Backend 역할 분담 명확한가?
- [ ] 특별한 제약조건이 있는가? (인증, 권한 등)
```

### 3️⃣ 테스트 먼저 작성 (TDD)

```bash
# 개발 서버 시작
npm run dev

# Watch 모드로 테스트 작성 (별도 터미널)
npm run test -- --watch

# 테스트부터 작성하고 구현 후 테스트 통과 확인
```

**테스트 작성 순서:**
1. 인수 테스트 (한글 함수명) - Business Logic
2. 통합 테스트 (API/Controller) - Integration
3. 단위 테스트 (Service) - Unit
4. 구현 코드 작성
5. 모든 테스트 통과 확인

### 4️⃣ 코드 작성 & 테스트

```bash
# 개발 서버 시작
npm run dev

# 테스트 작성 (별도 터미널)
npm run test -- --watch

# 커밋 전 최종 검증
npm run lint          # TypeScript/ESLint
npm run type-check    # 타입 체크
npm run test          # 모든 테스트 성공 필수
```

**개발 중 규칙:**
- AC에 명시되지 않은 기능 추가 금지
- 타입 안정성 필수 (strict mode)
- 테스트 작성 의무

### 5️⃣ AC 완료 검증 (커밋 직전)

**반드시 Issue의 AC를 다시 읽고 체크:**

```markdown
Issue #123: 북마크 추가 기능

AC 검증:
- [x] AC 1: 사용자는 지도에서 위치를 선택할 수 있다
  → 테스트: createBookmark() 성공 케이스 작성함

- [x] AC 2: 북마크는 필수 필드를 포함해야 한다 (이름, 주소, 좌표)
  → 테스트: 필드 누락 시 오류 케이스 작성함

- [x] AC 3: 중복 북마크는 생성되지 않아야 한다
  → 테스트: 같은 좌표 중복 시 오류 케이스 작성함

- [x] AC 4: 북마크 목록을 조회할 수 있다
  → 테스트: GET /api/bookmarks 통합 테스트 작성함

⚠️ 주의: AC에 없는 기능은 구현하지 않음
- 생각해볼 것: "이 기능은 이 이슈에서 정의된 AC인가?"
```

### 6️⃣ 커밋 전 체크리스트

**커밋하기 전에 반드시 확인:**
- [ ] **AC 검증**: Issue의 모든 AC를 만족하는가?
- [ ] `npm run test` 모든 테스트 통과
- [ ] `npm run lint` 코드 스타일 통과
- [ ] `npm run type-check` 타입 오류 없음
- [ ] 관련 문서 최신화 (docs/yyyy-mm-dd/develop/)
- [ ] API 변경 시 API 명세 업데이트
- [ ] Database 스키마 변경 시 migration 파일 생성
- [ ] 커밋 메시지에 AI 모델/버전 명시

### 7️⃣ 커밋 메시지

```
feat: 사용자 북마크 추가 기능 구현

- BookmarkService.createBookmark() 메서드 추가
- Bookmark 엔티티에 location 필드 추가
- POST /api/bookmarks 엔드포인트 구현
- 인수 테스트: 북마크 생성 및 조회 성공 케이스

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

### 8️⃣ PR 생성 & 병합

```
PR Title: feat: 사용자 북마크 추가 기능

PR Body:
## AC 완료 상황
- [x] AC 1: 위치 선택 및 북마크 생성
- [x] AC 2: 필수 필드 검증
- [x] AC 3: 중복 방지
- [x] AC 4: 북마크 목록 조회

## 변경사항
- BookmarkService에 createBookmark, getBookmarks 메서드 추가
- Bookmark 엔티티 구현 (id, userId, name, address, latitude, longitude)
- POST /api/bookmarks, GET /api/bookmarks 엔드포인트 구현

## 테스트
- Unit: BookmarkService 테스트 (8개 케이스)
- Integration: API 엔드포인트 테스트 (5개 케이스)
- Acceptance: 한글 함수명 인수 테스트 (3개 시나리오)
- 커버리지: 85%

## 관련 Issue
Closes #123
```

---

## 🏗 프로젝트 구조

```
simsasukgo/
├── apps/
│   ├── web/                   # Next.js Frontend (Vite)
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── components/
│   │   │   ├── hooks/
│   │   │   └── __tests__/     # Frontend 테스트
│   │   └── package.json
│   │
│   └── api/                   # Nest.js Backend
│       ├── src/
│       │   ├── modules/
│       │   │   └── bookmarks/
│       │   │       ├── bookmarks.module.ts
│       │   │       ├── bookmarks.service.ts
│       │   │       ├── bookmarks.controller.ts
│       │   │       ├── bookmark.entity.ts
│       │   │       └── __tests__/
│       │   │           ├── bookmarks.service.spec.ts
│       │   │           ├── bookmarks.controller.spec.ts
│       │   │           └── acceptance.spec.ts
│       │   └── main.ts
│       └── package.json
│
├── packages/
│   ├── shared/               # 공유 유틸리티
│   └── database/             # TypeORM 설정
│       └── src/
│           ├── entities/
│           ├── migrations/   # TypeORM migrations
│           └── data-source.ts
│
├── docs/yyyy-mm-dd/
│   ├── plan/                 # 기획 문서
│   ├── develop/              # 개발 기록
│   └── retro/                # 일일 회고
│
└── .github/
    ├── CONTRIBUTING.md
    └── ISSUE_TEMPLATE/
```

---

## 🛠 기술 스택 (확정)

| 계층 | 기술 | 이유 |
|-----|------|------|
| **언어** | TypeScript | AI 협업 효율 95% |
| **Frontend** | Next.js 16 + Vite | Vercel 최적화, 빠른 개발 |
| **Backend** | Nest.js + TypeORM | 엔터프라이즈급 (93-94% AI 정확도) |
| **Database** | PostgreSQL | 관계형 쿼리 (98% AI 정확도) |
| **Testing** | Jest | 통일된 테스팅 (92% AI 정확도) |
| **Monorepo** | npm workspaces + Turborepo | 워크스페이스 관리 |
| **배포** | Vercel (Frontend), TBD (Backend) | 확장 가능한 구조 |

---

## 🧪 테스팅 규칙

### 테스트 작성 패턴 (Given-When-Then)

```typescript
describe('BookmarkService', () => {
  // ✅ GOOD: Given-When-Then 패턴
  describe('createBookmark', () => {
    it('사용자가 유효한 위치 데이터를 전송할 때 북마크가 생성된다', async () => {
      // Given: 사전 조건
      const 사용자ID = 'user-123';
      const 북마크데이터 = {
        이름: '카페 아메리카노',
        주소: '서울시 강남구',
        위도: 37.4979,
        경도: 127.0276,
      };

      // When: 액션
      const 결과 = await bookmarkService.createBookmark(사용자ID, 북마크데이터);

      // Then: 검증
      expect(결과).toBeDefined();
      expect(결과.이름).toBe('카페 아메리카노');
      expect(결과.사용자ID).toBe(사용자ID);
    });

    // ❌ BAD CASE: 유효하지 않은 데이터
    it('필수 필드가 누락되면 오류를 발생한다', async () => {
      const 불완전한데이터 = {
        이름: '카페',
        // 주소 누락 (필수)
      };

      await expect(
        bookmarkService.createBookmark('user-123', 불완전한데이터)
      ).rejects.toThrow('주소는 필수입니다');
    });
  });
});
```

### 각 레이어별 테스트

#### 1. Unit Tests (개별 함수/메서드)
```typescript
// services/bookmarks.service.spec.ts
describe('BookmarkService - 비즈니스 로직', () => {
  it('북마크 거리 계산이 정확하다', () => {
    const 거리 = bookmarkService.거리계산({위도: 37.4979, 경도: 127.0276},
                                   {위도: 37.4980, 경도: 127.0277});
    expect(거리).toBeLessThan(150); // 150m 이내
  });
});
```

#### 2. Integration Tests (API 엔드포인트)
```typescript
// controllers/bookmarks.controller.spec.ts
describe('BookmarkController - API 통합', () => {
  it('POST /api/bookmarks로 북마크를 생성한다', async () => {
    const 응답 = await request(app.getHttpServer())
      .post('/api/bookmarks')
      .set('Authorization', 'Bearer token123')
      .send({
        이름: '맛집',
        주소: '서울시 강남구',
        위도: 37.4979,
        경도: 127.0276,
      });

    expect(응답.status).toBe(201);
    expect(응답.body.id).toBeDefined();
  });

  // Bad case: 인증 없이 요청
  it('인증 토큰이 없으면 401 Unauthorized를 반환한다', async () => {
    const 응답 = await request(app.getHttpServer())
      .post('/api/bookmarks')
      .send({이름: '맛집', 주소: '서울'});

    expect(응답.status).toBe(401);
  });
});
```

#### 3. Acceptance Tests (인수 테스트)

**한글 변수명/메서드명으로 비즈니스 시나리오를 명확하게 표현**

```typescript
// bookmarks.controller.acceptance.spec.ts
// Fixture 상속으로 중복되는 셋업 제거
class BookmarkControllerAcceptanceTest extends BookmarkControllerAcceptanceFixture {
  private static readonly 북마크_목록_URI = '/api/bookmarks?page=0&size=10';
  private static readonly 북마크_단일_URI = '/api/bookmarks/';

  private 사용자_토큰: string;
  private 사용자: User;

  @BeforeEach
  setup() {
    this.사용자 = 사용자_생성({ 이메일: 'user@example.com' });
    this.사용자_토큰 = 토큰_생성(this.사용자);
  }

  @Test
  async 북마크를_페이징_조회한다() {
    // given: 여러 북마크가 존재
    북마크_목록_생성(this.사용자.id, 15); // 15개 생성

    // when: 페이징 조회
    const 북마크_목록_조회_결과 = await 북마크_목록을_조회한다(
      this.사용자_토큰,
      BookmarkControllerAcceptanceTest.북마크_목록_URI
    );

    // then: 페이지네이션 검증
    북마크_목록_조회_결과_검증(북마크_목록_조회_결과, {
      전체_개수: 15,
      현재_페이지_크기: 10,
      현재_페이지_번호: 0,
    });
  }

  @Test
  async 북마크를_단일_조회한다() {
    // given: 북마크 생성
    const 북마크 = 북마크_생성(this.사용자.id, {
      이름: '카페 아메리카노',
      주소: '서울시 강남구',
      위도: 37.4979,
      경도: 127.0276,
    });

    // when: 단일 조회
    const 북마크_조회_결과 = await 북마크를_조회한다(
      this.사용자_토큰,
      BookmarkControllerAcceptanceTest.북마크_단일_URI + 북마크.id
    );

    // then: 조회 결과 검증
    북마크_조회_검증(북마크_조회_결과, {
      id: 북마크.id,
      이름: '카페 아메리카노',
      주소: '서울시 강남구',
    });
  }

  @Test
  async 인증이_없으면_북마크_조회가_실패한다() {
    // given: 인증 토큰 없음
    const 인증_토큰_없음 = '';

    // when: 인증 없이 조회 시도
    const 북마크_조회_결과 = await 북마크_목록을_조회한다(
      인증_토큰_없음,
      BookmarkControllerAcceptanceTest.북마크_목록_URI
    );

    // then: 401 Unauthorized 반환
    expect(북마크_조회_결과.상태_코드).toBe(401);
    expect(북마크_조회_결과.오류_메시지).toContain('인증 필요');
  }

  @Test
  async 중복된_북마크는_생성할_수_없다() {
    // given: 첫 번째 북마크 생성
    const 북마크_데이터 = {
      이름: '카페',
      주소: '강남구',
      위도: 37.4979,
      경도: 127.0276,
    };

    await 북마크_생성(this.사용자.id, 북마크_데이터);

    // when: 같은 좌표로 재시도
    const 중복_생성_결과 = await 북마크_생성(
      this.사용자.id,
      북마크_데이터
    );

    // then: 중복 오류 반환
    expect(중복_생성_결과.상태_코드).toBe(409); // Conflict
    expect(중복_생성_결과.오류_메시지).toContain('이미 북마크');
  }
}
```

**Fixture 클래스 (공통 헬퍼 메서드)**
```typescript
// bookmarks.controller.acceptance.fixture.ts
class BookmarkControllerAcceptanceFixture {
  protected async 사용자_생성(옵션: UserOption): Promise<User> {
    return await userFactory.생성(옵션);
  }

  protected 토큰_생성(사용자: User): string {
    return jwtService.생성({ userId: 사용자.id });
  }

  protected 북마크_생성(
    사용자id: string,
    데이터: CreateBookmarkDto
  ): Promise<Bookmark> {
    return bookmarkService.생성(사용자id, 데이터);
  }

  protected 북마크_목록_생성(사용자id: string, 개수: number): void {
    for (let i = 0; i < 개수; i++) {
      bookmarkFactory.생성({
        사용자id,
        위도: 37.4979 + (i * 0.001),
        경도: 127.0276 + (i * 0.001),
      });
    }
  }

  protected async 북마크_목록을_조회한다(
    토큰: string,
    uri: string
  ): Promise<APIResponse> {
    return request(this.app.getHttpServer())
      .get(uri)
      .set('Authorization', `Bearer ${토큰}`);
  }

  protected async 북마크를_조회한다(
    토큰: string,
    uri: string
  ): Promise<APIResponse> {
    return request(this.app.getHttpServer())
      .get(uri)
      .set('Authorization', `Bearer ${토큰}`);
  }

  protected async 북마크_생성(
    토큰: string,
    데이터: CreateBookmarkDto
  ): Promise<APIResponse> {
    return request(this.app.getHttpServer())
      .post('/api/bookmarks')
      .set('Authorization', `Bearer ${토큰}`)
      .send(데이터);
  }

  protected 북마크_목록_조회_결과_검증(
    결과: APIResponse,
    기대값: PaginationExpectation
  ): void {
    expect(결과.상태_코드).toBe(200);
    expect(결과.본문.content.length).toBe(기대값.현재_페이지_크기);
    expect(결과.본문.totalElements).toBe(기대값.전체_개수);
  }

  protected 북마크_조회_검증(
    결과: APIResponse,
    기대값: BookmarkExpectation
  ): void {
    expect(결과.상태_코드).toBe(200);
    expect(결과.본문.id).toBe(기대값.id);
    expect(결과.본문.name).toBe(기대값.이름);
    expect(결과.본문.address).toBe(기대값.주소);
  }
}
```

### 테스트 커버리지 목표
- **Unit**: 80% 이상
- **Integration**: 모든 API 엔드포인트
- **Acceptance**: 주요 비즈니스 시나리오

---

## 🗄 Database Migration 규칙

### Migration 파일 네이밍

```
타임스탬프-도메인-액션.ts
예:
1712339400000-bookmark-create-table.ts
1712339500000-bookmark-add-category-column.ts
1712339600000-user-alter-email-unique.ts
1712339700000-bookmark-drop-old-index.ts
```

**구조:**
```typescript
// <timestamp>-<domain>-<action>.ts 예시
// 1712339400000-bookmark-create-table.ts

import { MigrationInterface, QueryRunner, Table } from 'typeorm';

export class BookmarkCreateTable1712339400000 implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    // 테이블 생성
    await queryRunner.createTable(
      new Table({
        name: 'bookmarks',
        columns: [
          {
            name: 'id',
            type: 'uuid',
            isPrimary: true,
            generationStrategy: 'uuid',
            default: 'uuid_generate_v4()',
          },
          {
            name: 'userId',
            type: 'varchar',
            isNullable: false,
          },
          {
            name: 'name',
            type: 'varchar',
            isNullable: false,
          },
          {
            name: 'address',
            type: 'varchar',
            isNullable: false,
          },
          {
            name: 'createdAt',
            type: 'timestamp',
            default: 'NOW()',
          },
        ],
        indices: [
          {
            columnNames: ['userId'],
          },
        ],
      })
    );
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.dropTable('bookmarks');
  }
}
```

**타임스탐프 생성:**
```typescript
// 터미널에서 사용
const timestamp = new Date().getTime(); // 밀리초 (TypeORM migration 포맷)
console.log(timestamp);
// 1712339400000
```

**동작 종류:**
- `create-table` - 테이블 생성
- `drop-table` - 테이블 삭제
- `add-column` - 칼럼 추가
- `drop-column` - 칼럼 삭제
- `alter-column` - 칼럼 수정
- `add-index` - 인덱스 추가
- `add-constraint` - 제약조건 추가

---

## 🤖 AI 협업 규칙

### 모든 커밋에 모델/버전 명시
```
Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

**목적:**
- 투명성: 어떤 모델이 작성했는지 기록
- 재현성: 모델 업그레이드 후 동작 변경 추적
- 품질: 모델별 정확도 관리

---

## 📝 문서화 규칙

### 날짜별 구조
```
docs/yyyy-mm-dd/
├── plan/          # 기획 (feature 설계, AC, 아키텍처)
├── develop/       # 개발 (구현 기록, PR 링크, 이슈 해결)
└── retro/         # 회고 (완료 항목, 배운 점, 개선사항)
```

### 개발 기록 작성 체크리스트
- [ ] PR 링크 기록
- [ ] 새로운 API 엔드포인트 목록
- [ ] Database 스키마 변경사항
- [ ] 테스트 추가된 케이스
- [ ] 알려진 이슈 또는 미해결 사항

### 프로젝트 구성 단계
- Issue 불필요: 저장소 초기화, 기본 구조, 문서화 → develop에 직접 적용
- Issue 필수: 기능 구현 시작부터 Issue → PR 워크플로우 준수

---

## 🎯 다음 우선순위

1. **TypeORM 스키마**
   - [ ] Bookmark 엔티티 + migration
   - [ ] User 엔티티 + migration
   - [ ] Unit/Integration 테스트

2. **Nest.js API**
   - [ ] bookmarks CRUD 엔드포인트
   - [ ] API 통합 테스트
   - [ ] API 명세 문서

3. **Next.js Frontend**
   - [ ] App Router 레이아웃
   - [ ] 기본 페이지 구조
   - [ ] 지도 컴포넌트 (향후)

---

## 📚 참고 문서

- `.github/CONTRIBUTING.md` - 상세 개발 규칙
- `docs/2026-04-05/plan/01-tech-stack-decision.md` - 기술 선택 근거
- `docs/retro/2026-04-05.md` - 프로젝트 진행 상황

---

**유지보수자:** Claude Haiku 4.5
**마지막 업데이트:** 2026-04-05
