# Wiki Schema — Design System React

## Topics

| Slug | Description |
|------|-------------|
| architecture | 프로젝트 전체 아키텍처, 기술 스택, 컴포넌트 API 표준, 절대 원칙 |
| migration-strategy | JSX→TSX, SCSS→Tailwind 2-Phase 전환 전략, 현황, 이슈 |
| styles-tailwind | Tailwind CSS v4 설정, @theme 토큰, @layer 구조, cn() 유틸 |
| shadcn-components | shadcn/ui 기반 커스텀 컴포넌트 (Button, Dialog, Select 등) |
| tui-components | Toast UI 래퍼 컴포넌트 (Grid, Tree, Calendar 등), Grid 렌더러 |
| core-components | 핵심 재사용 컴포넌트 28개 (Button, Input, Modal 등) |
| layout-components | 앱 레이아웃 컴포넌트 (MDI, GNB, LNB) |
| skeleton-components | 로딩 스켈레톤 컴포넌트 4개 |
| storybook | Storybook v8 설정, 스토리 구조, 실행 방법 |
| testing | 테스트/품질 전략, 시각 회귀 테스트, 코드 컨벤션 |

## Concepts

| Slug | Description | Connects |
|------|-------------|---------|
| visual-equivalence | 시각 동등성 원칙 — 전환 전후 픽셀 수준 동일 필수 | architecture, migration-strategy, styles-tailwind, testing |

## Naming Conventions

- Topic slugs: lowercase-kebab-case
- Concept slugs: lowercase-kebab-case
- Link style: obsidian (`[[relative/path]]`)
- Language: 한국어 (코드/라이브러리명 제외)

## Evolution Log

- 2026-04-15: Initial schema generated from 10 topics, 1 concept
