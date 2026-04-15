# llm-wiki — Design System React

[design-system-react](../design-system-react) 프로젝트를 위해 컴파일된 LLM 전용 지식 베이스입니다.

Claude Code가 소스 파일을 직접 탐색하는 대신 이 위키를 먼저 참고함으로써 컨텍스트를 절약하고 더 정확한 답변을 제공합니다.

---

## 구조

```
wiki/
├── INDEX.md          # 전체 토픽 목록 및 통계
├── CONTEXT.md        # 위키 사용 가이드 (Claude용)
├── log.md            # 컴파일 로그
├── schema.md         # 위키의 운영 규칙, 톤앤매너, 지식 추출 가이드를 정의한 스키마
├── topics/           # 여러 소주제를 묶어놓은 '책의 챕터(장)' or '폴더 카테고리’
│   ├── architecture.md
│   ├── core-components.md
│   ├── shadcn-components.md
│   ├── tui-components.md
│   ├── layout-components.md
│   ├── skeleton-components.md
│   ├── styles-tailwind.md
│   ├── migration-strategy.md
│   ├── storybook.md
│   └── testing.md
└── concepts/         # 여러 단어가 모여 만들어진 '문단'이나 '소주제’
    └── visual-equivalence.md
```

---

## 커버리지 (2026-04-15 기준)

| 토픽 | 소스 수 |
|------|--------|
| architecture | 5 |
| migration-strategy | 4 |
| styles-tailwind | 7 |
| core-components | 15 |
| shadcn-components | 12 |
| tui-components | 14 |
| layout-components | 7 |
| skeleton-components | 4 |
| storybook | 3 |
| testing | 2 |
| **합계** | **118** |

---

## 위키 재컴파일

소스 프로젝트가 업데이트된 경우 Claude Code에서 실행:

```
/wiki-compile 실행 시, 
design-system-react에서 '.compile-state.json'에 있는 'last_commit" 값 이후 커밋된 파일만 골라서 처리하게 된다.
```
