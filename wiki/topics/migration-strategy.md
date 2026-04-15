---
topic: Migration Strategy
last_compiled: 2026-04-15
source_count: 4
status: active
---

# Migration Strategy

## Summary [coverage: high -- 4 sources]

SCSS+PropTypes+React 18 → Tailwind v4+TypeScript+React 19 전환을 **2-Phase**로 분리하여 진행 중이다.

- **Phase 1 (TSX 전환)**: 완료 ✅ — 공통 컴포넌트 대다수 JSX→TSX 전환, forwardRef/...rest 적용, PropTypes 제거
- **Phase 2 (Tailwind 전환)**: 진행 예정 — SCSS→Tailwind CSS v4 교체, styled-components 제거, 22개 라우트 최종 시각 검증

전환 대상: 55개 파일 (공통 컴포넌트 28개 + 레이아웃 3개 + 뷰 24개)

## Timeline [coverage: high -- 4 sources]

```
Phase 1: TSX 전환 (완료 ✅, ~2026-03-20)
├── S-01: 인프라 구축 (@theme 토큰, cn.ts, base.css)
├── S-02: Button TSX + Tailwind 완전 전환 (기준 패턴)
├── S-03: 게이트 1 — Button 시각 검증
├── S-04~S-09: 나머지 공통 컴포넌트 JSX→TSX (SCSS 유지)
└── 결과: .tsx 파일 + SCSS import 유지 상태

Phase 2: Tailwind 전환 (예정)
├── S-10: 게이트 2 — 현재 상태(SCSS) 시각 기준선 확인
├── S-11: Input + Loader Tailwind 전환
├── S-12: Alert + Modal Tailwind 전환
├── S-13: Form 컴포넌트 (Checkbox, Radio, Dropdown) 전환
├── S-14: TUI 컴포넌트 (Calendar, Grid, Tree, Pagination) 전환
├── S-15: 나머지 공통 + 레이아웃 + styled-components 제거
├── S-16: 뷰 컴포넌트 24개 전환
└── S-17: 게이트 3 — 전체 22개 라우트 최종 검증
```

## Current State [coverage: high -- 4 sources]

**2026-03-20 기준 컴포넌트 전환 상태:**

| 컴포넌트 | TSX | Tailwind | 비고 |
|---------|-----|----------|------|
| Button | ✅ | ✅ | 완전 전환 완료 (기준 패턴) |
| Input | ✅ | ⬜ | SCSS 유지 |
| Loader | ✅ | ⬜ | styled-components → SCSS 파일로 추출 |
| Alert | ✅ | ⬜ | SCSS 유지 |
| Modal | ✅ | ⬜ | SCSS 유지 |
| Checkbox/CheckboxGroup | ✅ | ⬜ | SCSS 유지 |
| Radio/RadioGroup | ✅ | ⬜ | SCSS 유지 |
| Dropdown | ✅ | ⬜ | SCSS 유지 |
| Calendar | ✅ | ⬜ | SCSS 유지 |
| TuiGrid/TuiTree/TuiPagination | ✅ | ⬜ | SCSS 유지 |
| Tab | ✅ | ⬜ | SCSS 유지 |
| Tabs (Compound) | ✅ | ⬜ | src/components/compound/Tabs.tsx |
| Popup (Compound) | ✅ | ⬜ | src/components/compound/Popup.tsx |
| Tooltip | ✅ | ⬜ | SCSS 유지 |
| Card | ✅ | ⬜ | Vue 잔재 제거 완료 |
| Pagination | ✅ | ⬜ | useMemo 안티패턴 수정 완료 |
| Tree | ✅ | ⬜ | SCSS 유지 |
| SubButton | ✅ | ✅ | Button과 동일 세션 완전 전환 |
| SubInput | ✅ | ⬜ | SCSS 유지 |

**TSX 미전환 (Phase 1 잔여)**:
- Avatar.jsx, Breadcrumb.jsx, Link.jsx, Title.jsx, Prism.jsx
- FileDownloader.jsx, FileuploaderMultiple.jsx, FileuploaderSingle.jsx, DetailForm.jsx
- 레이아웃: View.jsx, DocsHeader.jsx, DocsSidebar.jsx (styled-components 잔존)
- 뷰 컴포넌트 대부분

## Key Decisions [coverage: medium -- 2 sources]

- **2-Phase 분리 이유**: 동시 진행 시 오류 원인(타입 오류 vs 시각 오류) 구분 불가. TSX 전환만으로도 forwardRef, PropTypes 제거, 타입 안전성 등 독립적 가치가 있다.
- **전환 우선순위**: 영향도(40) + 복잡도(25) + 회귀리스크(25) + TUI외존성(10) 점수로 정량화. P0: Button, Loader, Input, Alert, Modal.
- **TUI 전환 원칙**: `@apply` 사용 금지, `.tui-*` 최상위 안정 selector만 사용, tui-overrides.css @layer components에 추가.

## Known Issues & Blockers [coverage: high -- 2 sources]

**[High] h1~h5 헤딩 스타일 전멸**
- 원인: SCSS `_reset.scss`에서 `@each` 루프로 생성하던 h1~h5 스타일이 Tailwind Preflight로 모두 `inherit`으로 리셋됨
- 해결: `@layer base`에 h1~h5 스타일 명시 추가 (현재 tailwind.css에 적용됨)

**[High] TUI Tree 아이콘 깨짐**
- 원인: `tui-overrides.css`에 `.tui-ico-line`, `.tui-ico-file` background-image 누락
- 증상: 파일 노드 아이콘, leaf 노드 들여쓰기 선 아이콘 미표시

**styled-components 잔존 파일 (Phase 2에서 처리)**:
- src/layouts/View.jsx
- src/layouts/DocsHeader.jsx
- src/layouts/DocsSidebar.jsx
- src/views/components/SubPopup.jsx
- src/views/components/SubTooltipPagination.jsx

## Component Gap Analysis [coverage: medium -- 1 source]

Phase 2 주요 전환 계획:
- **S-11**: Input(`inputtype="search"` → variant 또는 SearchInput 분리 검토), Loader(0.66s 커스텀 animate-loader-spin)
- **S-12**: Alert(type별 색상 variant map), Modal(Portal + z-index 토큰)
- **S-13**: Checkbox/Radio/Dropdown 폼 컴포넌트
- **S-14**: TUI Calendar/Grid/Tree/Pagination (tui-overrides.css 병행)
- **S-15**: 레이아웃 3개 + styled-components 완전 제거
- **S-16**: 뷰 컴포넌트 24개

## Gotchas & Known Issues [coverage: high -- 3 sources]

- **Tailwind Preflight 충돌**: `@import "tailwindcss"` 시 기존 SCSS reset이 덮어써지는 케이스 다수. 헤딩 스타일이 대표 사례.
- **TUI 오버라이드 불안정**: `.tui-*` 클래스 선택자 안정성 검증 필수. background-image URL 누락 주의.
- **rem/px 스케일 충돌**: breakpoint는 px 단위 고정 (`--breakpoint-sm: 640px`). rem 혼용 금지.
- **저장 데이터 호환**: localStorage(theme), sessionStorage(grid filter) 키 구조 변경 시 초기화 리스크. 전환 전후 키 동일성 비교 필수.
- **React 19 defaultProps 지원 약화**: 전환 시 defaultProps 제거, 인자 기본값으로 교체 필수.

## Open Questions [coverage: low -- 1 source]

- Phase 2 착수 시점 미확정
- `src/views/dev/` (ApiTester, ColorPaletteTester)는 전환 제외 확정

## Sources
- [[../../design-system-react/docs/05-migration-strategy]]
- [[../../design-system-react/docs/08-session-plan]]
- [[../../design-system-react/docs/11-migration-issues]]
- [[../../design-system-react/docs/100-design-system-component-gap-analysis]]
