테스트 호출 방법
=====
* 별도 Thread에 SecurityContext를 공유(상속)하기 위한 설정
```java
@Configuration
@EnableAsync
public class ProjectConfig {

    @Bean
    public InitializingBean initializingBean() {
        return () -> SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);
    }
}
```
* SecurytContext는 스레드 안전을 지원하지 않으므로 사용에 조심해야 한다.

```java
SecurityContextHolder.MODE_GLOBAL
```

* curl 을 통한 호출
```shell
curl -u user:99ff79f3-**** http://localhost:8080/hello
Hello, user!  
```

* 별도 스레드를 생성하는 경우 DelegationSecurityContextCallable 또는 DelegationSecurityContextCallable<T>를 사용하여 SecurityContext를 전달할 수 있다.
```java
    @GetMapping("/ciao")
    public String ciao() throws Exception {
        Callable<String> task = () -> {
            SecurityContext context = SecurityContextHolder.getContext();
            return context.getAuthentication().getName();
        };

        ExecutorService e = Executors.newCachedThreadPool();
        try {
            var contextTask = new DelegatingSecurityContextCallable<>(task);
            return "Ciao, " + e.submit(contextTask).get() + "!";
        } finally {
            e.shutdown();
        }
    }
```
* 예약된 작업에 SecurityContext를 전파하고자 하는 경우는 DelegatingSecurityContextExecutorService를 이용한다.
```java
    @GetMapping("/hola")
    public String hola() throws Exception {
        Callable<String> task = () -> {
            SecurityContext context = SecurityContextHolder.getContext();
            return context.getAuthentication().getName();
        };

        ExecutorService e = Executors.newCachedThreadPool();
        e = new DelegatingSecurityContextExecutorService(e);
        try {
            return "Hola, " + e.submit(task).get() + "!";
        } finally {
            e.shutdown();
        }
    }
```
