---
topic: Testing & Quality
last_compiled: 2026-04-16
source_count: 2
status: active
---

# Testing & Quality

## Summary [coverage: high -- 2 sources]

테스트 인프라는 거의 없다. `package.json`에 테스트 스크립트가 없고 단위/E2E 테스트 파일도 없다. 품질 보증은 **Playwright MCP 기반 시각 회귀 테스트**에 집중한다. 운영 서버(`http://design-system-react.gsitm-frontend.site`) vs 로컬(`localhost:5173`) 22개 라우트 스크린샷 비교가 핵심 검증 수단이다.

## Current State [coverage: high -- 2 sources]

- 단위 테스트: 없음
- E2E 테스트: 없음 (Playwright 설정은 `.playwright-mcp` 존재)
- 린트: `npx eslint src/`로 실행 (package.json 스크립트 없음)
- 빌드 검증: `pnpm tsc --noEmit` + `pnpm build`

## Visual Regression Testing [coverage: high -- 1 source]

**대상 라우트 22개** (`/components/*`):
button, input, grid, gridHeader, gridFilter, dropdownCalendar, selectionLoader, tooltipPagination, popup, tabsTree, layout, detailForms, fileUploader, searchForms, background, colors, colorBackground, display, flex, spacing

**판정 기준**:
- High mismatch(레이아웃 깨짐, 색상 완전 다름) = **0건** 필수
- Medium mismatch = 게이트별 허용치 (Iter2: ≤3건, 최종: ≤5건)

**실행 방식**: Playwright MCP로 각 라우트를 양쪽 서버에서 열고 스크린샷 비교

## Code Convention [coverage: high -- 1 source]

**네이밍**:
- 컴포넌트 파일: `PascalCase.tsx`
- 유틸 파일: `camelCase.ts`
- 스타일 파일: `kebab-case.css`

**코드 규칙**:
- 들여쓰기: 스페이스 2칸 (탭 금지)
- 경로 alias `@` → `src/`
- `cn()` 유틸로 className 병합 필수
- 동적 Tailwind 클래스 조합 금지 (정적 분석 안전)

**TypeScript**:
- `pnpm tsc --noEmit` 오류 0 필수
- `strict` 모드 적용
- `...rest`로 네이티브 HTML 속성 전달

## Gotchas & Known Issues [coverage: medium -- 2 sources]

- `.playwright-mcp` 설정 파일은 있으나 실제 테스트 스크립트 없음 — 수동 MCP 실행 방식
- 접근성(aria/keyboard/focus) 체크는 기준 정의됐으나 자동화 없음

## Sources
- [[../../design-system-react/docs/06-test-quality-strategy]]
- [[../../design-system-react/docs/07-code-convention]]
