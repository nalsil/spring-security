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

