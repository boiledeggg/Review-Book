# 책 리뷰 블로그 MVP PRD

## 핵심 정보

**목적**: Notion을 CMS로 활용하여 책 리뷰를 작성하면 별도 배포 없이 블로그에 자동 반영되는 개인 책 블로그
**사용자**: Notion에서 글을 작성하고 싶은 개인 독서 기록 관리자 (1인 운영자 본인)

---

## 사용자 여정

```
1. 홈 페이지 (최초 진입)
   ↓ 자동 로드

2. 책 리뷰 목록 확인
   ↓ 사용자 선택

   [검색어 입력] → 검색 결과 필터링 → 목록 갱신 (같은 페이지 내)
   [장르 필터 선택] → 해당 장르 목록 표시 → 목록 갱신 (같은 페이지 내)
   [정렬 옵션 선택] → 정렬 기준 변경 → 목록 갱신 (같은 페이지 내)
   [카드 클릭] → 상세 페이지로 이동
   ↓

3. 책 리뷰 상세 페이지
   ↓ 확인 후

4. 홈으로 돌아가기 버튼 클릭 → 홈 페이지
```

---

## 기능 명세

### 1. MVP 핵심 기능

| ID | 기능명 | 설명 | MVP 필수 이유 | 관련 페이지 |
|----|--------|------|--------------|------------|
| **F001** | Notion 리뷰 목록 조회 | Notion API로 데이터베이스의 책 리뷰 전체 목록을 가져와 카드 형태로 표시 | 서비스의 핵심 콘텐츠 공급원 | 홈 페이지 |
| **F002** | 책 리뷰 상세 조회 | 선택한 책의 제목, 저자, 장르, 평점, 읽은 날짜, 리뷰 본문을 표시 | 개별 리뷰 콘텐츠 소비의 핵심 목적 | 상세 페이지 |
| **F003** | 장르별 필터링 | Notion 데이터베이스의 Genre 값을 기준으로 목록을 필터링 | 카테고리 탐색으로 원하는 책 빠르게 찾기 | 홈 페이지 |
| **F004** | 키워드 검색 | 책 제목과 저자명 기준으로 클라이언트 사이드 검색 | 특정 책을 빠르게 찾는 기본 탐색 기능 | 홈 페이지 |
| **F005** | 정렬 기능 | 읽은 날짜, 평점, 출판일 기준 오름차순/내림차순 정렬 | 원하는 순서로 목록 탐색 | 홈 페이지 |

### 2. MVP 필수 지원 기능

| ID | 기능명 | 설명 | MVP 필수 이유 | 관련 페이지 |
|----|--------|------|--------------|------------|
| **F010** | Notion API 연동 | `@notionhq/client` 패키지로 데이터베이스 쿼리 및 페이지 콘텐츠 조회 | 모든 콘텐츠의 데이터 소스 | 홈 페이지, 상세 페이지 |
| **F011** | 반응형 레이아웃 | 모바일/태블릿/데스크톱 화면 크기에 맞는 레이아웃 자동 조정 | 다양한 기기에서 콘텐츠 소비 가능 | 홈 페이지, 상세 페이지 |
| **F012** | ISR 캐싱 | Next.js ISR(Incremental Static Regeneration)로 Notion API 호출 최소화 및 빠른 로딩 | API 호출 제한 대응 및 성능 확보 | 홈 페이지, 상세 페이지 |

### 3. MVP 이후 기능 (제외)

- 사용자 인증 및 댓글 기능
- 좋아요/북마크 기능
- RSS 피드
- 태그 기반 다중 필터
- 다크 모드 토글
- 독서 통계 대시보드

---

## 메뉴 구조

```
책 리뷰 블로그 내비게이션

헤더 (전체 공통)
├── 로고/사이트명 → 홈 페이지 이동
└── 검색 입력창 - F004 (키워드 검색)

홈 페이지 내 컨트롤
├── 장르 필터 탭/버튼 - F003 (장르별 필터링)
├── 정렬 셀렉트박스 - F005 (정렬 기능)
└── 책 리뷰 카드 그리드 - F001 (목록 조회)

상세 페이지 내 컨트롤
└── 홈으로 돌아가기 버튼 → 홈 페이지 이동
```

---

## 페이지별 상세 기능

### 홈 페이지

> **구현 기능:** `F001`, `F003`, `F004`, `F005`, `F010`, `F011`, `F012` | **인증:** 불필요

| 항목 | 내용 |
|------|------|
| **역할** | 전체 책 리뷰 목록을 탐색하는 메인 랜딩 페이지 |
| **진입 경로** | 최초 접속, 로고 클릭, 상세 페이지에서 뒤로가기/홈 버튼 클릭 |
| **사용자 행동** | 검색창에 키워드 입력, 장르 필터 선택, 정렬 방식 변경, 카드 클릭으로 상세 이동 |
| **주요 기능** | • Notion API에서 전체 리뷰 목록 로드 (F001, F010)<br>• 책 제목 + 저자명 기준 클라이언트 사이드 키워드 검색 (F004)<br>• Genre 필드 기준 필터 탭 (전체 / 소설 / 비소설 / 자기계발 등 동적 생성) (F003)<br>• 읽은 날짜 / 평점 / 출판일 기준 정렬 셀렉트박스 (F005)<br>• 책 카드: 표지 이미지 없는 경우 기본 플레이스홀더, 제목 / 저자 / 장르 / 평점 / 읽은 날짜 표시<br>• ISR revalidate 설정으로 Notion 변경 사항 주기적 반영 (F012)<br>• 모바일 1열 / 태블릿 2열 / 데스크톱 3열 반응형 그리드 (F011) |
| **다음 이동** | 카드 클릭 → 상세 페이지 |

---

### 상세 페이지

> **구현 기능:** `F002`, `F010`, `F011`, `F012` | **인증:** 불필요

| 항목 | 내용 |
|------|------|
| **역할** | 특정 책 리뷰의 전체 내용을 표시하는 개별 콘텐츠 페이지 |
| **진입 경로** | 홈 페이지의 책 카드 클릭 |
| **사용자 행동** | 책 상세 정보 및 리뷰 본문 읽기, 홈으로 돌아가기 버튼 클릭 |
| **주요 기능** | • Notion 페이지 ID 기반 개별 리뷰 데이터 조회 (F002, F010)<br>• 책 메타 정보 표시: 제목 / 저자 / 장르 / 출판일 / 읽은 날짜 / 평점(별점 UI)<br>• Notion 리치 텍스트 블록을 HTML로 변환하여 리뷰 본문 렌더링 (F002)<br>• 홈으로 돌아가기 버튼 (브라우저 history 기반 또는 홈 링크)<br>• ISR revalidate 설정으로 Notion 수정 사항 반영 (F012)<br>• 모바일/데스크톱 반응형 단일 컬럼 레이아웃 (F011) |
| **다음 이동** | 홈으로 돌아가기 클릭 → 홈 페이지 |

---

## 데이터 모델

### BookReview (Notion 데이터베이스 매핑)

| 필드 | 설명 | 타입/관계 |
|------|------|----------|
| id | Notion 페이지 고유 ID | string (UUID) |
| title | 책 제목 (Notion Title 프로퍼티) | string |
| genre | 책 장르 (Notion Genre 프로퍼티) | string |
| author | 저자명 (Notion Author 프로퍼티) | string |
| published | 출판일 (Notion Published 프로퍼티) | string (YYYY-MM-DD) |
| star | 평점 1~5 (Notion Star 프로퍼티) | number |
| readDate | 읽은 날짜 (Notion Read Date 프로퍼티) | string (YYYY-MM-DD) |
| review | 리뷰 본문 요약 (Notion Review 프로퍼티) | string |

### NotionBlock (상세 페이지 본문 렌더링용)

| 필드 | 설명 | 타입/관계 |
|------|------|----------|
| id | 블록 고유 ID | string |
| type | 블록 타입 (paragraph, heading_1 등) | string |
| content | 블록 텍스트 콘텐츠 | string |
| children | 중첩 블록 목록 | NotionBlock[] |

---

## Notion API 연동 설계

### 환경 변수 (.env.local)

```
NOTION_TOKEN=secret_xxxx          # Notion Integration 토큰
NOTION_DATABASE_ID=xxxx           # 책 리뷰 데이터베이스 ID
```

### 핵심 API 호출 패턴

```
목록 조회: databases.query({ database_id })
  → filter: Genre 조건 (선택 시)
  → sorts: 정렬 기준 필드

상세 조회: pages.retrieve({ page_id })
  → 메타 정보 추출

본문 조회: blocks.children.list({ block_id: page_id })
  → 리치 텍스트 블록 파싱
```

### ISR 캐싱 전략

```
홈 페이지: revalidate = 3600 (1시간)
상세 페이지: revalidate = 3600 (1시간)
```

---

## 기술 스택

### 프론트엔드 프레임워크

- **Next.js 15.5.3** (App Router + Turbopack) - React 풀스택 프레임워크, ISR 지원
- **TypeScript 5** - 타입 안전성 보장
- **React 19.1.0** - UI 라이브러리

### 스타일링 & UI

- **TailwindCSS v4** (설정 파일 없는 새로운 엔진) - 유틸리티 CSS 프레임워크
- **shadcn/ui** (new-york style) - 고품질 React 컴포넌트 라이브러리
- **Lucide React** - 아이콘 라이브러리 (별점 등)
- **Radix UI** - 접근성 기반 헤드리스 컴포넌트

### 폼 & 검증

- **React Hook Form 7.x** - 검색 폼 상태 관리
- **Zod** - 스키마 검증 (Notion API 응답 타입 검증)

### Notion CMS 연동

- **@notionhq/client** - 공식 Notion JavaScript SDK
- **notion-to-md** (선택) - Notion 블록을 Markdown으로 변환

### 백엔드 & 데이터

- **Next.js Server Actions / Route Handlers** - Notion API 서버 사이드 호출
- **Next.js ISR** - Incremental Static Regeneration으로 캐싱

### 개발 도구

- **ESLint** - 코드 품질 검사
- **Prettier** - 코드 포맷팅
- **Husky + lint-staged** - 커밋 전 자동 검사

### 배포 & 호스팅

- **Vercel** - Next.js 15 최적화 배포 플랫폼 (ISR 네이티브 지원)

---

## 구현 순서

### 1단계: Notion API 환경 설정

1. `npm install @notionhq/client` 패키지 설치
2. Notion Integration 생성 후 토큰 발급
3. 책 리뷰 Notion 데이터베이스 생성 (Title / Genre / Author / Published / Review / Star / Read Date)
4. 데이터베이스에 Integration 연결 (공유 설정)
5. `.env.local`에 `NOTION_TOKEN`, `NOTION_DATABASE_ID` 설정

### 2단계: Notion 데이터 레이어 구현

1. `src/lib/notion.ts` - Notion 클라이언트 초기화 및 API 함수 작성
   - `getBookReviews()` - 전체 목록 조회
   - `getBookReview(id)` - 단일 페이지 조회
   - `getBookReviewBlocks(id)` - 본문 블록 조회
2. Notion API 응답을 `BookReview` 타입으로 변환하는 파싱 함수 작성
3. Zod 스키마로 응답 데이터 타입 검증

### 3단계: 홈 페이지 구현

1. `src/app/page.tsx` - ISR 설정 + 목록 데이터 로드
2. `src/components/BookCard.tsx` - 책 카드 컴포넌트 (제목 / 저자 / 장르 / 평점 / 읽은 날짜)
3. `src/components/SearchBar.tsx` - 키워드 검색 입력 컴포넌트
4. `src/components/GenreFilter.tsx` - 장르 필터 탭 컴포넌트
5. `src/components/SortSelect.tsx` - 정렬 셀렉트박스 컴포넌트
6. 클라이언트 사이드 필터링/검색/정렬 로직 구현 (useState + useMemo)

### 4단계: 상세 페이지 구현

1. `src/app/[id]/page.tsx` - 동적 라우트 + ISR 설정
2. `src/components/StarRating.tsx` - 별점 표시 컴포넌트
3. `src/components/NotionRenderer.tsx` - Notion 블록 HTML 렌더링 컴포넌트
4. 홈으로 돌아가기 버튼 구현

### 5단계: 스타일링 및 최적화

1. TailwindCSS v4 + shadcn/ui로 전체 디자인 정비
2. 모바일/태블릿/데스크톱 반응형 레이아웃 검증
3. Next.js `<Image>` 컴포넌트로 이미지 최적화 (필요 시)
4. Vercel 배포 및 환경 변수 설정
