---
topic: Architecture
last_compiled: 2026-04-16
source_count: 5
status: active
---

# Architecture

## Summary [coverage: high -- 5 sources]

`design-system-react`는 React 19 + TypeScript 5.9 + Tailwind CSS v4 + Vite 5 기반 UI 디자인 시스템 컴포넌트 라이브러리다. 총 52개 컴포넌트(공통 28개 + 뷰 24개)를 문서화하고 시연하는 React Router 기반 SPA다.

핵심 목표는 **기술 현대화 + UI 시각 동등성 유지**다. SCSS+styled-components → Tailwind CSS v4, JSX+PropTypes → TSX+TypeScript 전환 중이며, 전환 전후 화면이 픽셀 수준에서 동일해야 한다는 것이 협상 불가 원칙이다. 2026-03-20 기준 Phase 1(TSX 전환)은 완료됐고, Phase 2(Tailwind 전환)가 예정 중이다.

기술 스택: React 19.2.3 / TypeScript 5.9.3 / Tailwind CSS v4.2.2 / Vite 5.4.21 / pnpm 9.0.0 / Storybook 8 / react-hook-form ^7.72.1

## Current State [coverage: high -- 5 sources]

**스타일 3계층 공존 중 (마이그레이션 과도기)**:
1. **SCSS** — `src/assets/scss/` 42개 파일, 150KB+, 레거시 주력
2. **styled-components** — 11개 파일, 레이아웃/뷰 중심, 일부 제거 완료
3. **Tailwind CSS v4** — `src/styles/tailwind.css` 진입점, 신규 컴포넌트 기준

**컴포넌트 전환 현황 (2026-03-20)**:
- TSX 전환: 대다수 공통 컴포넌트 완료 (Button, Input, Modal, Alert, Checkbox 등)
- Tailwind 전환: Button + SubButton만 완전 전환, 나머지는 SCSS 유지
- styled-components 잔존: View.jsx, DocsHeader.jsx, DocsSidebar.jsx 등

**포털 앵커**: `index.html`의 `<div id="ds-portal-root">` — Modal, Dropdown, Tooltip, Toast가 이 노드를 사용.

## Key Decisions [coverage: high -- 5 sources]

- **2-Phase 전략 채택** — TSX 전환과 Tailwind 전환을 분리. 두 작업 동시 진행 시 오류 원인 분석 어렵고 시각 동등성 리스크가 크기 때문.
- **Tailwind CSS v4 선택** — CSS-first 방식(`@theme`, `@layer`), 빌드 최적화. SCSS 유지나 CSS-in-JS는 기술 부채 지속/런타임 비용으로 배제.
- **TSX + TypeScript 전면 전환** — PropTypes(런타임 검증)의 한계, React 19에서 `defaultProps` 지원 약화.
- **forwardRef + ...rest 필수 표준** — 모든 컴포넌트에 `forwardRef` 적용, `...rest`로 네이티브 HTML 속성 전달 보장.
- **`cn()` 유틸 SSOT** — `src/lib/cn.ts` (clsx + tailwind-merge). 조건부 className 병합은 반드시 이 함수 사용.
- **LLM 지식 위키 운영** — `../llm-wiki-design-system/wiki/`에 컴파일된 위키가 존재한다. Claude Code는 코드베이스 탐색 전 위키를 먼저 참조한다. 네비게이션 가이드: `wiki/CONTEXT.md`, 전체 목차: `wiki/INDEX.md`.
- **git 명령 Claude 금지** — git add/commit/push 등 모든 git 작업은 사용자가 직접 수행.

## Directory Structure [coverage: high -- 5 sources]

```
src/
├── components/          # 핵심 재사용 컴포넌트
│   ├── *.tsx            # 공통 컴포넌트 28개 (Button, Input, Modal 등)
│   ├── compound/        # Compound 컴포넌트 (Popup, Tabs)
│   ├── layout/          # 앱 레이아웃 (Mdi, Header, Lnb)
│   ├── shadcn/          # shadcn/ui 기반 커스텀 컴포넌트
│   ├── skeleton/        # 로딩 스켈레톤 컴포넌트
│   ├── tui/             # Toast UI 래퍼 컴포넌트
│   └── icons/           # SVG 아이콘 컴포넌트들
├── views/components/    # 컴포넌트 데모/문서 페이지 (재사용 X)
├── lib/
│   └── cn.ts            # className 병합 유틸 (SSOT)
├── styles/
│   ├── tailwind.css     # Tailwind v4 진입점 (@theme 토큰 포함)
│   └── layers/          # 레이어 파일들 (layout, tui-overrides 등)
├── routes/index.jsx     # React Router 라우트 정의
└── api/                 # Axios 기반 API 모듈
```

## Component API Standards [coverage: high -- 5 sources]

**표준 패턴 (실제 Button.tsx 기준)**:
```typescript
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ variant, size = 'md', icon, className, disabled, children, ...rest }, ref) => {
    return (
      <button
        ref={ref}
        disabled={disabled}
        aria-disabled={disabled}
        className={cn(buttonStyles({ variant, size }), className)}
        {...rest}
      >
        {children}
      </button>
    );
  },
);
Button.displayName = 'Button';
```

**variant 클래스 선언 규칙 (Tailwind v4 정적 분석 안전)**:
- 동적 문자열 조합 금지: ~~`text-${color}-500`~~
- full class name이 코드에 직접 노출되는 객체 매핑 사용
- `tailwind-variants`(tv) 라이브러리 적극 활용

**타입 계층**:
```typescript
// 네이티브 HTML 속성 확장
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>, ButtonVariants {
  icon?: IconType;
}
```

## Constraints & Rules [coverage: high -- 5 sources]

**절대 원칙**:
- UI 시각 동등성: 전환 전후 화면 픽셀 수준 동일 (협상 불가)
- High mismatch(레이아웃 깨짐, 색상 완전 다름) = **0건**이어야 전환 완료 인정
- 기능 추가/변경 금지 — 기술 전환만 수행
- 스타일 수치(색상, spacing, font-size) 임의 변경 금지

**코드 규칙**:
- 들여쓰기: 스페이스 2칸 (탭 금지)
- 컴포넌트 파일: PascalCase.tsx
- 유틸 파일: camelCase.ts
- 경로 alias `@` → `src/`
- `!important` 남용 금지 — 레이어/스코프/선택자 구조로 해결

**검증 명령**:
```bash
pnpm tsc --noEmit  # 오류 0
pnpm build         # 성공
```

**UI 비교 대상**: 20개 라우트 (`/components/input`, `/components/grid` 등), Playwright MCP로 운영 서버 vs 로컬 비교.

## Open Questions [coverage: low -- 1 source]

- styled-components 완전 제거 타임라인 미확정 (View.jsx, DocsHeader.jsx, DocsSidebar.jsx 잔존)
- 뷰 컴포넌트(24개) Tailwind 전환 세부 일정 미확정
- `src/views/dev/` (ApiTester, ColorPaletteTester) 전환 제외 확정됐으나 유지보수 전략 없음

## Sources
- [[../../design-system-react/docs/01-scope]]
- [[../../design-system-react/docs/02-architecture]]
- [[../../design-system-react/docs/03-component-design]]
- [[../../design-system-react/CLAUDE]]
- [[../../design-system-react/README]]
