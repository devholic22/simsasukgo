# 2. 북마크 (Bookmark)

## 개요

simsasukgo의 핵심 도메인. 사용자가 여행 중 관심 장소를 저장하고 상태를 관리한다.

레거시에서는 모든 데이터를 `localStorage`에 저장하여 기기를 바꾸면 데이터가 사라지는 문제가 있었다.
이번 버전에서는 PostgreSQL 기반으로 재설계하여 데이터 영속성을 확보한다.

**결정일:** 2026-04-05

---

## 레거시와의 차이

| 항목 | 레거시 | 이번 버전 |
|------|--------|----------|
| 저장소 | localStorage | PostgreSQL |
| 데이터 영속성 | 기기 종속 | 서버 저장 |
| 사용자 식별 | 없음 | userId (개발 단계: localStorage UUID) |
| Google Places 호출 | 프론트에서 직접 | 백엔드에서 처리 (API 키 보안) |
| 자동 백업 | 1시간마다 localStorage 백업 | 불필요 (DB 저장) |

---

## 인증에 대한 의사결정

실제 인증 시스템은 핵심 기능 구현 이후로 미룬다.

**개발 단계:**
- 앱 최초 실행 시 `localStorage`에 UUID를 생성하여 임시 `userId`로 사용
- DB 스키마에는 처음부터 `userId` 컬럼을 포함하여 추후 인증 연동 시 마이그레이션 최소화

**인증 연동 시:**
- localStorage UUID를 실제 계정 ID로 교체하는 마이그레이션 필요

---

## 도메인 설계

### 장소 타입

레거시와 동일하게 3가지 타입을 유지한다.

| 타입 | 설명 |
|------|------|
| `restaurant` | 맛집 |
| `place` | 관광지, 카페 등 일반 장소 |
| `accommodation` | 숙소 |

### 엔티티 필드

레거시의 `MarkerInfo`를 기반으로 DB 컬럼으로 재설계한다.

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| `id` | uuid | O | 고유 식별자 |
| `userId` | varchar | O | 사용자 식별자 (개발 단계: localStorage UUID) |
| `name` | varchar | O | 장소명 |
| `type` | enum | O | restaurant / place / accommodation |
| `latitude` | decimal(10,8) | O | 위도 |
| `longitude` | decimal(11,8) | O | 경도 |
| `address` | varchar | X | 주소 |
| `phone` | varchar | X | 전화번호 |
| `rating` | float | X | 평점 |
| `memo` | text | X | 사용자 메모 (레거시의 menu + description 통합) |
| `visited` | boolean | O | 방문 여부 (기본값: false) |
| `used` | boolean | O | 사용 여부 - 숙소 전용 (기본값: false) |
| `isOpenNow` | boolean | X | 현재 영업 여부 - Places API 동기화 시 갱신 |
| `placeId` | varchar | X | Google Places ID - 영업시간 조회에 사용 |
| `createdAt` | timestamp | O | 생성일시 |
| `updatedAt` | timestamp | O | 수정일시 |

> `memo`는 레거시의 `menu`(메뉴 메모)와 `description`(설명)을 단일 필드로 통합한다.

---

## 마커 필터링 규칙

지도에 표시할 마커를 타입별로 다르게 필터링한다. 레거시에서 검증된 규칙을 그대로 가져온다.

| 타입 | 지도 표시 조건 |
|------|--------------|
| `restaurant` | `isOpenNow = true` AND `visited = false` |
| `place` | `visited = false` |
| `accommodation` | `used = false` |

이미 방문하거나 사용한 장소, 또는 영업 중이 아닌 맛집은 지도에서 제거하여 현재 갈 수 있는 장소만 표시한다.

---

## API 엔드포인트

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `POST` | `/api/bookmarks` | 북마크 생성 |
| `GET` | `/api/bookmarks` | 북마크 목록 조회 |
| `GET` | `/api/bookmarks/:id` | 북마크 단일 조회 |
| `PATCH` | `/api/bookmarks/:id` | 북마크 수정 (방문 여부, 메모 등) |
| `DELETE` | `/api/bookmarks/:id` | 북마크 삭제 |

모든 엔드포인트는 요청 헤더 또는 쿼리에 `userId`를 포함하여 본인 데이터만 접근 가능하도록 한다.

---

## Acceptance Criteria

- [ ] AC 1: 사용자는 장소명, 타입, 좌표를 입력하여 북마크를 생성할 수 있다
- [ ] AC 2: 사용자는 본인의 북마크 목록을 조회할 수 있다
- [ ] AC 3: 사용자는 방문 여부, 사용 여부, 메모를 수정할 수 있다
- [ ] AC 4: 사용자는 북마크를 삭제할 수 있다
- [ ] AC 5: 다른 사용자의 북마크는 조회 및 수정할 수 없다
- [ ] AC 6: 필수 필드(name, type, latitude, longitude)가 누락되면 생성에 실패한다
