# ⚖️ React 상태 관리 라이브러리 비교

## 1. Redux

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

### 추가 예시

```
// store/userSlice.ts
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { getUserInfo, updateUserInfo } from '@/services/userService';

export const fetchUser = createAsyncThunk('user/fetchUser', async () => {
  const response = await getUserInfo();
  return response.data;
});

export const updateUser = createAsyncThunk('user/updateUser', async (newInfo) => {
  const response = await updateUserInfo(newInfo);
  return response.data;
});

const userSlice = createSlice({
  name: 'user',
  initialState: {
    user: null,
    status: 'idle', // 'loading', 'succeeded', 'failed'
    error: null,
  },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.user = action.payload;
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message;
      })
      .addCase(updateUser.fulfilled, (state, action) => {
        state.user = action.payload; // 업데이트 후 최신 정보로 갱신
      });
  },
});

export default userSlice.reducer;

```
- createAsyncThunk : 비동기 요청을 간단하게 처리하게 해주는 Redux Toolkit 함수
  - pending(로딩), fulfilled(성공), rejected(실패)는 Redux Toolkit의 createAsyncThunk()를 사용할 때 비동기 작업의 상태
- initialState: 처음 Redux 상태를 정의. 유저 정보 없고, status는 ‘idle’
- extraReducers: createAsyncThunk에서 만든 비동기 액션들에 대해 각각의 상태를 정의 (예: 로딩 중, 성공, 실패)
  - reducer는 일반 액션 처리(동기), extra는 비동기 액션처리로 이 정도 알면 될 듯
- fetchUser, updateUser는 각각 API 요청을 트리거하고, 응답이 오면 상태를 바꾸는 로직이 여기 있음
  
```
// components/ProfilePage.tsx
import { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchUser, updateUser } from '@/store/userSlice';

export default function ProfilePage() {
  const dispatch = useDispatch();
  const { user, status } = useSelector((state: any) => state.user);

  useEffect(() => {
    dispatch(fetchUser());
  }, [dispatch]);

  const handleUpdate = async () => {
    await dispatch(updateUser({ name: '보성 김', email: 'bosung@email.com' }));
    dispatch(fetchUser()); // 다시 불러와 최신화 시켜주기
  };

  if (status === 'loading') return <div>로딩 중...</div>;
  if (!user) return <div>유저 정보 없음</div>;

  return (
    <div>
      <p>이름: {user.name}</p>
      <p>이메일: {user.email}</p>
      <button onClick={handleUpdate}>정보 수정</button>
    </div>
  );
}
```

useDispatch()
- Redux store에 액션을 발행(dispatch) 하는 함수
- dispatch(fetchUser()) 하면 비동기 요청이 시작됨
  
useSelector()
- Redux 상태 중에서 필요한 데이터(user 등) 를 꺼내옴
- 상태가 바뀌면 컴포넌트가 자동으로 리렌더링 됨

✅ 장점
- 가장 안정적이고 성숙한 생태계
- 미들웨어, DevTools, 비동기 처리 등 확장성 강력
- 전역 상태 변경을 명확하게 추적 가능

❌ 단점
- 초기 설정이 복잡하고 코드가 많아짐
- 함수형 사고보단 OOP스럽게 느껴질 수 있음

⸻

## 2. Zustand

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

### 추가 예시

```
// stores/useUserStore.ts
import { create } from 'zustand'
import { getUserInfo, updateUserInfo } from '@/services/userService'

// 유저가 있다고 가정하고
// 여긴 Store에 인터페이스를 미리 구축
interface UserStore {
  user: User | null
  loading: boolean
  error: string | null

  fetchUser: () => Promise<void>
  updateUser: (newInfo: Partial<User>) => Promise<void>
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  loading: false,
  error: null,

  fetchUser: async () => {
    set({ loading: true, error: null })
    try {
      const res = await getUserInfo()
      set({ user: res.data, loading: false })
    } catch (err: any) {
      set({ error: err.message || '에러 발생', loading: false })
    }
  },

  updateUser: async (newInfo) => {
    set({ loading: true, error: null })
    try {
      const res = await updateUserInfo(newInfo)
      set({ user: res.data, loading: false })
    } catch (err: any) {
      set({ error: err.message || '에러 발생', loading: false })
    }
  },
}))
```

```
// components/UserProfile.tsx
'use client'

import { useUserStore } from '@/stores/useUserStore'
import { useEffect } from 'react'

export default function UserProfile() {
  const { user, loading, error, fetchUser, updateUser } = useUserStore()

  useEffect(() => {
    fetchUser()
  }, [])

  if (loading) return <p>로딩 중...</p>
  if (error) return <p>에러 발생: {error}</p>
  if (!user) return null

  return (
    <div>
      <p>이름: {user.name}</p>
      <p>이메일: {user.email}</p>
      <button onClick={() => updateUser({ name: '홍길동' })}>이름 수정</button>
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

## 3. Recoil

### 특징
- Facebook에서 개발한 상태 관리 라이브러리
- atom과 selector로 상태를 정의
  - atom : 기본 상태 저장소
  - selector : 계산된 값(파생 상태) 또는 비동기 요청용, 여러 atom을 조합하거나 변형할 때 사용
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
이건 selector
```
export const userNameSelector = selector({
  key: 'userNameSelector',
  get: ({ get }) => {
    const user = get(userState);
    return user?.name;
  },
});
```
### 추가 예시
```
// recoil/userAtom.ts
import { atom } from 'recoil';

export const userState = atom({
  key: 'userState',
  default: null, // 최초엔 데이터 없음
});
```
- Recoil의 전역 상태 저장소
- 유저 정보를 null로 초기화함 (앱 시작 시 아무 정보 없음)
- 이후 setUser()로 업데이트 가능

```
// hooks/useUser.ts
import { useRecoilState, useSetRecoilState } from 'recoil';
import { userState } from '@/recoil/userAtom';
import { getUserInfo, updateUserInfo } from '@/services/userService';

export function useUser() {
  const [user, setUser] = useRecoilState(userState);

  const fetchUser = async () => {
    const res = await getUserInfo();
    setUser(res.data);
  };

  const updateUser = async (newInfo) => {
    const res = await updateUserInfo(newInfo);
    setUser(res.data); // 최신 정보로 업데이트
  };

  return { user, fetchUser, updateUser };
}
```
-	useRecoilState(userState) → 상태 읽기 & 쓰기 동시 사용
- fetchUser() → 서버에서 유저 정보 불러와서 setUser()로 전역 상태 갱신
- updateUser() → 서버에 수정 요청 보내고 결과로 다시 상태 갱신
```
// components/UserProfile.tsx
import { useUser } from '@/hooks/useUser';

export default function UserProfile() {
  const { user, fetchUser, updateUser } = useUser();

  if (!user) return <button onClick={fetchUser}>유저 정보 불러오기</button>;

  return (
    <div>
      <p>이름: {user.name}</p>
      <button onClick={() => updateUser({ name: '보성' })}>이름 변경</button>
    </div>
  );
}

```
- user === null이면 아직 로딩 전 → 버튼 눌러서 수동 fetch
- user가 있으면 이름을 보여주고, 버튼으로 이름 변경 요청
- updateUser() 이후 userState가 바뀌므로 자동으로 UI 업데이트됨
  
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
