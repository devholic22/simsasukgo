# 2. Google Maps 통합 및 기능 설계

## 📌 개요

simsasukgo의 핵심 기능은 사용자가 관심 있는 장소(맛집, 관광지, 숙박지 등)를 지도에 북마크하고, 각 장소의 실시간 영업시간을 확인하는 것입니다.

**목표:**
- Google Maps를 통한 지각적인 위치 시각화
- 사용자 북마크 관리 및 영속성
- Google Places API를 이용한 실시간 비즈니스 시간 정보

**결정일:** 2026-04-05
**상태:** ✅ 계획 수립

---

## 🗺 Google Maps 통합 전략

### 1. Google Maps API 선택지 분석

| API | 용도 | 우리 선택 | 이유 |
|-----|------|---------|------|
| **Google Maps Embed API** | 간단한 지도 임베드 | ❌ 제한적 | 인터랙티브 기능 부족 |
| **Google Maps JavaScript API** | 상호작용 가능한 지도 | ✅ 메인 | 마커, 정보창, 커스텀 기능 가능 |
| **Google Places API** | 장소 정보 (영업시간 등) | ✅ 필수 | 실시간 비즈니스 정보 |
| **Google Maps Geocoding API** | 주소 ↔ 좌표 변환 | ✅ 보조 | 주소 검색 기능 지원 |

**최종 결정:**
- **Frontend**: Google Maps JavaScript API + @react-google-maps/api (React 라이브러리)
- **Backend**: Google Places API (Node.js 클라이언트)
- **비용 구조**: 월별 지도 렌더링 비용 + Places API 쿼리 비용 (실제 사용량 기반)

---

## 🎯 핵심 기능 명세

### Feature 1: 위치 북마크 (Location Bookmarking)

**설명:** 사용자가 지도에서 위치를 선택하여 북마크 저장

**기능:**
- 지도 클릭으로 마커 생성
- 마커 정보창에 장소명, 주소, 메모 입력
- 북마크 저장 (DB 영속성)
- 북마크 목록 조회 (세션별 또는 전체)
- 북마크 수정/삭제

**데이터 모델:**
```typescript
// Location (북마크된 장소)
interface Bookmark {
  id: string;
  userId: string;
  placeId?: string; // Google Places ID
  name: string;
  address: string;
  latitude: number;
  longitude: number;
  notes?: string; // 사용자 메모
  category?: string; // 식당, 카페, 숙박 등
  rating?: number; // Google Places 평점
  createdAt: Date;
  updatedAt: Date;
}
```

**API 엔드포인트:**
```
POST   /api/bookmarks                  # 새 북마크 생성
GET    /api/bookmarks                  # 사용자 북마크 목록 조회
GET    /api/bookmarks/:id              # 특정 북마크 조회
PATCH  /api/bookmarks/:id              # 북마크 수정
DELETE /api/bookmarks/:id              # 북마크 삭제
```

---

### Feature 2: 실시간 영업시간 조회 (Business Hours)

**설명:** Google Places API를 통해 장소의 현재 영업 상태 및 시간 확인

**기능:**
- 마커 클릭 시 영업시간 정보 표시
- 현재 영업 중 여부 표시 (Open/Closed)
- 오늘의 영업시간, 주간 영업시간 표시
- 휴무일 안내

**데이터 구조:**
```typescript
interface BusinessHours {
  placeId: string;
  name: string;
  isOpen: boolean; // 현재 영업 중인지
  openingHours?: string[]; // ["월: 10:00 ~ 22:00", "화: 10:00 ~ 22:00", ...]
  todayHours?: string; // "10:00 ~ 22:00"
  closedOn?: string; // 휴무일
  note?: string; // "매주 월요일 정기 휴무"
  lastUpdated: Date;
}
```

**API 엔드포인트:**
```
GET /api/places/:placeId/business-hours    # 특정 장소의 영업시간 조회
```

**Google Places API 호출:**
- Backend에서만 호출 (API Key 보안)
- 응답 캐싱 (30분 TTL) - Runtime Cache 사용
- 사용자 조회 시마다 새로 요청하지 않음

---

### Feature 3: 지도 시각화 (Map Visualization)

**설명:** 북마크된 모든 위치를 지도에 표시하고 인터랙션 제공

**기능:**
- 여러 마커 동시 표시
- 마커 클러스터링 (많은 마커가 겹칠 때)
- 마커 색상/아이콘으로 카테고리 구분
- 마커 클릭 시 정보창 표시 (장소명, 주소, 영업시간, 메모)
- 지도 검색 (장소명 또는 주소로 필터링)
- 지도 중심 제어 (특정 마커로 이동)

**마커 카테고리:**
```
🍽️  음식점 (Restaurant)
☕  카페 (Cafe)
🏨  숙박 (Hotel)
🎭  관광지 (Attraction)
🛍️  쇼핑 (Shopping)
⚕️  병원 (Hospital)
기타 (Other)
```

---

## 🔧 기술 구현 방안

### Frontend (Next.js + React)

**라이브러리:**
- `@react-google-maps/api` - React에서 Google Maps 사용
- `@react-google-maps/marker-clusterer` - 마커 클러스터링
- `shadcn/ui` - UI 컴포넌트

**컴포넌트 구조:**
```
app/
├── map/
│   └── page.tsx                      # 지도 메인 페이지
├── components/
│   ├── Map.tsx                       # <GoogleMap> 래퍼
│   ├── BookmarkMarker.tsx            # 마커 컴포넌트
│   ├── BookmarkInfoWindow.tsx        # 정보창
│   ├── BookmarkList.tsx              # 사이드바 북마크 목록
│   ├── BusinessHoursDisplay.tsx      # 영업시간 표시
│   └── AddBookmarkForm.tsx           # 북마크 추가 폼
└── hooks/
    ├── useBookmarks.ts               # 북마크 CRUD 훅
    └── useBusinessHours.ts           # 영업시간 조회 훅
```

**지도 초기화:**
```typescript
// API Key는 NEXT_PUBLIC_GOOGLE_MAPS_API_KEY 환경변수에서 로드
// Key 제한 설정:
// - HTTP referrer 제한 (https://simsasukgo.com)
// - JavaScript Maps API만 허용
// - Places API 사용 불가 (frontend에서는 Places API 미사용)
```

---

### Backend (Nest.js + TypeORM)

**모듈 구조:**
```
src/
├── bookmarks/
│   ├── bookmarks.controller.ts       # REST 엔드포인트
│   ├── bookmarks.service.ts          # 비즈니스 로직
│   └── bookmark.entity.ts            # TypeORM 엔티티
├── places/
│   ├── places.service.ts             # Google Places API 통합
│   └── business-hours.cache.ts       # 캐싱 로직
└── config/
    └── google-maps.config.ts         # Google Maps API 설정
```

**Google Places API 호출:**
```typescript
// places.service.ts
import { Client } from "@googlemaps/js-client-library";

@Injectable()
export class PlacesService {
  private client: Client;

  async getBusinessHours(placeId: string): Promise<BusinessHours> {
    // 1. Runtime Cache에서 조회 (key: `business-hours:${placeId}`)
    // 2. 캐시 미스 시 Google Places API 호출
    // 3. 결과 캐싱 (30분 TTL)
    // 4. 응답 반환
  }
}
```

---

### Database (PostgreSQL + TypeORM)

**Bookmark 엔티티:**
```typescript
@Entity('bookmarks')
export class Bookmark {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  userId: string;

  @Column({ nullable: true })
  placeId: string; // Google Places ID

  @Column()
  name: string;

  @Column()
  address: string;

  @Column('decimal', { precision: 10, scale: 8 })
  latitude: number;

  @Column('decimal', { precision: 11, scale: 8 })
  longitude: number;

  @Column({ nullable: true, type: 'text' })
  notes: string;

  @Column({ nullable: true })
  category: string;

  @Column({ nullable: true, type: 'float' })
  rating: number;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @Index(['userId'])
  @Index(['placeId'])
}
```

**마이그레이션:**
```bash
npx typeorm migration:create ./src/migrations/CreateBookmarkTable
```

---

## 📊 API 응답 예시

### GET /api/bookmarks
```json
{
  "data": [
    {
      "id": "uuid-1",
      "userId": "user-123",
      "placeId": "ChIJN1blFLsB6EsR...",
      "name": "카페 아메리카노",
      "address": "서울시 강남구 테헤란로 123",
      "latitude": 37.4979,
      "longitude": 127.0276,
      "category": "카페",
      "rating": 4.5,
      "notes": "라떼 추천",
      "createdAt": "2026-04-05T10:00:00Z"
    }
  ]
}
```

### GET /api/places/:placeId/business-hours
```json
{
  "data": {
    "placeId": "ChIJN1blFLsB6EsR...",
    "name": "카페 아메리카노",
    "isOpen": true,
    "todayHours": "09:00 ~ 22:00",
    "openingHours": [
      "월: 09:00 ~ 22:00",
      "화: 09:00 ~ 22:00",
      "수: 09:00 ~ 22:00",
      "목: 09:00 ~ 22:00",
      "금: 09:00 ~ 23:00",
      "토: 10:00 ~ 23:00",
      "일: 10:00 ~ 21:00"
    ],
    "closedOn": null,
    "lastUpdated": "2026-04-05T14:30:00Z"
  }
}
```

---

## 🔐 보안 고려사항

### 1. API Key 관리
- Google Maps API Key는 **환경 변수**로 관리
- **Frontend 키** (NEXT_PUBLIC_GOOGLE_MAPS_API_KEY)
  - Maps JavaScript API만 허용
  - HTTP referrer 제한
- **Backend 키** (GOOGLE_MAPS_API_KEY)
  - Places API, Geocoding API 허용
  - IP 주소 제한 (Vercel IP 화이트리스트)

### 2. 데이터 보안
- 사용자 북마크는 `userId`로 필터링 (다른 사용자 접근 불가)
- Places API 응답은 공개 정보만 사용 (라이선스 규정 준수)

### 3. 레이트 제한
- Google Places API 호출 제한 (비용 최적화)
- Runtime Cache로 중복 쿼리 방지
- 사용자별 북마크 생성 제한 (예: 최대 1000개)

---

## 📈 확장 계획 (Phase 2+)

### Phase 2: 고급 기능
- [ ] 여행 일정 생성 (여러 장소를 순서대로 방문 계획)
- [ ] 루트 최적화 (가장 효율적인 이동 경로 제안)
- [ ] 공유 기능 (친구와 여행 계획 공유)
- [ ] 리뷰 작성 (방문한 장소에 대한 평가)

### Phase 3: AI 기능
- [ ] 추천 장소 (방문 패턴 기반 추천)
- [ ] 여행 가이드 생성 (AI로 여행 계획 자동 생성)
- [ ] 실시간 알림 (좋아하는 장소 이벤트 알림)

---

## 💡 주의사항

1. **Google Maps API 쿼터**
   - Maps JavaScript API: 월 일정량 무료, 초과 시 과금
   - Places API: 쿼리당 비용 발생
   - **모니터링**: Google Cloud Console에서 실시간 사용량 추적

2. **플레이스 ID 수집**
   - Google Maps 검색에서 얻은 `placeId` 저장 필수
   - Geocoding API 대신 Places API 사용 (정확도 높음)

3. **오프라인 모드**
   - 북마크는 IndexedDB에 캐싱 (앱 재방문 시 빠른 로딩)
   - 비즈니스 시간은 실시간 서버 조회 필수

---

## 📋 Acceptance Criteria (AC)

- [ ] AC 1: Google Maps JavaScript API 통합 완료
- [ ] AC 2: 북마크 CRUD API 구현 완료
- [ ] AC 3: Google Places API 실시간 영업시간 조회 구현
- [ ] AC 4: Frontend 지도 컴포넌트 구현 (마커, 정보창, 검색)
- [ ] AC 5: Backend 캐싱 (Runtime Cache) 적용
- [ ] AC 6: API Key 보안 설정 (환경변수, 제한 설정)

---

## 🔗 관련 문서

- [기술 스택 결정](./01-tech-stack-decision.md)
- [CONTRIBUTING.md](../../.github/CONTRIBUTING.md)

---

## 📝 다음 단계

1. TypeORM 데이터베이스 스키마 설계 및 마이그레이션
2. Nest.js 백엔드 API 엔드포인트 구현
3. Next.js 프론트엔드 지도 컴포넌트 구현
4. Google Maps API Key 발급 및 환경변수 설정

---

**작성자:** Claude Haiku 4.5
**작성일:** 2026-04-05
**검토 예정:** 2026-04-08
