# 🔁 ISR (Incremental Static Regeneration)

## ISR이란?
SSG의 장점(빠른 속도, 정적 파일)은 유지하면서도 변경된 데이터만 백그라운드에서 새로 갱신할 수 있는 렌더링 방식
- Next.js가 제공하는 고유한 하이브리드 렌더링 전략
- 정적으로 생성된 HTML을 일정 주기마다 서버에서 재생성
- 사용자는 항상 빠른 정적 페이지를 보지만 서버는 필요 시 최신 데이터로 자동 업데이트함

⸻

### 작동 흐름

1. 사용자가 페이지 요청 → 기존에 캐시된 정적 HTML 제공
2. 서버는 백그라운드에서 새 데이터 기반으로 페이지를 다시 생성
3. 새 HTML이 완성되면 다음 사용자부터는 최신 버전으로 제공

즉, 사용자는 항상 빠른 응답을 받고 데이터는 자동으로 갱신되는 구조!

⸻

### 예제 코드 (Next.js)
```
// pages/posts/[id].tsx
export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: { post },
    revalidate: 60, // 60초마다 페이지를 백그라운드에서 재생성
  };
}

export async function getStaticPaths() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  const paths = posts.map(post => ({
    params: { id: post.id.toString() },
  }));

  return {
    paths,
    fallback: "blocking",
  };
}
```
- revalidate: 이 시간이 지나면, 다음 요청 시 백그라운드에서 HTML 재생성
- fallback: 빌드 시점에 존재하지 않던 경로도 대응 가능

⸻

### ✅ 장점
- SSG처럼 빠른 응답 속도 제공
- 실시간 데이터 반영 가능 (캐시 갱신 주기 설정)
- CDN 캐싱 유지 + 최신 데이터 갱신 가능 → 유연한 구조
- 전체 사이트를 다시 빌드하지 않고 특정 페이지만 개별 갱신

⸻

### ❗ 단점
- 첫 요청자에게만 오래된 데이터가 보여질 수 있음 (그 후에 갱신됨)
- 데이터 갱신 시점의 통제가 어려움 (시간 단위로만 설정 가능)
- 구현 복잡도 증가 (fallback, revalidate, 캐시 고려 필요)

⸻

### 🧠 실제 사용 사례
- 쇼핑몰 상품 페이지 (가격은 자주 바뀌지만 자주 조회되는 인기 상품)
- 블로그 포스트 (조회는 많지만, 수정은 가끔)
- 뉴스 사이트 (빠른 응답 + 일정 간격의 갱신)

⸻

### 📝 요약
- ISR은 SSG의 속도와 SSR의 유연성을 결합한 방식
- 초기에는 정적 HTML로 빠르게 응답하고, 일정 시간이 지나면 자동으로 백그라운드 재생성
- 실시간성은 낮지만, 빈번한 빌드 없이 데이터 최신화가 가능하다는 점에서 매우 실용적임
