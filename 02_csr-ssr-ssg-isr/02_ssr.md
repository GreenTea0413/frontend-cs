# ⚙️ SSR (Server-Side Rendering)

## SSR이란?
사용자의 요청이 들어올 때마다 서버에서 HTML을 실시간으로 생성해서 브라우저에 전달하는 렌더링 방식
- 브라우저가 요청하면 서버는 미리 데이터를 가져와 완성된 HTML을 생성해 응답
- 브라우저는 HTML을 바로 렌더링 → 초기 화면 표시 속도가 빠름
- 이후 자바스크립트를 로드해 클라이언트에서도 동작 가능하게 함 (hydration)
	- hydration?
   		- 서버에서 생성된 정적인 HTML에 자바스크립트 기능을 연결(활성화)하는 과정
     	- 빠르게 HTML을 보여주긴 하지만 브라우저에서 Hydration이 완료되어야 실제로 클릭, 입력 등의 인터랙션이 가능해짐

⸻

### 작동 흐름
```
브라우저 → HTML 요청 → 서버가 데이터 fetch 후 HTML 생성 → 브라우저에 응답
↓
브라우저는 완성된 HTML 렌더링 → JS 파일 실행 → 인터랙션 가능 상태로 전환(hydrate)

React 기반 프레임워크인 Next.js에서 대표적으로 SSR을 사용
```
⸻

### 예제 코드 (Next.js)
```
// pages/index.tsx
export async function getServerSideProps() {
  const res = await fetch("https://api.example.com/data");
  const data = await res.json();

  return {
    props: { data },
  };
}

export default function Home({ data }) {
  return <div>{data.message}</div>;
}
```
- getServerSideProps 함수가 요청마다 실행되어 서버에서 HTML을 생성함

⸻

### ✅ 장점
- 초기 렌더링 속도 빠름 → 사용자가 빠르게 콘텐츠 확인 가능
- SEO에 매우 유리 → HTML에 콘텐츠가 포함되어 검색 엔진이 쉽게 인식 가능
- 페이지 단위로 데이터 요청 및 렌더링이 가능

⸻

### ❗ 단점
- 요청마다 서버 연산 발생 → 트래픽이 많을수록 서버에 부하가 큼
- 서버가 응답을 준비할 때까지 기다려야 하므로 초기 응답까지 시간이 걸릴 수 있음
- 복잡한 캐싱 전략 필요

⸻

### 🧠 실제 사용 사례
- 검색엔진 최적화(SEO)가 중요한 마케팅/블로그/뉴스 사이트
- 로그인 여부에 따라 초기 데이터가 달라지는 서비스
- 예: Next.js 기반 기업 홈페이지, 블로그 플랫폼

⸻

### 📝 요약
	•	SSR은 서버가 HTML을 완성해 보내주는 방식으로 빠른 초기 렌더링과 SEO에 유리!
	•	하지만 서버 부하와 속도 저하를 고려해야 하며 적절한 캐싱이 중요함
	•	실시간 데이터와 SEO가 모두 중요한 서비스에 적합함

⸻
### SSR에서 사용되는 복잡한 캐싱 전략들

1. HTTP 캐싱 (브라우저 & CDN 레벨)
- Cache-Control, ETag, Last-Modified 등의 HTTP 헤더를 설정해 브라우저 또는 CDN이 응답을 캐싱하도록 유도
- 정적 리소스뿐 아니라 SSR 응답도 특정 조건 하에 캐시 가능

```
Cache-Control: public, max-age=60
ETag: "abc123"
```
max-age=60 → 60초 동안은 클라이언트가 서버에 요청하지 않고 캐시된 응답 사용
CDN을 활용하면 글로벌하게 빠른 응답도 가능

⸻

2. 서버 메모리 캐싱 (in-memory)
- 서버 내부에서 특정 데이터나 HTML 결과를 메모리에 저장
- 대표 예: Node.js의 LRU 캐시, lru-cache, node-cache 등
```
const cache = new Map();

app.get("/page", async (req, res) => {
  if (cache.has("page-html")) {
    return res.send(cache.get("page-html"));
  }

  const data = await fetchData();
  const html = renderToHtml(data);

  cache.set("page-html", html);
  res.send(html);
});
```
단점: 서버 재시작 시 캐시가 모두 날아감

⸻

3. Redis 등 외부 캐시 스토리지 사용
- 캐시를 서버 외부 저장소(ex. Redis)에 저장하면 다중 서버에서도 공유 가능
- 캐시 만료 조건을 세밀하게 설정 가능 (예: TTL, 조건부 갱신)
```
const cached = await redis.get("page:123");
if (cached) return res.send(cached);

// 없으면 생성 후 저장
const html = renderToHtml(data);
await redis.set("page:123", html, { EX: 60 });
```

⸻

4. ISR처럼 백그라운드 재생성 (Stale-While-Revalidate)
- Next.js ISR 방식처럼 먼저 캐시된 HTML을 응답하고 백그라운드에서 최신 데이터를 가져와 캐시를 갱신하는 방식
- 사용자는 빠른 응답을 받고 서버는 최신 버전을 준비해 다음 요청에 사용

⸻

5. 캐시 계층화 전략 (Multi-tier caching)
- 클라이언트 캐시 (브라우저)
- CDN 캐시 (Cloudflare, Akamai 등)
- 서버 캐시 (Memory, Redis 등)
- DB 캐시 (Query 결과를 저장)

이런 식으로 각 계층에서 효율적인 캐싱을 적용하는 게 SSR 성능 최적화의 핵심!

⸻

### 상황별 전략 예시

| 상황                             | 추천 캐싱 방식                                             |
|----------------------------------|------------------------------------------------------------|
| 트래픽이 많은 인기 페이지        | CDN + Redis                                                |
| 사용자별 데이터가 포함된 페이지   | Redis (세션 기반 키) 또는 짧은 TTL 설정                   |
| 실시간성이 중요한 뉴스 페이지   | SWR (Stale-While-Revalidate)                              |
| 변하지 않는 정보                 | 정적 HTML로 프리렌더링 (SSG로 대체하는 것도 고려 가능)   |


⸻

### 요약
- SSR은 매 요청마다 렌더링되므로 캐싱 없이는 비효율적
- 단순히 “메모리에 저장”하는 수준이 아니라 “언제, 어디에, 얼마나 오래 저장할지”에 대한 전략이 중요
- 실무에서는 Redis, CDN, ISR 등의 조합으로 계층 캐싱을 활용

