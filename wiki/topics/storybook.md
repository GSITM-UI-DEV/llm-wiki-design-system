---
topic: Storybook
last_compiled: 2026-04-15
source_count: 3
status: active
---

# Storybook

## Summary [coverage: medium -- 3 sources]

Storybook v8이 `storybook/` 디렉토리에 설치됐다. 28개 공통 컴포넌트의 격리된 개발·테스트·시각 문서화 환경을 제공하는 것이 목표다. `pnpm storybook`으로 실행(localhost:6006).

기존 `src/views/components/` 기반 라우팅 문서 앱과 병행 운영하며, Storybook은 컴포넌트 격리 개발/Props 탐색용, 기존 앱은 시스템 통합 문서용으로 역할이 다르다.

## Current State [coverage: medium -- 2 sources]

- Storybook 설치 완료 (`storybook/.storybook/main.ts` 존재)
- 스토리 파일(`.stories.tsx`) 작성은 아직 초기 단계
- Vite 기반 빌드 (`@storybook/react-vite`)
- 애드온: a11y, actions, controls, docs, interactions, viewport

## Directory Structure [coverage: medium -- 2 sources]

```
storybook/
├── .storybook/
│   ├── main.ts           # Storybook 번들 설정 (vite, addons)
│   ├── preview.ts        # 전역 데코레이터, 테마
│   └── preview-head.html # head 주입 (Tailwind CSS, fonts)
└── stories/
    ├── 01-foundations/   # Design Token 시각화
    ├── 02-common/        # 공통 컴포넌트 스토리
    ├── 03-form/          # Form 컴포넌트 스토리
    └── 04-tui/           # TUI 래퍼 스토리
```

## Key Decisions [coverage: medium -- 2 sources]

- **storybook/ 루트 분리**: 앱 src/와 완전 분리. 오염 없이 독립적 실행 가능.
- **기존 views/components/ 유지**: 라우팅 기반 통합 문서 앱과 병행. 역할 중복 아님.
- **Vite 기반**: `@storybook/react-vite`로 빠른 HMR 지원.

## Gotchas & Known Issues [coverage: low -- 1 source]

- Tailwind CSS 토큰이 Storybook 환경에서도 적용되려면 `preview-head.html`에 tailwind.css import 필요.
- TUI 컴포넌트 스토리는 명령형 API 특성상 작성 복잡도 높음.

## Sources
- [[../../design-system-react/docs/09-storybook]]
- [[../../design-system-react/docs/storybook-guide]]
- [[../../design-system-react/storybook/.storybook/main.ts]]
