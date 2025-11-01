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
4. 단, 이미 해당 페이지를 보고 있는 사용자는 갱신된 HTML을 받지 않음
	- 같은 URL로 다시 접속하거나 새로고침해야 최신 콘텐츠를 볼 수 있음

즉, 사용자는 항상 빠른 응답을 받고 데이터는 자동으로 갱신되는 구조!

⸻

### 예제 코드 (Next.js)
```
// app/posts/[id]/page.tsx

type Props = {
  params: { id: string }
}

// 선택된 경로들에 대해 미리 HTML 생성
export async function generateStaticParams() {
  const res = await fetch("https://api.example.com/posts")
  const posts = await res.json()

  return posts.map((post: any) => ({
    id: post.id.toString(),
  }))
}

// 60초마다 백그라운드에서 페이지 갱신
export const revalidate = 60

export default async function PostPage({ params }: Props) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`, {
    next: { revalidate: 60 }, // ISR 설정
  })

  if (res.status === 404) {
    return notFound()
  }

  const post = await res.json()

  return <div>{post.title}</div>
}
```
- generateStaticParams()로 일부 경로를 빌드 시 미리 생성
- 그 외 경로는 요청 시 fallback: blocking처럼 처리
- 생성된 HTML은 60초마다 백그라운드에서 자동 갱신

⸻

### 장점
- SSG처럼 빠른 응답 속도 제공
- 실시간 데이터 반영 가능 (캐시 갱신 주기 설정)
- CDN 캐싱 유지 + 최신 데이터 갱신 가능 → 유연한 구조
- 전체 사이트를 다시 빌드하지 않고 특정 페이지만 개별 갱신을 통한 서버 부하 감소

⸻

### 단점
- 첫 요청자에게만 오래된 데이터가 보여질 수 있음 (그 후에 갱신할려면 사용자가 다시 URL을 들어가거나 새로고침해야함)
- 데이터 갱신 시점의 통제가 어려움 (시간 단위로만 설정 가능)
- 구현 복잡도 증가 (fallback, revalidate, 캐시 고려 필요)

⸻

### 실제 사용 사례
- 쇼핑몰 상품 페이지 (가격은 자주 바뀌지만 자주 조회되는 인기 상품)
- 블로그 포스트 (조회는 많지만, 수정은 가끔)
- 뉴스 사이트 (빠른 응답 + 일정 간격의 갱신)
