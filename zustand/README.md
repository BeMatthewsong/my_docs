# ☑️ zustand를 사용하는 이유
- Flux 패턴 : 중앙에 집중된 형식의 스토어 구조를 가져 예측가능성이 높다.
- 불필요한 렌더링 최소화
- Recoil이나 Redux보다 더 작은 번들 사이즈
- 러닝 커브가 낮으면서 간결하다

# ☑️ zustand 사용법

## 1. 상태와 상태를 변경할 액션 함수 지정
- create 함수를 통하여 전역 상태와 상태를 바꿀 함수를 저장할 store를 만든다.
```js
import { create } from 'zustand'

const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
```

## 2. 상태 혹은 액션 함수 꺼내기
```js
// 상태(bears) 꺼내기
function BearCounter() {
  const bears = useStore((state) => state.bears)
  return <h1>{bears} around here...</h1>
}

// 액션 함수(increasePopulation ) 꺼내기
function Controls() {
  const increasePopulation = useStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>one up</button>
}
```

## 3. 타입 넣기
- create 함수 뒤에 제네릭 형식으로 타입을 넣어주면 된다.
```js
import { create } from 'zustand'

type State = {
  count: number
}

type Actions = {
  increment: (qty: number) => void
  decrement: (qty: number) => void
}

const useCountStore = create<State & Actions>((set) => ({
  count: 0,
  increment: (qty: number) => set((state) => ({ count: state.count + qty })),
  decrement: (qty: number) => set((state) => ({ count: state.count - qty })),
}))
```

# ☑️ 슬라이스 패턴
슬라이스 패턴이란 하나의 스토어를 여러 작은 스토어들로 분해하여 쪼개는 패턴을 말한다.

왜 슬라이스 패턴을 써야 할까?
- 상태 간의 의존성을 덜 고려할 수 있다
- 일관성과 유지보수에 용이하다

```js
export const createFishSlice = (set) => ({
  fishes: 0,
  addFish: () => set((state) => ({ fishes: state.fishes + 1 })),
})
```
```js
export const createBearSlice = (set) => ({
  bears: 0,
  addBear: () => set((state) => ({ bears: state.bears + 1 })),
  eatFish: () => set((state) => ({ fishes: state.fishes - 1 })),
})
```
```js
import { create } from 'zustand'
import { createBearSlice } from './bearSlice'
import { createFishSlice } from './fishSlice'

export const useBoundStore = create((...a) => ({
  ...createBearSlice(...a),
  ...createFishSlice(...a),
}))
```
# ☑️ zustand는 서버 컴포넌트? 클라이언트 컴포넌트?
zustand는 전역 상태를 가지고 있기 때문에 클라이언트 컴포넌트이다. <br/>
다시 말해, 상태를 가질 수 있는 건 클라이언트 컴포넌트이기 때문에 zustand는 클라이언트 컴포넌트이다.<br/>
zustand를 쓸 때는 `use client`를 사용하자
