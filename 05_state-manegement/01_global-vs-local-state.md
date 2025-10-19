# 🧩 전역 상태 vs 지역 상태

## 지역 상태 (Local State)란?

특정 컴포넌트 내부에서만 사용하는 상태
- 컴포넌트의 UI, 입력값, toggle 여부 등
- useState, useReducer 등 훅으로 관리됨
  
```
function InputForm() {
  const [name, setName] = useState('');
  return <input value={name} onChange={(e) => setName(e.target.value)} />;
}
```

⸻

## 전역 상태 (Global State)란?

앱 전역에서 여러 컴포넌트가 공유해야 하는 상태
- 사용자 정보, 테마, 로그인 상태, 장바구니, 다크모드 등
- 상태가 여러 곳에서 읽히고 수정되므로 전역 관리 도구 사용
- 프로젝트할 때는 주로 user 고유 id 저장 또는 한번 불러오면 계속 사용할 수 있는 데이터를 전역으로 사용했음
- 예: Context, Recoil, Zustand, Redux, Jotai 등

```
// 예: Recoil 전역 상태 예시
export const userState = atom({ key: 'userState', default: null });
```

⸻

### 사용 기준 비교
| 구분           | 지역 상태                               | 전역 상태                                           |
|----------------|------------------------------------------|-----------------------------------------------------|
| 접근 범위      | 특정 컴포넌트 내부에서만 사용            | 여러 컴포넌트 또는 페이지 전역에서 공유됨            |
| 사용 도구      | `useState`, `useReducer`                | `Context`, `Redux`, `Recoil`, `Zustand` 등          |
| 사용 예시      | 모달 열림 여부, form 입력값              | 로그인 정보, 다크모드, 유저 설정 등                 |
| 변경 영향      | 해당 컴포넌트만 리렌더링                 | 여러 컴포넌트에 리렌더링 전파 가능성 있음           |


⸻

### 실전 판단 기준

“이 상태는 몇 개의 컴포넌트에서, 어디에서, 얼마나 자주 쓰이는가?”

✅ 지역 상태로 두기 좋은 경우
- 간단한 입력 값 및 한번만 쓰는 경우
- UI 제어용 (모달 on/off, 슬라이더 상태)
- 단일 컴포넌트 내부에 국한
  
✅ 전역 상태로 올리기 좋은 경우
- 여러 컴포넌트가 읽고 수정해야 하는 데이터
- 페이지 간 공유가 필요한 상태 (ex. 로그인 정보, 토큰)
- 해당 데이터를 사용해서 API 호출을 많이 할 경우 (ex. 작성한 글, 고유 자산 등)

⸻

### 요약
- 상태의 위치는 “누가 사용하느냐”에 따라 결정해야 함
- 작은 UI 조작은 지역 상태, 앱 전반의 공통 데이터는 전역 상태
- 너무 빨리 전역으로 끌어올리는 건 복잡성과 성능 이슈의 원인이 될 수 있음

⸻

🔗 참고 자료
- Kent C. Dodds – Global State is not your Enemy
- React Docs – State: A Component’s Memory
- Recoil Docs – Atoms and Selectors
