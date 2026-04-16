---
topic: TUI Components
last_compiled: 2026-04-16
source_count: 14
status: active
---

# TUI Components

## Summary [coverage: high -- 14 sources]

`src/components/tui/`에 Toast UI 라이브러리(tui-grid, tui-tree, tui-date-picker, tui-pagination, @toast-ui/select-box)를 React로 래핑한 컴포넌트들이 있다. 이 라이브러리들은 **명령형(jQuery 스타일)**이므로 React 패턴(`ref` + `useImperativeHandle`)으로 라이프사이클을 직접 관리한다.

TUI 컴포넌트의 CSS는 SCSS 기반이며 Phase 2에서 Tailwind 전환 예정이나, 오버라이드는 `tui-overrides.css`로 별도 관리된다.

## Component Inventory [coverage: high -- 5 sources]

| 컴포넌트 | TUI 원본 | forwardRef | 비고 |
|---------|---------|-----------|------|
| TuiGrid | @toast-ui/react-grid | ✅ (Grid 인스턴스 노출) | useImperativeHandle로 Grid API 노출 |
| TuiTree | tui-tree | ✅ | useRef + useEffect 라이프사이클 |
| TuiCalendar | tui-date-picker | - | DatePicker + TimePicker 조합 |
| TuiDropdown | @toast-ui/select-box | - | SelectBox 명령형 래퍼 |
| TuiPagination | tui-pagination | - | 페이지네이션 명령형 래퍼 |

## Architecture Pattern [coverage: high -- 5 sources]

명령형 TUI 라이브러리를 React에서 사용하는 표준 패턴:

```typescript
// TuiGrid — useImperativeHandle로 Grid 인스턴스 직접 노출
export const TuiGrid = forwardRef<TuiGridRef, TuiGridProps>((props, ref) => {
  const innerRef = useRef<ToastGrid>(null);

  // 마운트 후 Grid 인스턴스를 부모에게 노출
  useImperativeHandle(ref, () => innerRef.current!.getInstance(), []);

  return (
    <div className="grid">
      <ToastGrid
        ref={innerRef}
        data={data as never[]}
        columns={columns as never[]}
        // ...
      />
    </div>
  );
});
```

**부모에서 Grid API 직접 호출**:
```typescript
const gridRef = useRef<TuiGridRef>(null);
// 마운트 후 tui-grid API 직접 접근
gridRef.current?.resetData(newData);
gridRef.current?.setColumnValues('colName', values);
```

## TuiGrid Props [coverage: high -- 1 source]

```typescript
interface TuiGridProps {
  data?: unknown[];
  columns?: unknown[];
  rowHeaders?: unknown[];
  columnOptions?: Record<string, unknown>;
  complexColumns?: unknown[];
  height?: number;           // 행 높이 (기본 44px)
  scroll?: {
    scrollX?: boolean;
    scrollY?: boolean;
    bodyHeight?: number;
  };
  // 이벤트
  onDblclick?: (e: any) => void;
  onClick?: (e: any) => void;
  onCheck?: (e: any) => void;
  onUncheck?: (e: any) => void;
  onCheckAll?: (e: any) => void;
  onFocusChange?: (e: any) => void;
}
```

## Grid Renderers [coverage: high -- 9 sources]

`src/components/tui/renderer/`에 TuiGrid 셀 내부에 React 컴포넌트를 렌더링하는 커스텀 렌더러들이 있다:

| 렌더러 | 역할 |
|-------|------|
| RenderButton | 셀 내 버튼 렌더링 |
| RenderCheckbox | 셀 내 체크박스 렌더링 |
| RenderComplexHeader | 복합 헤더 렌더링 |
| RenderDatePicker | 셀 내 날짜 선택기 |
| RenderDropdown | 셀 내 드롭다운 |
| RenderInput | 셀 내 입력 필드 |
| RenderLink | 셀 내 링크 |
| RenderState | 셀 내 상태 표시 |
| RenderSkeleton | 셀 로딩 스켈레톤 |

## TUI CSS Overrides [coverage: medium -- 1 source]

`src/styles/layers/tui-overrides.css`에서 TUI 라이브러리 기본 CSS를 오버라이드한다.

**원칙**:
- `@apply` 사용 금지
- `.tui-grid-*`, `.tui-tree-wrap`, `.tui-date-picker` 등 최상위 안정 selector만 사용
- `@layer components` 블록 내에서 정의

**알려진 미정의 (버그)**:
- `.tui-ico-line` — TuiTree leaf 노드 들여쓰기 선 아이콘 스타일 누락
- `.tui-ico-file` — background-image URL 누락 → 파일 노드 아이콘 깨짐

## Gotchas & Known Issues [coverage: high -- 3 sources]

- **라이프사이클 직접 관리**: TUI 인스턴스 생성/소멸은 React 생명주기와 별개. useEffect cleanup에서 TUI 인스턴스 destroy 필수.
- **styled-components 제거 주의**: 일부 TUI 래퍼가 styled-components를 사용했으나 SCSS 파일로 추출 완료됨. Phase 2 전환 시 SCSS → tui-overrides.css 통합.
- **@apply 금지**: tui-overrides.css 내에서 Tailwind `@apply` 사용 시 빌드 오류 또는 우선순위 충돌 발생.
- **TUI CSS 임포트**: `import 'tui-grid/dist/tui-grid.css'`가 TuiGrid.tsx 상단에 직접 임포트됨. 빌드 순서 주의.

## Sources
- [[../../design-system-react/src/components/tui/TuiGrid]]
- [[../../design-system-react/src/components/tui/TuiTree]]
- [[../../design-system-react/src/components/tui/TuiCalendar]]
- [[../../design-system-react/src/components/tui/TuiDropdown]]
- [[../../design-system-react/src/components/tui/TuiPagination]]
- [[../../design-system-react/src/components/tui/renderer/RenderButton]]
- [[../../design-system-react/src/components/tui/renderer/RenderCheckbox]]
- [[../../design-system-react/src/components/tui/renderer/RenderComplexHeader]]
- [[../../design-system-react/src/components/tui/renderer/RenderDatePicker]]
- [[../../design-system-react/src/components/tui/renderer/RenderDropdown]]
- [[../../design-system-react/src/components/tui/renderer/RenderInput]]
- [[../../design-system-react/src/components/tui/renderer/RenderLink]]
- [[../../design-system-react/src/components/tui/renderer/RenderState]]
- [[../../design-system-react/src/styles/layers/tui-overrides.css]]
