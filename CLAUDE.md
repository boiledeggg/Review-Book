# 🤖 Claude Code 개발 지침

**review-book**은 Notion을 CMS로 활용하는 개인 책 리뷰 블로그입니다. Notion에서 글을 작성하면 별도 배포 없이 블로그에 자동 반영됩니다.

## 🛠️ 핵심 기술 스택

- **Framework**: Next.js 15.5.3 (App Router + Turbopack)
- **Runtime**: React 19.1.0 + TypeScript 5
- **Styling**: TailwindCSS v4 + shadcn/ui (new-york style)
- **CMS**: @notionhq/client (Notion API) + ISR 캐싱 (revalidate=3600)
- **Forms**: React Hook Form + Zod
- **UI Components**: Radix UI + Lucide Icons
- **Development**: ESLint + Prettier + Husky + lint-staged

## 🔑 환경 변수 (.env.local)

```
NOTION_TOKEN=secret_xxxx          # Notion Integration 토큰
NOTION_DATABASE_ID=xxxx           # 책 리뷰 데이터베이스 ID
```

## 🏗️ 아키텍처 개요

- **라우트**: `/` (홈 - 목록/검색/필터), `/[id]` (상세 - 책 리뷰 본문)
- **데이터 흐름**: Notion DB → `src/lib/notion.ts` → Server Component → Client Component (필터/검색은 클라이언트 사이드 `useState + useMemo`)
- **Notion 데이터 모델**: `BookReview` (id, title, genre, author, published, star, readDate, review) / `NotionBlock` (상세 페이지 본문)
- **캐싱**: ISR `revalidate=3600` (홈/상세 모두)
- **인증 없음**: 인증이 필요하지 않은 공개 블로그

## 📚 개발 가이드

- **🗺️ 개발 로드맵**: `@/docs/ROADMAP.md`
- **📋 프로젝트 요구사항**: `@/docs/PRD.md`
- **📁 프로젝트 구조**: `@/docs/guides/project-structure.md`
- **🎨 스타일링 가이드**: `@/docs/guides/styling-guide.md`
- **🧩 컴포넌트 패턴**: `@/docs/guides/component-patterns.md`
- **⚡ Next.js 15.5.3 전문 가이드**: `@/docs/guides/nextjs-15.md`
- **📝 폼 처리 완전 가이드**: `@/docs/guides/forms-react-hook-form.md`

## ⚡ 자주 사용하는 명령어

```bash
# 개발
npm run dev         # 개발 서버 실행 (Turbopack)
npm run build       # 프로덕션 빌드
npm run check-all   # 모든 검사 통합 실행 (권장)

# UI 컴포넌트
npx shadcn@latest add button    # 새 컴포넌트 추가
```

## ✅ 작업 완료 체크리스트

```bash
npm run check-all   # 모든 검사 통과 확인
npm run build       # 빌드 성공 확인
```

💡 **상세 규칙은 위 개발 가이드 문서들을 참조하세요**
