# ⚖️ React 상태 관리 라이브러리 비교

## 1. ✅ Redux

### 특징
- 가장 널리 알려진 상태 관리 라이브러리
- 상태를 중앙에서 관리하며, 액션 → 리듀서 → 상태 업데이트 구조
- Redux Toolkit을 사용하면 보일러플레이트 코드를 줄일 수 있음

### 사용 예시

```
// counterSlice.ts (Redux Toolkit)
import { createSlice } from '@reduxjs/toolkit'

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: state => { state.value += 1 },
    decrement: state => { state.value -= 1 },
  },
})

export const { increment, decrement } = counterSlice.actions
export default counterSlice.reducer
```
```
// Counter.tsx
import { useSelector, useDispatch } from 'react-redux'
import { increment, decrement } from './counterSlice'

export default function Counter() {
  const count = useSelector((state: any) => state.counter.value)
  const dispatch = useDispatch()

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  )
}
```
✅ 장점
- 가장 안정적이고 성숙한 생태계
- 미들웨어, DevTools, 비동기 처리 등 확장성 강력
- 전역 상태 변경을 명확하게 추적 가능

❌ 단점
- 초기 설정이 복잡하고 코드가 많아짐
- 함수형 사고보단 OOP스럽게 느껴질 수 있음

⸻

## 2. ✅ Zustand

### 특징
- Hook 기반의 상태 관리
- 매우 가볍고 간결한 API
- 불변성 자동 처리 (Immer 내장)

### 사용 예시

```
// store.ts
import { create } from 'zustand'

interface CounterState {
  count: number
  increase: () => void
  decrease: () => void
}

export const useCounterStore = create<CounterState>(set => ({
  count: 0,
  increase: () => set(state => ({ count: state.count + 1 })),
  decrease: () => set(state => ({ count: state.count - 1 })),
}))
```
```
// Counter.tsx
import { useCounterStore } from './store'

export default function Counter() {
  const { count, increase, decrease } = useCounterStore()

  return (
    <div>
      <p>{count}</p>
      <button onClick={increase}>+</button>
      <button onClick={decrease}>-</button>
    </div>
  )
}
```
✅ 장점
- 코드량이 매우 적고 직관적
- Redux보다 러닝 커브가 낮음
- SSR, Refs 등 React 환경과 유연하게 연동됨

❌ 단점
- 공식 비동기 처리 도구가 없음 (직접 처리 필요)
- 상태 변경 추적은 Redux보다 약함

⸻

## 3. ✅ Recoil

### 특징
- Facebook에서 개발한 상태 관리 라이브러리
- atom과 selector로 상태를 정의
- React 전용 라이브러리 (Context 기반)

### 사용 예시
```
// store.ts
import { atom } from 'recoil'

export const counterAtom = atom({
  key: 'counterAtom',
  default: 0,
})
```
```
// Counter.tsx
import { useRecoilState } from 'recoil'
import { counterAtom } from './store'

export default function Counter() {
  const [count, setCount] = useRecoilState(counterAtom)

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  )
}
```
✅ 장점
- 비동기 처리와 파생 상태(selector)가 강력
- 컴포넌트 단위로 세분화된 상태 관리 가능
- Context 구조로 React 앱과 자연스럽게 통합

❌ 단점
- React 외 환경에선 사용 불가
- 상태 디버깅 및 DevTools는 제한적

⸻

### 종합 비교 표

| 항목 | Redux | Zustand | Recoil |
| --- | --- | --- | --- |
| 철학 | Flux 기반 액션/리듀서 구조 | Hook 기반 함수형 접근 | React 전용 원자 단위 상태 |
| 코드량 | 많음 (보일러플레이트 있음) | 매우 적음 | 중간 |
| 학습 난이도 | 중~상 | 하 | 중 |
| 비동기 처리 | 미들웨어 사용 (e.g. thunk, saga) | 직접 처리 (Promise 등) | selector async 지원 |
| DevTools | 매우 강력 | 제공 약함 (middleware로 확장 가능) | 제한적 |
| SSR 지원 | 가능 | 매우 유연함 | 다소 까다로움 |
| 커뮤니티/생태계 | 매우 큼 (성숙) | 점점 성장 중 | 비교적 작음 |
| 대표 사용처 | 대규모 앱, 협업 중심 | 개인/소규모 앱, 빠른 개발 | 파생 상태 많은 앱 (e.g. Form, Graph 구조 등) |


⸻

### 결론
- 간단한 앱, 빠른 개발 → Zustand
- 대규모 프로젝트, 팀 협업 → Redux (Toolkit 사용 권장)
- 비동기 + 파생 상태 많고, React에 밀접한 구조 → Recoil
