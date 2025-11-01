# 🧱 SSG (Static Site Generation)

## SSG란? (정적 사이트 생성)
페이지를 빌드 시점(build time)에 HTML 파일로 미리 생성해두고 요청 시에는 서버가 아닌 CDN이나 정적 호스팅을 통해 즉시 제공하는 방식
- 사용자가 웹 사이트 접속 시 브라우저는 완성된 HTML을 그대로 제공
- 추가 페이지 요청 시 미리 생성해 둔 해당 페이지의 HTML을 내보냄
- 데이터가 자주 바뀌지 않는 페이지에 매우 적합
- Next.js, Gatsby, Hugo 등에서 사용 가능

### CDN
- 분산된 서버 네트워크
- 사용자에게 더 빠르게 콘텐츠 제공
  - ex) 한국 사용자가 미국에 있는 서버에서 사이트를 열면 거리만큼 느려짐
  - 그래서 CDN은 한국, 일본, 싱가포르 등 아시아 지역에도 서버가 있어서 가장 가까운 서버에서 콘텐츠를 전달함
 
### 정적 호스팅
- 정적 호스팅은 정적인 파일(HTML, CSS, JS 등)을 직접 서빙하는 웹 호스팅 방식
- 백엔드 로직이 따로 필요 없어서 HTML, CSS, JS 파일을 그대로 저장해 두고, URL 요청 시 그냥 바로 제공함

### 간단하게 보면
- 정적 호스팅은 정적 파일을 저장하고 제공하는 공간
- CDN은 그 파일을 더 빠르게 전 세계에 퍼뜨려주는 시스템
- 실제로는 정적 호스팅 + CDN을 같이 씀

  
⸻

### 작동 흐름
```
프로젝트 빌드 시점 → 모든 페이지의 HTML 생성 → 정적 파일로 저장
↓
브라우저가 요청 → 미리 생성된 HTML을 그대로 응답
```
- 서버는 실시간으로 HTML을 생성하지 않고 이미 생성된 정적 파일만 전달
- 대부분 CDN에 캐시되어 있어 응답 속도가 매우 빠름

⸻

### 예제 코드 (Next.js)
```
// app/posts/[id]/page.tsx

type Props = {
  params: { id: string }
}

export async function generateStaticParams() {
  const res = await fetch("https://api.example.com/posts")
  const posts = await res.json()

  return posts.map((post: any) => ({
    id: post.id.toString(),
  }))
}

export default async function PostPage({ params }: Props) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`, {
    cache: 'force-cache', // 기본값 → SSG
  })
  const post = await res.json()

  return <div>{post.title}</div>
}
```
- Next.js App Router에서 /posts/[id] 형태의 동적 경로를 정적으로 생성하는 SSG 구조
- generateStaticParams()를 통해 어떤 게시글 ID를 미리 빌드할지 정함
- page.tsx에서 해당 ID에 대한 데이터를 fetch해서 정적 HTML을 생성
- 그 후 사용자가 해당 페이지 요청 시 그대로 정적 HTML을 브라우저에서 보여줌

⸻

### 장점
- 매우 빠른 응답 속도 (HTML이 미리 존재)
- 서버 부하 없음 (서버 없이도 배포 가능)
- SEO에 최적화 (크롤러가 정적 콘텐츠 바로 읽을 수 있음)
- CDN을 통한 글로벌 배포 용이

⸻

### 단점
- 데이터가 자주 바뀌는 페이지에는 부적합
- 빌드 시간이 오래 걸릴 수 있음 (페이지 수가 많을 경우)
- 실시간 요청 반영이 불가 → 업데이트하려면 다시 빌드 필요

⸻

### 실제 사용 사례
- 회사 소개 페이지, 블로그, 마케팅 랜딩 페이지 등
- 게시물이나 제품 정보가 자주 바뀌지 않는 정적 콘텐츠 중심의 사이트
