---
topic: Core Components
last_compiled: 2026-04-16
source_count: 16
status: active
---

# Core Components

## Summary [coverage: high -- 16 sources]

`src/components/*.tsx`에 28개 공통 재사용 컴포넌트가 있다. Phase 1(TSX 전환) 완료로 대다수가 TypeScript + forwardRef + ...rest 패턴으로 전환됐고, Button + SubButton만 Tailwind 완전 전환됐다. 나머지는 SCSS 유지 상태로 Phase 2 대기 중이다.

**tailwind-variants**(tv) 라이브러리가 variant 관리 표준이다. 모든 컴포넌트는 `cn()` 유틸로 className을 병합한다. 오버레이 컴포넌트(Modal, Dropdown, Tooltip)는 `#ds-portal-root`에 포털로 렌더링한다.

**react-hook-form**(^7.72.1)이 의존성으로 추가됐다. Input 컴포넌트와의 통합 패턴이 SubDetailForms 데모에 문서화되어 있다.

## Component Inventory [coverage: high -- 15 sources]

| 컴포넌트 | TSX | Tailwind | 주요 Props | 포털 |
|---------|-----|----------|-----------|------|
| Button | ✅ | ✅ | variant, size, icon, iconVariant | - |
| Input | ✅ | ⬜ | type, placeholder, ...rest | - |
| Alert | ✅ | ⬜ | type (confirm/error/warning/info) | - |
| Modal | ✅ | ⬜ | open, onClose, title, children | ✅ |
| Checkbox | ✅ | ⬜ | checked, onChange, label, disabled | - |
| CheckboxGroup | ✅ | ⬜ | options, value, onChange | - |
| Radio | ✅ | ⬜ | checked, onChange, label | - |
| RadioGroup | ✅ | ⬜ | options, value, onChange | - |
| Dropdown | ✅ | ⬜ | options, value, onChange | ✅ |
| DatePicker | ✅ | ⬜ | value, onChange, mode | - |
| Tab | ✅ | ⬜ | tabs, activeTab, onChange | - |
| Tooltip | ✅ | ⬜ | content, placement, children | ✅ |
| Card | ✅ | ⬜ | title, children | - |
| Pagination | ✅ | ⬜ | total, page, pageSize, onChange | - |
| Tree | ✅ | ⬜ | data, onSelect | - |
| Popup (Compound) | ✅ | ⬜ | open, onClose, children | ✅ |
| Tabs (Compound) | ✅ | ⬜ | defaultValue, children | - |
| Switch | ✅ | ⬜ | checked, onChange | - |
| Textarea | ✅ | ⬜ | value, onChange, rows | - |
| Toast | ✅ | ⬜ | message, type, duration | ✅ |
| Separator | ✅ | ⬜ | orientation | - |
| Loader | ✅ | ⬜ | fullscreen, size | - |
| Icon | ✅ | ✅ | type, size, color | - |
| Editor | ✅ | ⬜ | value, onChange (Tiptap 기반) | - |
| SearchForm | ✅ | ⬜ | children (FormItem 컴포지션) | - |
| DetailForm | - | ⬜ | JSX 미전환 상태 | - |
| FileuploaderMultiple | - | ⬜ | JSX 미전환 상태 | - |
| FileuploaderSingle | - | ⬜ | JSX 미전환 상태 | - |

## Common Patterns [coverage: high -- 5 sources]

**표준 컴포넌트 패턴 (Button.tsx 기준)**:
```typescript
import { forwardRef } from 'react';
import { tv, type VariantProps } from 'tailwind-variants';
import { cn } from '@/lib/cn';

const buttonStyles = tv({
  base: ['inline-flex items-center ...', 'cursor-pointer group'],
  variants: {
    variant: {
      primary: 'bg-white border-gray-100 hover:border-gray-260',
      confirm: 'bg-primary border-primary text-white',
    },
    size: { sm: '', md: '', lg: '' },
  },
  defaultVariants: { size: 'md' },
});

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonStyles> {
  // 추가 props
}

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ variant, size = 'md', className, children, ...rest }, ref) => (
    <button
      ref={ref}
      className={cn(buttonStyles({ variant, size }), className)}
      {...rest}
    >
      {children}
    </button>
  ),
);
Button.displayName = 'Button';
```

**cn() 유틸**: `src/lib/cn.ts` — clsx + tailwind-merge. 모든 className 병합에 사용.

**네이티브 props 전달**: `...rest`로 `aria-label`, `data-testid`, `onMouseEnter` 등 전달 보장.

**아이콘 hover 상태**: `group` + `group-hover:` 패턴으로 부모-자식 연동.

## Form Integration (react-hook-form) [coverage: medium -- 1 source]

`react-hook-form` ^7.72.1이 의존성에 추가됐다. Input 컴포넌트가 `...rest` 전달을 지원하므로 `register`를 직접 spread할 수 있다.

**기본 셋업**:
```typescript
import { useForm } from 'react-hook-form';

interface FormValues {
  name: string;
  email: string;
  phone: string;
}

const {
  register,
  handleSubmit,
  reset,
  formState: { errors },
} = useForm<FormValues>({ mode: 'onSubmit' });
```

**Input + register 패턴**:
```typescript
<Input
  {...register('name', { required: '이름을 입력하세요.' })}
  className={errors.name ? 'is-error' : ''}
  placeholder="이름"
/>
{errors.name && (
  <p className="text-[12px] text-[color:var(--color-point-01)] mt-[4px]">
    {errors.name.message}
  </p>
)}
```

**유효성 규칙 예시**:
- 필수 입력: `{ required: '메시지' }`
- 이메일 패턴: `{ pattern: { value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/, message: '...' } }`
- 전화번호 패턴: `{ pattern: { value: /^[0-9]{2,3}-[0-9]{3,4}-[0-9]{4}$/, message: '...' } }`

**에러 상태 표시**: Input에 `is-error` className 적용 → SCSS에 정의된 에러 스타일 활성화. 에러 메시지는 Input 하단에 별도 `<p>` 태그로 표시.

## Compound Components [coverage: medium -- 2 sources]

`src/components/compound/`에 복합 패턴 컴포넌트 2개:

**Popup** — Modal보다 단순한 팝업. 내부에 `FocusTrap`으로 키보드 포커스 격리, `#ds-portal-root`에 포털 렌더링.

**Tabs** — Radix UI 기반 탭 컴포지션. TabsList + TabsTrigger + TabsContent 컴포지션 패턴.

## Portal Usage [coverage: medium -- 3 sources]

다음 컴포넌트들이 `#ds-portal-root`(index.html에 선언)에 포털로 렌더링됨:
- Modal, Dropdown, Tooltip, Toast, Popup

z-index는 반드시 `@theme` 토큰 사용:
```css
--z-index-dropdown: 1000
--z-index-modal-backdrop: 1040
--z-index-modal: 1050
--z-index-tooltip: 1100
```

## Gotchas & Known Issues [coverage: medium -- 3 sources]

- **두 개의 Button**: `src/components/Button.tsx` (tailwind-variants)와 `src/components/shadcn/Button.tsx` (cva)가 공존. import 경로 주의.
- **Loader 애니메이션**: `animate-spin`(1s) 대신 `animate-loader-spin`(0.6s) 사용. @theme에 정의됨.
- **Input search variant**: `inputtype="search"` 분기 → Phase 2에서 `variant` prop 또는 별도 `SearchInput` 분리 예정.
- **Editor**: Tiptap 기반. `@layer components .tiptap` 스타일이 tailwind.css에 정의됨.
- **react-hook-form + Input**: `register` spread 시 Input의 `...rest`로 전달됨. 에러 상태는 `is-error` className으로 표시 — controlled state 없이 SCSS 스타일 활성화.

## Sources
- [[../../design-system-react/src/components/Button]]
- [[../../design-system-react/src/components/Input]]
- [[../../design-system-react/src/components/Modal]]
- [[../../design-system-react/src/components/Alert]]
- [[../../design-system-react/src/components/Dropdown]]
- [[../../design-system-react/src/components/Checkbox]]
- [[../../design-system-react/src/components/Radio]]
- [[../../design-system-react/src/components/Tab]]
- [[../../design-system-react/src/components/Tooltip]]
- [[../../design-system-react/src/components/DatePicker]]
- [[../../design-system-react/src/components/Pagination]]
- [[../../design-system-react/src/components/Tree]]
- [[../../design-system-react/src/components/compound/Popup]]
- [[../../design-system-react/src/components/compound/Tabs]]
- [[../../design-system-react/src/lib/cn.ts]]
- [[../../design-system-react/src/views/components/SubDetailForms]]
