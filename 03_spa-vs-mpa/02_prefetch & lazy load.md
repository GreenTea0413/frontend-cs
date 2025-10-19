# ⚡️ prefetch & lazy load 전략

## prefetch란?
향후에 사용될 가능성이 있는 리소스를 미리 받아두는 전략
- 사용자가 아직 해당 리소스를 요청하지 않았지만 곧 필요할 것 같다면 브라우저가 미리 다운로드함
- 페이지 전환을 빠르게 만들기 위해 사용됨

⸻

### 햄버거 메뉴 + prefetch 예시 설명

사용자가 햄버거 메뉴에 마우스를 올리거나, 특정 링크 근처로 스크롤만 해도 <br>
브라우저(또는 프레임워크)가 “아, 이 사람이 곧 이 링크를 클릭할 것 같네?” 하고 <br>
해당 페이지의 리소스(JS, HTML, 데이터)를 백그라운드에서 미리 받아놓는 것이 바로 prefetch

### 실제 동작 흐름 예시 (Next.js 기준)

1. 초기 상태
- 사용자는 /home 페이지에 있음
- 햄버거 메뉴 안에 /about, /profile 링크가 있음

2. 사용자가 메뉴를 열거나 링크에 마우스를 올림 (hover 상태)
- Next.js는 해당 링크에 대해 자동으로 prefetch 수행
`<Link href="/about" prefetch={true}>About</Link>`

3. prefetch 동작
- 브라우저가 /about 페이지의 JS chunk를 미리 받아둠
- 사용자가 실제로 클릭하면, 이미 받아놓은 리소스를 즉시 사용해서 빠르게 페이지 전환됨

⸻

### ✅ 장점
- 페이지 전환 시 더 빠르게 응답 가능
- 사용자 경험(UX) 향상

### ❗ 주의점
- 너무 많은 prefetch는 네트워크 자원 낭비로 이어질 수 있음
- 사용자 행동 예측이 중요

⸻

## lazy load란?

실제로 필요한 시점에 리소스를 로드하는 전략
- 초기 로딩 시 모든 리소스를 불러오는 대신, 사용자가 해당 영역에 도달했을 때 리소스를 로드
- 보통 이미지, 컴포넌트, 모듈에 적용

⸻

### 예시: 

`<img src="thumbnail.jpg" loading="lazy" alt="썸네일" />`
- <img> 태그에 loading="lazy"를 추가하면, 뷰포트에 들어오기 전까지 로딩 지연

⸻

### 컴포넌트 Lazy Load (React)

```
import { lazy, Suspense } from "react";

const HeavyComponent = lazy(() => import("./HeavyComponent"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}
```
- lazy() + Suspense를 통해 무거운 컴포넌트를 지연 로딩할 수 있음

⸻

### ✅ 장점
- 초기 로딩 속도 개선
- 불필요한 리소스 낭비 방지

### ❗ 주의점
- 화면 전환 시 로딩 지연이 느껴질 수 있음 → Skeleton UI, 로딩 컴포넌트로 UX 보완 필요

⸻

## preload란?
- 필요한 리소스 자원을 받을 때 우선순위를 부여하여 특정 리소스를 빠르게 로딩
- 보통은 폰트, 이미지, 필수적으로 보여줘야하는 리소스를 불러옴

### ❗ 주의할 점
- 무조건 로딩됨 → 네트워크 부담됨
- 너무 많이 preload하면 성능이 오히려 나빠질 수 있음
  → 꼭 필요한 핵심 리소스에만 사용하는 것이 좋음

### prefetch vs preload vs lazy load

| 전략        | 목적                              | 로딩 시점                       | 사용 대상                  |
|-------------|-----------------------------------|----------------------------------|----------------------------|
| preload     | 필수 리소스 우선 로드              | 페이지 렌더링 전에              | 핵심 폰트, JS, CSS         |
| prefetch    | 앞으로 필요할 리소스 미리 로드     | 브라우저 idle 시간에 미리 로드 | 다음 페이지의 JS 등        |
| lazy load   | 리소스를 나중에 필요할 때 로드     | 실제로 보여질 때               | 이미지, 컴포넌트 등        |



⸻

🔗 참고 자료
- MDN - Resource Hints (prefetch, preload)
- Google Developers - Lazy loading
- Next.js prefetching
