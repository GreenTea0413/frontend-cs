# 보안 헤더 (CORS, CSP 등)

웹 애플리케이션에서 보안 수준을 강화하기 위해 HTTP 응답에 포함시킬 수 있는 헤더들이 존재<br>
이 중 CORS와 CSP는 보안상 특히 중요한 역할!


## CORS (Cross-Origin Resource Sharing)란?

다른 출처(Origin) 간의 리소스 공유를 제어하기 위한 메커니즘<br>
브라우저는 보안상의 이유로 기본적으로 출처가 다른 요청을 차단함<br>
프로젝트를 하게 되면 백엔드와의 첫 연결 당시 많이 봤던 오류
- 서버에서 해결 해주는 방식
- 프론트에서는 proxy라는 방식이 있음
- 예: `https://example.com`에서 `https://api.another.com`의 데이터를 요청하려고 할 때

### 언제 발생하는가?

프론트: `http://localhost:3000`<br>
백엔드: `http://localhost:8080`<br>


프론트에서 백엔드로 요청을 보냄 -> `fetch('http://localhost:8080/api/user')`
```
Access to fetch at 'http://localhost:8080/api/user' from origin 'http://localhost:3000' has been blocked by CORS policy:
No 'Access-Control-Allow-Origin' header is present on the requested resource.
```
그러면 console창에서  오류를 볼 수 있을 거임<br>


백엔드 (Spring Boot)에서는 아래와 같은 설정 필요
- 개별 API마다 적용
```
@CrossOrigin(origins = "http://localhost:3000")
@RestController
public class MyController { ... }
```
- 전역으로 설정하는 방법 (config)
```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**") // 모든 경로
                .allowedOrigins("http://localhost:3000") // 허용할 Origin
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                .allowCredentials(true) // 쿠키 포함 허용 시
                .allowedHeaders("*")
                .maxAge(3600); // preflight 요청 캐싱 시간 (초)
    }
}
```


프론트에서는 개발용 proxy 사용 (개발 중 임시 방편)<br>
CRA (Create React App) 또는 Vite 프로젝트에서는 proxy 옵션으로 프록시를 설정해 요청을 같은 출처처럼 보이게 할 수 있음
```
// package.json
"proxy": "http://localhost:8080"
```

`fetch('/api/user') // → http://localhost:8080/api/user로 프록시 처리됨`

하지만 배포된 상황에서는 되지않으니 테스트 위주로만 사용할 것


⸻

핵심 헤더
```
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Credentials: true
```

주의사항
- 프론트엔드에서 withCredentials: true 사용 시, 서버도 반드시 Access-Control-Allow-Credentials: true와 함께 설정 필요
- *와 credentials을 함께 쓰면 오류 발생

⸻

## CSP (Content Security Policy)란?

XSS, 인젝션 등의 공격을 방지하기 위한 보안 정책 헤더<br>
허용된 스크립트, 스타일, 이미지, 외부 리소스 출처를 명시하여 제한된 출처 외 차단

예시
```
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.cdn.com
```
위 설정은 자기 자신의 도메인과 trusted.cdn.com에서만 스크립트 로딩 허용

주의사항
- 개발 시에는 report-only로 시작해서 테스트 후 강제 적용
- inline script (<script>alert(1)</script>)는 'unsafe-inline'을 명시해야 허용됨 (권장 ❌)

⸻

### 보안 헤더 정리 표

| 헤더 | 목적 | 주요 설정 값 | 보안 효과 |
| --- | --- | --- | --- |
| Access-Control-Allow-Origin | 교차 출처 요청 허용 | `https://example.com`, * | CORS 정책 제어 |
| Access-Control-Allow-Credentials | 쿠키/세션 공유 여부 | true / false | 인증 요청 허용 여부 |
| Content-Security-Policy | 리소스 로딩 출처 제한 | default-src, script-src 등 | XSS/인젝션 차단 |
| X-Content-Type-Options | 콘텐츠 타입 MIME 강제 지정 | nosniff | MIME 타입 위장 방지 |
| X-Frame-Options | iframe 삽입 제한 | DENY, SAMEORIGIN | 클릭재킹 방지 |
