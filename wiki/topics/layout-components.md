---
topic: Layout Components
last_compiled: 2026-04-15
source_count: 7
status: active
---

# Layout Components

## Summary [coverage: high -- 7 sources]

`src/components/layout/`에 앱 셸 레이아웃 컴포넌트가 있다. MDI(Multi Document Interface) 구조로 GNB(Global Navigation Bar), LNB(Local Navigation Bar), 컨텐츠 영역으로 구성된다. Tailwind CSS로 전환됐으며 `src/styles/layers/layout.css`에 레이아웃 전용 CSS가 정의된다.

LNB는 메뉴 탭과 북마크 탭 두 개의 탭으로 구성되며, 검색(AutoComplete), 브레드크럼, 아바타, 북마크 기능을 포함한다. API(`getSubMenuByMainId`)와 연동해 동적 메뉴를 렌더링한다.

## Component Structure [coverage: high -- 6 sources]

```
src/components/layout/
├── Mdi.tsx              # MDI 루트 레이아웃 (GNB + LNB + Content 영역)
├── header/
│   ├── index.tsx        # Header 컴포넌트
│   └── Gnb.tsx          # Global Navigation Bar (상단 앱바)
└── lnb/
    ├── index.tsx        # LNB 컨테이너 (메뉴 검색, 탭 관리)
    ├── MenuTab.tsx      # 메뉴 트리 탭
    └── BookmarkTab.tsx  # 북마크 탭
```

## Current State [coverage: medium -- 3 sources]

- `layout.css`에서 Tailwind 유틸리티 + CSS 레이어로 레이아웃 구조 정의
- LNB는 cn() + Tailwind 클래스 사용 (전환 진행됨)
- `src/layouts/` 디렉토리의 View.jsx, DocsHeader.jsx, DocsSidebar.jsx는 **styled-components 잔존** — Phase 2 전환 대상

## Key Features [coverage: high -- 5 sources]

**LNB 주요 기능**:
- `AutoComplete` 컴포넌트로 메뉴 검색
- `Tab` 컴포넌트로 메뉴/북마크 전환
- `Breadcrumb` 현재 위치 표시
- `Avatar` 사용자 프로필
- `BookmarkIcon`/`BookmarkActiveIcon`으로 북마크 토글
- `useNavigate`(React Router)로 메뉴 클릭 시 라우팅

**API 연동**:
```typescript
// getSubMenuByMainId(5)로 서브메뉴 목록 조회
const groups = getSubMenuByMainId(5) as MenuGroup[];

// 검색용 옵션으로 flatten
const menuOptions = useMemo(() => {
  return groups.flatMap(group =>
    group.contents.map(item => ({
      label: item.subTitle,
      value: item.name,
      groupTitle: group.title,
    }))
  );
}, []);
```

## Gotchas & Known Issues [coverage: medium -- 2 sources]

- `src/layouts/` (View, DocsHeader, DocsSidebar)와 `src/components/layout/` 두 디렉토리가 존재. 역할이 다름: `layouts/`는 문서 앱 셸, `components/layout/`은 실제 앱 레이아웃.
- styled-components 잔존: View.jsx, DocsHeader.jsx, DocsSidebar.jsx — Phase 2에서 Tailwind로 전환 예정.

## Sources
- [[../../design-system-react/src/components/layout/Mdi]]
- [[../../design-system-react/src/components/layout/header/index]]
- [[../../design-system-react/src/components/layout/header/Gnb]]
- [[../../design-system-react/src/components/layout/lnb/index]]
- [[../../design-system-react/src/components/layout/lnb/MenuTab]]
- [[../../design-system-react/src/components/layout/lnb/BookmarkTab]]
- [[../../design-system-react/src/styles/layers/layout.css]]
