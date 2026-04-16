---
topic: Shadcn Components
last_compiled: 2026-04-16
source_count: 13
status: active
---

# Shadcn Components

## Summary [coverage: high -- 13 sources]

`src/components/shadcn/`에 shadcn/ui 기반 컴포넌트가 있다. shadcn/ui 원본을 직접 복사(not npm install)하여 프로젝트에 맞게 커스터마이징한 방식이다. `cva`(class-variance-authority) + `cn()` 조합으로 variant를 관리하며, Radix UI 프리미티브를 기반으로 한다.

2026-04-16 기준 11개 컴포넌트가 있다. 이 중 **Card** 컴포넌트는 shadcn 디렉터리에 위치하지만 예외적으로 `tailwind-variants`(tv)를 사용한다 — 다른 shadcn 컴포넌트들이 `cva`를 사용하는 것과 다르다.

## Component Inventory [coverage: high -- 11 sources]

| 컴포넌트 | 기반 | 주요 특이사항 |
|---------|------|------------|
| Button | cva + Radix Slot | asChild 지원, icon/iconVariant 이중 아이콘 API, 8개 variant |
| Alert | cva | variant: default/destructive |
| Avatar | Radix Avatar | AvatarImage + AvatarFallback 컴포지션 |
| Dialog | Radix Dialog | DialogTrigger/Content/Header/Footer/Title/Description |
| Select | Radix Select | SelectTrigger/Content/Item 컴포지션 |
| Checkbox | Radix Checkbox | CheckedState 타입 지원 |
| Collapsible | Radix Collapsible | CollapsibleTrigger/Content |
| Skeleton | 순수 div | animate-pulse 기반 로딩 스켈레톤 |
| Spinner | 순수 div | animate-loader-spin (0.6s 커스텀) |
| Tree | 재귀 컴포지션 | TreeItem, TreeItemLabel 등 내부 컴포넌트 포함 |
| Card | tailwind-variants (tv) | colSpan(1/2/3), rowSpan(1/2/3), type(searchForm/content), forwardRef |

## Button API (상세) [coverage: high -- 1 source]

shadcn Button은 `cva`로 8개 variant를 정의한다:

| variant | 설명 |
|---------|------|
| primary | 흰색 배경, 연회색 보더 (기본) |
| outline | 회색 보더, hover 시 배경 변경 |
| confirm | 검정 배경, 흰색 텍스트 (강조) |
| search | 검정 배경 + 검색 아이콘 |
| shuffle | 작은 아이콘 전용 버튼 |
| icon | 아이콘 전용 버튼 |
| popupBlack | 팝업 내 확인 버튼 |
| popupWhite | 팝업 내 취소 버튼 |

**아이콘 API 두 가지**:
```typescript
// 1. iconVariant — 자동 크기/hover/disabled 상태 처리
<Button iconVariant="search">검색</Button>

// 2. icon — 직접 ReactNode 전달 (호출자가 상태 관리)
<Button icon={<MyIcon />}>버튼</Button>
```

**asChild 패턴 (Radix Slot)**:
```typescript
// 자식 엘리먼트에 버튼 스타일 위임
<Button asChild>
  <a href="/link">링크 버튼</a>
</Button>
```

## Card API (상세) [coverage: high -- 1 source]

Card는 shadcn 디렉터리에 위치하지만 `tailwind-variants`(tv)의 slots API를 사용한다:

```typescript
export interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  colSpan?: 1 | 2 | 3;   // CSS grid col-span (기본값: 1)
  rowSpan?: 1 | 2 | 3;   // CSS grid row-span
  type?: 'searchForm' | 'content';  // 기본값: 'content'
}
```

| type | 패딩 |
|------|------|
| content | `pt-[20px] pb-[30px]` (기본) |
| searchForm | `py-[25px]` |

스타일: `px-[20px] rounded-[13px] bg-white shadow-[0_0_16px_0_rgba(0,0,0,0.15)] w-full`

## Key Decisions [coverage: medium -- 2 sources]

- **shadcn/ui 복사 전략**: npm 패키지가 아닌 소스 복사로 완전한 커스터마이징 가능. components.json이 shadcn CLI 설정 파일.
- **cva vs tailwind-variants**: shadcn 컴포넌트는 대부분 `cva`(class-variance-authority) 사용. 단, **Card는 예외** — `tailwind-variants`(tv)의 slots API를 사용. 두 라이브러리 공존.
- **Radix UI 기반**: Dialog, Select, Checkbox, Collapsible, Avatar 등 접근성이 중요한 컴포넌트는 Radix UI 프리미티브 활용.

## API Surface [coverage: high -- 8 sources]

```typescript
// Dialog 사용 패턴
<Dialog open={open} onOpenChange={setOpen}>
  <DialogTrigger>열기</DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>제목</DialogTitle>
    </DialogHeader>
    {/* 내용 */}
  </DialogContent>
</Dialog>

// Select 사용 패턴
<Select onValueChange={setValue}>
  <SelectTrigger><SelectValue placeholder="선택" /></SelectTrigger>
  <SelectContent>
    <SelectItem value="a">옵션A</SelectItem>
  </SelectContent>
</Select>

// Card 사용 패턴
<Card colSpan={2} type="searchForm">
  {/* 검색폼 내용 */}
</Card>
```

## Gotchas & Known Issues [coverage: medium -- 2 sources]

- `src/components/shadcn/Button.tsx`와 `src/components/Button.tsx`가 별도 구현으로 공존. shadcn Button은 `cva`, 일반 Button은 `tailwind-variants`. 혼용 시 주의.
- `@import 'shadcn/tailwind.css'`가 tailwind.css 마지막에 포함됨 — shadcn 기본 CSS 변수 오버라이드 가능.
- Skeleton 컴포넌트는 `animate-pulse` 사용 (Tailwind 기본값). Spinner는 `animate-loader-spin`(0.6s 커스텀) 사용.
- **Card는 shadcn 디렉터리지만 tv 사용**: `src/components/shadcn/Card.tsx`는 shadcn 스타일이 아닌 tailwind-variants 패턴으로 구현됨.

## Sources
- [[../../design-system-react/docs/12-shadcn-migration]]
- [[../../design-system-react/components.json]]
- [[../../design-system-react/src/components/shadcn/Button]]
- [[../../design-system-react/src/components/shadcn/Alert]]
- [[../../design-system-react/src/components/shadcn/Dialog]]
- [[../../design-system-react/src/components/shadcn/Select]]
- [[../../design-system-react/src/components/shadcn/Checkbox]]
- [[../../design-system-react/src/components/shadcn/Avatar]]
- [[../../design-system-react/src/components/shadcn/Spinner]]
- [[../../design-system-react/src/components/shadcn/Skeleton]]
- [[../../design-system-react/src/components/shadcn/Tree]]
- [[../../design-system-react/src/components/shadcn/Collapsible]]
- [[../../design-system-react/src/components/shadcn/Card]]
