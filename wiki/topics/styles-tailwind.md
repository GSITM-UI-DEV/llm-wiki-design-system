---
topic: Styles & Tailwind
last_compiled: 2026-04-15
source_count: 7
status: active
---

# Styles & Tailwind

## Summary [coverage: high -- 7 sources]

SCSS(42개 파일, 150KB+), styled-components(11개 파일), Tailwind CSS v4가 현재 공존 중이며 Tailwind v4로 단일화하는 것이 목표다. `src/styles/tailwind.css`가 진입점이며 `@theme`에 전체 디자인 토큰이 정의되어 있다.

`cn()` 유틸(`src/lib/cn.ts`, clsx + tailwind-merge)이 className 병합의 단일 표준이다. 신규 코드와 전환 컴포넌트는 Tailwind 유틸리티 클래스 + `cn()`을 우선 사용한다.

## Current State [coverage: high -- 7 sources]

**tailwind.css 실제 구조** (`src/styles/tailwind.css`):
```css
@import 'tailwindcss';           /* Preflight + 유틸리티 전체 */
@source "../";                   /* src/ 하위 정적 클래스 스캔 */

/* Pretendard 폰트 (400/500/600/700) */
@font-face { font-family: 'Pretendard'; ... }

@theme { /* 전체 디자인 토큰 */ }

@layer base { /* h1~h5, html, svg 기본 스타일 */ }

@keyframes { loader-spin, fade-in-up, fade-in-down, ... }

@theme { /* 애니메이션 토큰 */ }

@layer components { /* Tiptap 에디터 스타일 */ }

/* 레이어 파일 import */
@import './layers/layout.css';
@import './layers/lib.css';
@import './layers/tui-overrides.css';
@import './layers/date-picker.css';
@import 'tw-animate-css';
@import 'shadcn/tailwind.css';
```

**레이어 파일 목적**:
- `layout.css` — 앱 레이아웃(GNB, LNB, MDI, Content 영역) CSS
- `lib.css` — 외부 라이브러리(OverlayScrollbars 등) 오버라이드
- `tui-overrides.css` — Toast UI 컴포넌트 CSS 오버라이드
- `date-picker.css` — react-day-picker 오버라이드

## Token System [coverage: high -- 1 source]

**색상 토큰** (`@theme`):
```css
--color-gray-20: #f8f8f8  /* 가장 밝은 회색 */
--color-gray-280: #333     /* 가장 어두운 회색 */
--color-point-01: #d40000  /* error */
--color-point-02: #f9b037  /* warning */
--color-primary: #151515   /* 주요 강조색 */
--color-secondary: #476c97
```

**타이포 토큰**:
```css
--font-sans: 'Pretendard', -apple-system, ...
--font-size-xs: 12px  --font-size-sm: 13px
--font-size-base: 14px  --font-size-lg: 16px
```

**z-index 토큰**:
```css
--z-index-dropdown: 1000
--z-index-modal-backdrop: 1040
--z-index-modal: 1050
--z-index-tooltip: 1100
--z-index-loader: 9999
```

**브레이크포인트**: px 단위 고정 (`--breakpoint-sm: 640px` ~ `--breakpoint-2xl: 1536px`)

**애니메이션 토큰**:
```css
--animate-loader-spin: loader-spin 0.6s linear infinite  /* 기본 animate-spin(1s) 대신 */
--animate-fade-in-up: fade-in-up 220ms ease-out forwards
```

## Key Decisions [coverage: high -- 3 sources]

- **`cn()` SSOT**: `src/lib/cn.ts` (clsx + tailwind-merge). 조건부 className 병합은 반드시 이 함수 사용.
- **`@layer` 순서**: base → components → utilities. 순서 변경 금지.
- **동적 클래스 조합 금지**: `text-${color}-500` 같은 템플릿 리터럴 Tailwind 클래스는 빌드 시 누락. full class name 객체 매핑 사용.
- **`@apply` 금지 (TUI 오버라이드 내)**: `.tui-*` 직접 선택자만 사용.
- **스코프 클래스**: 전환 컴포넌트는 `.ds-migrated`, `.tui-scope` 등으로 CSS specificity 확보 가능.

## TUI Overrides [coverage: medium -- 2 sources]

`src/styles/layers/tui-overrides.css`에서 Toast UI 라이브러리의 기본 스타일을 오버라이드한다.

**원칙**:
- `@apply` 사용 금지
- `.tui-grid-*`, `.tui-tree-wrap`, `.tui-date-picker` 등 최상위 안정 selector만 사용
- `@layer components` 블록 내에서 정의

**알려진 누락 (이슈)**:
- `.tui-ico-line`, `.tui-ico-file` background-image 미정의 → TUI Tree 아이콘 깨짐

## Tailwind Variants Pattern [coverage: medium -- 2 sources]

`tailwind-variants`(tv) 라이브러리 + `cn()` 조합이 공식 표준이다.

**실제 Button.tsx 예시**:
```typescript
export const buttonStyles = tv({
  base: [
    'inline-flex items-center py-[5px] px-[20px] rounded-[4px]',
    'border font-normal text-[14px] leading-[20px] text-black',
    'transition-all duration-300 align-middle',
    'disabled:pointer-events-none cursor-pointer group',
  ],
  variants: {
    variant: {
      primary: 'bg-white border-gray-100 hover:border-gray-260 disabled:text-gray-120',
      confirm: 'bg-primary border-primary text-white hover:bg-gray-280',
      search: 'px-[15px] bg-primary border-primary text-white',
      // ...
    },
    size: { sm: '', md: '', lg: '' },
  },
  defaultVariants: { size: 'md' },
});
```

## Gotchas & Known Issues [coverage: high -- 3 sources]

- **Tailwind Preflight 충돌**: `@import "tailwindcss"` 시 h1~h5 font-size/weight가 `inherit`으로 리셋됨. `@layer base`에 명시적 정의 필수.
- **`!important` 남용 금지**: 레이어/스코프/선택자 구조로 specificity 해결.
- **rem/px 혼용 주의**: SCSS는 rem 기반, Tailwind 토큰은 px 기반. 혼용 시 스케일 충돌 발생.
- **`@source "../"` 경로 확인**: variant 선언 파일이 이 경로에 포함돼야 빌드 시 클래스 누락 없음.
- **Loader 애니메이션**: 기본 `animate-spin`(1s) 대신 `--animate-loader-spin`(0.6s) 사용 필수.

## Sources
- [[../../design-system-react/docs/04-style-system]]
- [[../../design-system-react/docs/10-tailwind-variants-full-refactor]]
- [[../../design-system-react/src/styles/tailwind.css]]
- [[../../design-system-react/src/styles/layers/tui-overrides.css]]
- [[../../design-system-react/src/styles/layers/layout.css]]
- [[../../design-system-react/src/styles/layers/lib.css]]
- [[../../design-system-react/src/styles/layers/date-picker.css]]
