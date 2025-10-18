# 🧱 SSG (Static Site Generation)

## SSG란?
페이지를 빌드 시점(build time)에 HTML 파일로 미리 생성해두고 요청 시에는 서버가 아닌 CDN이나 정적 호스팅을 통해 즉시 제공하는 방식
- JavaScript가 필요 없이 완성된 HTML을 그대로 제공
- 데이터가 자주 바뀌지 않는 페이지에 매우 적합
- Next.js, Gatsby, Hugo 등에서 사용 가능

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
// pages/posts/[id].tsx
export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: { post },
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
    fallback: false,
  };
}

export default function PostPage({ post }) {
  return <div>{post.title}</div>;
}
```
- getStaticProps: 빌드 시 데이터를 가져와 페이지에 넘겨줌
- getStaticPaths: 어떤 동적 경로를 미리 생성할지 정의

⸻

### ✅ 장점
- 매우 빠른 응답 속도 (HTML이 미리 존재)
- 서버 부하 없음 (서버 없이도 배포 가능)
- SEO에 최적화 (크롤러가 정적 콘텐츠 바로 읽을 수 있음)
- CDN을 통한 글로벌 배포 용이

⸻

### ❗ 단점
- 데이터가 자주 바뀌는 페이지에는 부적합
- 빌드 시간이 오래 걸릴 수 있음 (페이지 수가 많을 경우)
- 실시간 요청 반영이 불가 → 업데이트하려면 다시 빌드 필요

⸻

### 🧠 실제 사용 사례
- 회사 소개 페이지, 블로그, 마케팅 랜딩 페이지 등
- 게시물이나 제품 정보가 자주 바뀌지 않는 정적 콘텐츠 중심의 사이트

⸻

### 📝 요약
- SSG는 정적 HTML 파일을 미리 생성해서 빠르게 제공하는 방식
- 속도와 SEO에 매우 유리하지만 실시간 데이터 반영에는 한계가 있음
- 자주 변하지 않는 콘텐츠를 빠르게 전달하고 싶을 때 이상적
