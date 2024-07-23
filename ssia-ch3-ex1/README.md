GrantedAuthority 계약
===

```java
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;

GrantedAuthority g1 = () -> "READ";
GrantedAuthority g2 = new SimpleGrantedAuthority("READ");
```

* 빌더를 이용해 UserDetails 형식의 인스턴스 만들기

```java
import org.springframework.security.core.userdetails.User;

UserDetails u = User.withUsername("bill")
        .password("12345")
        .authorities("read", "write")
        .accountExpired(false)
        .disabled(true)
        .build();
```
* User.UserBuilder인스턴스 만들기

```java
import org.springframework.security.core.userdetails.User;

User.UserBuilder builder1 = User.withUsername("bill");  // 주어진 사용자 이름으로 사용자 생성

UserDetails u1 = builder1
        .password("12345")
        .authorities("read", "write")
        .passwordEncoder(p -> encode(p)) // 암호 인코더는 인코딩을 수행하는 함수에 불과하다.
        .accountExpired(false)
        .disabled(true)
        .build();  // 빌더 파이크라인의 끝에서 build() 메서드를 호출한다.

User.UserBuilder builder2 = User.withUserDetails(u);  // 기존의 UserDetails 인스턴스에서 사용자를 만들 수도 있다.

UserDetails u2 = builder2. build();


```
