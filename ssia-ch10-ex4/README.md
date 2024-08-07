CORS 설명
==
## 1. Preflight 의 설명
이 사실은 중요하다! CORS가 권한 부여나 CSRF 보호와 비슷한 제한이라고 이해하는 개발자를 많이 만났다. CORS는 제한을 가하기보다 교차 도메인 호출의 엄격한 제약 조건을 완화하도록 도와주는 기능이다. 그리고 제한이 적용돼도 일부 상황에서는 엔드포인트를 호출할 수 있다. 이 동작이 항상 수행되는 것은 아니다. 종종 브라우저는 요청을 허용해야 하는지 테스트하기 위해 먼저 HTTP OPTIONS 방식으로 호출하는 경우가 있다. 이 테스트 요청을 사전(preflight) 요청이라고 한다. 이 요청이 실패하면 브라우저는 원래 요청을 수락하지 않는다.
사전 요청을 할지 결정하는 것은 브라우저의 책임이고 개발자가 이 논리를 구현해야 하는 것은 아니다. 하지만 특정 도메인에 CORS 정책을 지정하지 않았는데도 백엔드에 대해 교차 출처 호출을 볼 때 놀라지 않으려면 사전 요청의 개념을 알아두는 것이 중요하다. 앵귤러나 리액트와 같은 프레임워크로 클라이언트 앱을 개발했을 때 이런 상황이 생길 수 있다. 그럼 10.15에 이 요청 흐름이 나온다.

## 2. CORS 의 설정의 필요성
모든 출처를 허용하면 애플리케이션이 XSS(교차 사이트 스크립팅) 요청에 노출되고 1장에서 논의한 것처럼 결과적으로 DDoS 공격에 취약해질 수 있다. 테스트 환경에서도 모든 출처를 허용하지 않는 것이 바람직하다. 테스트와 운영에 같은 데이터 센터를 이용하는 잘못 정의된 인프라에서 애플리케이션이 실행되는 경우가 있다. 1장에서 논의한 것처럼 보안이 적용되는 모든 계층을 독립적으로 처리하고 인프라가 허용하지 않으므로 애플리케이션에 특정 취약성이 없다고 가정하는 것은 현명하지 않다.
@CrossOrigin으로 엔드포인트가 정의되는 위치에서 직접 규칙을 지정하면 규칙이 투명해지는 장점이 있지만, 코드가 장황해지고 많은 코드를 반복해야 할 수 있다는 단점도 있다. 또한 개발자가 새로 구현한 엔드포인트에 어노테이션을 추가하는 것을 잊어버릴 위험도 있다.

## 3. 구성 클래스로 중앙 집중된 CORS 구성 정의

```java
@Configuration
public class ProjectConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors(c -> {
            CorsConfigurationSource source = request -> {
                CorsConfiguration config = new CorsConfiguration();
                config.setAllowedOrigins(
                    List.of("example.com", "example.org"));
                config.setAllowedMethods(
                    List.of("GET", "POST", "PUT", "DELETE"));
                return config;
            };
            c.configurationSource(source);
        });
        http.csrf().disable();

        http.authorizeRequests()
            .anyRequest().permitAll();
    }
}
```


