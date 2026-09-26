# 프로젝트 개발 로드맵

Notion을 CMS로 활용하여 책 리뷰를 작성하면 별도 배포 없이 블로그에 자동 반영되는 개인 책 리뷰 블로그

## 개요

책 리뷰 블로그는 Notion에서 글을 작성하는 개인 독서 기록 관리자를 위한 정적 블로그로 다음 기능을 제공합니다:

- **Notion CMS 연동**: Notion 데이터베이스를 콘텐츠 소스로 활용하여 별도 배포 없이 리뷰 자동 반영
- **책 리뷰 탐색**: 장르별 필터링, 키워드 검색, 다양한 정렬 기준으로 원하는 리뷰 탐색
- **리치 텍스트 렌더링**: Notion 블록을 HTML로 변환하여 서식이 유지된 리뷰 본문 표시
- **ISR 캐싱**: Incremental Static Regeneration으로 빠른 로딩과 API 호출 최소화

## 기술 스택

- **Framework**: Next.js 15.5.3 (App Router + Turbopack)
- **Runtime**: React 19.1.0 + TypeScript 5
- **Styling**: TailwindCSS v4 + shadcn/ui (new-york style)
- **CMS**: @notionhq/client (Notion API)
- **Forms**: React Hook Form + Zod
- **배포**: Vercel (ISR 네이티브 지원)

## 개발 워크플로우

1. **작업 계획**

   - 기존 코드베이스를 학습하고 현재 상태를 파악
   - 새로운 작업을 포함하도록 `ROADMAP.md` 업데이트
   - 우선순위 작업은 마지막 완료된 작업 다음에 삽입

2. **작업 생성**

   - 기존 코드베이스를 학습하고 현재 상태를 파악
   - `/tasks` 디렉토리에 새 작업 파일 생성
   - 명명 형식: `XXX-description.md` (예: `001-setup.md`)
   - 고수준 명세서, 관련 파일, 수락 기준, 구현 단계 포함
   - API/비즈니스 로직 작업 시 "## 테스트 체크리스트" 섹션 필수 포함 (Playwright MCP 테스트 시나리오 작성)

3. **작업 구현**

   - 작업 파일의 명세서를 따름
   - 기능과 기능성 구현
   - API 연동 및 비즈니스 로직 구현 시 Playwright MCP로 테스트 수행 필수
   - 각 단계 후 작업 파일 내 단계 진행 상황 업데이트
   - 구현 완료 후 Playwright MCP를 사용한 E2E 테스트 실행
   - 각 단계 완료 후 중단하고 추가 지시를 기다림

4. **로드맵 업데이트**

   - 로드맵에서 완료된 작업을 체크 표시로 갱신

---

## 개발 단계

### Phase 1: 프로젝트 초기 설정 (골격 구축)

> **왜 먼저 하는가**: 전체 애플리케이션의 라우트 구조, 타입 정의, 환경 설정을 먼저 잡아야 이후 UI 개발과 기능 구현이 일관된 기반 위에서 진행된다. 불필요한 스타터 템플릿 코드를 정리하고 프로젝트 고유 구조를 확립하는 단계이다.
>
> **예상 소요 시간**: 2일
>
> **완료 기준**: 모든 페이지 라우트가 빈 껍데기로 존재하고, TypeScript 타입이 정의되어 있으며, 환경 변수 설정이 완료되어 `npm run build`가 성공하는 상태

- [ ] **TASK-001: 불필요한 스타터 코드 정리 및 라우트 구조 설정**
  - 스타터 템플릿의 불필요한 페이지 제거 (login, signup 등)
  - 스타터 템플릿의 불필요한 컴포넌트 제거 (sections, navigation, login-form, signup-form 등)
  - 홈 페이지 라우트 재구성 (`src/app/page.tsx` - 빈 껍데기)
  - 상세 페이지 동적 라우트 생성 (`src/app/[id]/page.tsx` - 빈 껍데기)
  - 공통 레이아웃 골격 구현 (`src/app/layout.tsx` - 헤더/푸터 기본 구조)

- [ ] **TASK-002: TypeScript 타입 정의 및 환경 변수 설정**
  - `src/types/book.ts` - BookReview 인터페이스 정의 (id, title, genre, author, published, star, readDate, review)
  - `src/types/notion.ts` - NotionBlock 인터페이스 정의 (id, type, content, children)
  - `src/types/index.ts` - 타입 통합 export 파일 생성
  - 정렬/필터 관련 타입 정의 (SortOption, SortDirection, GenreFilterOption)
  - `.env.local` 환경 변수 구조 설정 (NOTION_TOKEN, NOTION_DATABASE_ID)
  - `src/lib/env.ts` - 환경 변수 검증 유틸리티 업데이트 (Zod 스키마 적용)

---

### Phase 2: 공통 모듈 및 컴포넌트 개발 (더미 데이터 활용)

> **왜 먼저 하는가**: 공통 컴포넌트와 더미 데이터를 먼저 구현하면 실제 Notion API 없이도 전체 UI를 검증할 수 있다. 컴포넌트를 독립적으로 개발하여 재사용성을 확보하고, 이후 API 연동 시 더미 데이터만 교체하면 되므로 변경 범위를 최소화한다.
>
> **예상 소요 시간**: 4일
>
> **완료 기준**: 모든 UI 컴포넌트가 더미 데이터로 정상 렌더링되고, 홈/상세 페이지의 전체 사용자 플로우가 더미 데이터 기반으로 동작하며, 반응형 레이아웃이 모바일/태블릿/데스크톱에서 올바르게 표시되는 상태

- [ ] **TASK-003: 더미 데이터 및 공통 유틸리티 작성**
  - `src/lib/dummy-data.ts` - BookReview 더미 데이터 10건 이상 생성 (다양한 장르, 평점 분포)
  - `src/lib/dummy-data.ts` - NotionBlock 더미 데이터 생성 (paragraph, heading, list 등 다양한 블록 타입)
  - 데이터 정렬 유틸리티 함수 작성 (읽은 날짜/평점/출판일 기준)
  - 데이터 필터링 유틸리티 함수 작성 (장르 필터, 키워드 검색)

- [ ] **TASK-004: 공통 UI 컴포넌트 구현**
  - `src/components/BookCard.tsx` - 책 카드 컴포넌트 (제목/저자/장르/평점/읽은 날짜 표시, 플레이스홀더 이미지)
  - `src/components/StarRating.tsx` - 별점 표시 컴포넌트 (Lucide Star 아이콘, 1~5점)
  - `src/components/SearchBar.tsx` - 키워드 검색 입력 컴포넌트 (React Hook Form 연동)
  - `src/components/GenreFilter.tsx` - 장르 필터 탭 컴포넌트 (전체 + 동적 장르 목록)
  - `src/components/SortSelect.tsx` - 정렬 셀렉트박스 컴포넌트 (shadcn/ui Select 활용)
  - `src/components/layout/header.tsx` - 헤더 컴포넌트 업데이트 (로고/사이트명 + 검색창)
  - `src/components/layout/footer.tsx` - 푸터 컴포넌트 업데이트 (간결한 저작권 표시)

- [ ] **TASK-005: 홈 페이지 UI 완성 (더미 데이터 기반)**
  - 홈 페이지 레이아웃 구현 (검색 + 장르 필터 + 정렬 + 카드 그리드)
  - 반응형 그리드 적용 (모바일 1열 / 태블릿 2열 / 데스크톱 3열)
  - 클라이언트 사이드 검색/필터/정렬 로직 구현 (useState + useMemo)
  - 빈 상태(검색 결과 없음) UI 처리
  - 카드 클릭 시 상세 페이지 이동 네비게이션 연결

- [ ] **TASK-006: 상세 페이지 UI 완성 (더미 데이터 기반)**
  - `src/components/NotionRenderer.tsx` - Notion 블록 HTML 렌더링 컴포넌트 (paragraph, heading_1~3, bulleted_list, numbered_list, quote, code, image 블록 지원)
  - 상세 페이지 레이아웃 구현 (책 메타 정보 영역 + 리뷰 본문 영역)
  - 책 메타 정보 표시 (제목/저자/장르/출판일/읽은 날짜/별점)
  - 홈으로 돌아가기 버튼 구현
  - 반응형 단일 컬럼 레이아웃 적용

---

### Phase 3: 핵심 기능 개발 (Notion API 연동)

> **왜 먼저 하는가**: Phase 2에서 UI가 완성되었으므로 이제 실제 데이터 소스를 연결하는 단계이다. Notion API 클라이언트를 구현하고 더미 데이터를 실제 API 호출로 교체하면 최소한의 코드 변경으로 전체 기능이 동작한다.
>
> **예상 소요 시간**: 4일
>
> **완료 기준**: Notion 데이터베이스에서 실제 데이터를 조회하여 홈 페이지 목록과 상세 페이지 본문이 정상 표시되고, ISR 캐싱이 적용되어 있으며, 모든 사용자 플로우 E2E 테스트가 통과하는 상태

- [ ] **TASK-007: Notion API 클라이언트 구현** - 우선순위
  - `@notionhq/client` 패키지 설치 및 설정
  - `src/lib/notion.ts` - Notion 클라이언트 초기화 (환경 변수 기반)
  - `getBookReviews()` 함수 구현 - databases.query로 전체 목록 조회
  - `getBookReview(id)` 함수 구현 - pages.retrieve로 단일 페이지 조회
  - `getBookReviewBlocks(id)` 함수 구현 - blocks.children.list로 본문 블록 조회
  - Notion API 응답을 BookReview 타입으로 변환하는 파싱 함수 작성
  - Zod 스키마로 API 응답 데이터 타입 검증
  - Playwright MCP를 활용한 API 호출 결과 검증 테스트

- [ ] **TASK-008: 홈 페이지 Notion API 연동 및 ISR 적용**
  - 홈 페이지를 Server Component로 전환하여 Notion API 호출
  - 더미 데이터를 `getBookReviews()` 실제 API 호출로 교체
  - ISR 설정 적용 (revalidate = 3600)
  - 장르 목록을 Notion 데이터에서 동적 추출
  - 로딩 상태 및 에러 핸들링 UI 구현 (loading.tsx, error.tsx)
  - Playwright MCP로 목록 조회, 필터링, 검색, 정렬 E2E 테스트 수행

- [ ] **TASK-009: 상세 페이지 Notion API 연동 및 ISR 적용**
  - 상세 페이지에서 `getBookReview(id)` + `getBookReviewBlocks(id)` 호출
  - 더미 데이터를 실제 API 호출로 교체
  - ISR 설정 적용 (revalidate = 3600)
  - generateStaticParams로 빌드 시 정적 경로 생성
  - NotionRenderer에서 실제 Notion 블록 데이터 렌더링 검증
  - 존재하지 않는 페이지 접근 시 404 처리
  - Playwright MCP로 상세 페이지 렌더링 및 네비게이션 E2E 테스트 수행

- [ ] **TASK-010: 핵심 기능 통합 테스트**
  - Playwright MCP를 사용한 전체 사용자 플로우 테스트 (홈 -> 검색/필터 -> 카드 클릭 -> 상세 -> 홈 복귀)
  - API 연동 및 비즈니스 로직 검증 (데이터 정합성, 정렬 정확도)
  - 에러 핸들링 테스트 (잘못된 ID 접근, API 응답 실패 시나리오)
  - 반응형 레이아웃 테스트 (모바일/태블릿/데스크톱 뷰포트)

---

### Phase 4: 추가 기능 개발

> **왜 먼저 하는가**: 핵심 기능이 완성된 후 사용자 경험을 향상시키는 부가 기능을 추가한다. 이 단계의 작업들은 핵심 동작에 영향을 주지 않으면서 완성도를 높이는 역할을 한다.
>
> **예상 소요 시간**: 2일
>
> **완료 기준**: 스켈레톤 로딩, 에러 바운더리, SEO 메타데이터가 적용되어 사용자 경험이 완성된 상태

- [ ] **TASK-011: 로딩 상태 및 에러 처리 고도화**
  - 홈 페이지 스켈레톤 UI 구현 (카드 그리드 로딩 플레이스홀더)
  - 상세 페이지 스켈레톤 UI 구현 (메타 정보 + 본문 로딩 플레이스홀더)
  - 글로벌 에러 바운더리 구현 (error.tsx, global-error.tsx)
  - not-found.tsx 페이지 구현 (404 커스텀 페이지)
  - API 호출 실패 시 재시도 또는 폴백 UI 표시

- [ ] **TASK-012: SEO 및 메타데이터 최적화**
  - 홈 페이지 메타데이터 설정 (title, description, Open Graph)
  - 상세 페이지 동적 메타데이터 생성 (generateMetadata - 책 제목/저자 기반)
  - sitemap.xml 자동 생성 (Notion 데이터 기반)
  - robots.txt 설정

---

### Phase 5: 최적화 및 배포

> **왜 마지막에 하는가**: 모든 기능이 완성된 후 성능 최적화와 배포 설정을 진행한다. 완성된 앱의 실제 성능을 측정하고 병목을 해소하는 것이 효율적이다.
>
> **예상 소요 시간**: 2일
>
> **완료 기준**: Vercel에 배포 완료, Lighthouse 성능 점수 90점 이상, 실제 Notion 데이터베이스와 연동되어 정상 동작하는 상태

- [ ] **TASK-013: 성능 최적화**
  - Next.js Image 컴포넌트 적용 (표지 이미지가 있는 경우)
  - 번들 사이즈 분석 및 불필요한 의존성 제거
  - 컴포넌트 lazy loading 적용 (NotionRenderer 등)
  - Lighthouse 성능 점수 측정 및 개선

- [ ] **TASK-014: Vercel 배포 및 환경 설정**
  - Vercel 프로젝트 생성 및 Git 연동
  - 환경 변수 설정 (NOTION_TOKEN, NOTION_DATABASE_ID)
  - 프로덕션 빌드 검증 (`npm run build` 성공 확인)
  - 배포 후 실제 Notion 데이터베이스 연동 동작 확인
  - ISR revalidation 정상 동작 검증
  - Playwright MCP로 프로덕션 환경 E2E 테스트 수행
