---
concept: Visual Equivalence (시각 동등성)
last_compiled: 2026-04-15
topics_connected: [architecture, migration-strategy, styles-tailwind, testing]
status: active
---

# Visual Equivalence (시각 동등성)

## Pattern

"전환 전후 화면이 픽셀 수준에서 동일해야 한다"는 원칙이 이 프로젝트의 거의 모든 의사결정을 지배한다. 단순한 품질 기준이 아니라, **무엇을 하면 안 되는지**를 결정하는 상위 원칙으로 작동한다.

이 원칙이 반복해서 등장하는 이유는 기술 전환(스타일/타입/프레임워크)이 본질적으로 UI 변경 위험을 내포하기 때문이다. "더 나은 코드"가 "다른 화면"을 만들어버리는 순간, 그것은 전환이 아니라 기능 변경이 된다.

## Instances

- **architecture.md**: "전환 전후 UI는 픽셀 수준에서 동일해야 한다. 이것은 협상 불가 원칙이다." High mismatch = 0건이어야 전환 완료.
- **migration-strategy.md**: 2-Phase 전략 채택의 근본 이유. TSX와 Tailwind 전환을 분리한 것은 "시각 동등성 리스크가 크다"는 판단에서 나왔다.
- **styles-tailwind.md**: "Tailwind로 바꾼 뒤에도 화면은 전환 전 SCSS 기준과 동일해야 한다. 대략 비슷함은 합격이 아니다." 동등성 증명 없이 PR 병합 금지.
- **testing.md**: 22개 라우트 Playwright 스크린샷 비교가 유일한 공식 검증 수단. High mismatch 0, Medium mismatch 게이트별 허용치.
- **migration-strategy.md (이슈)**: Tailwind Preflight가 h1~h5 스타일을 리셋해 실제로 High mismatch가 발생했던 사례. 원칙이 실제 롤백 판단에도 사용됨.

## What This Means

시각 동등성 원칙은 **작업 속도보다 안전성을 택하는 구조적 결정**이다. 이 원칙 때문에:
- 2개 작업(TSX 전환 + Tailwind 전환)을 동시에 하지 않고 Phase로 분리했다
- 스타일 수치를 임의로 "더 좋게" 바꾸는 것이 금지됐다
- 매 Phase마다 게이트 검증이 필수화됐다
- Claude가 git을 실행하지 않는 규칙도 여기서 파생된다 (실수로 잘못된 전환을 커밋하는 것 방지)

이 원칙을 이해하지 못하면 "왜 이렇게 보수적인가"라는 질문을 반복하게 된다.

## Sources
- [[../topics/architecture]]
- [[../topics/migration-strategy]]
- [[../topics/styles-tailwind]]
- [[../topics/testing]]
