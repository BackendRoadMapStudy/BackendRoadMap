### **문제 1:**

싱글톤 방식으로 설계된 객체를 사용할 때, 상태를 유지(stateful)하면 발생할 수 있는 문제는 무엇인가요?

이를 해결하기 위해 객체를 어떻게 설계해야 하나요?

- 정답
    - 상태를 유지(stateful)하게 설계하면 **여러 클라이언트가 공유되는 객체의 상태를 변경**할 수 있어, 예상치 못한 동작이 발생합니다.
    - 이를 해결하기 위해 객체를 무상태(stateless)로 설계해야 하며
        - 특정 클라이언트에 의존적인 필드를 사용하지 않습니다.
        - 값을 변경할 수 있는 필드를 피하고, 가능한 읽기 전용으로 만듭니다.

---

### **문제 2:**

다음 코드에서 `memberRepository()` 메서드가 호출되는 횟수는 몇 번일까요?

그리고 이 횟수와 관련된 스프링의 동작 원리를 설명하세요.

```java
@Configuration
public class AppConfig {
    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }
}

```

- 정답
    - `memberRepository()` 메서드는 **1번**만 호출됩니다.
    - 이유: 스프링은 **싱글톤 컨테이너**로 동작하며, `@Configuration`이 붙은 클래스를 CGLIB 바이트코드 조작으로 감싸 **프록시 객체**를 생성합니다. 이 프록시 객체는 이미 생성된 스프링 빈을 반환하도록 처리합니다.

---

### **문제 3:**

위의 `AppConfig` 클래스에서 `@Configuration`을 제거하고 `@Bean`만 사용하면 어떤 문제가 발생할까요?

스프링은 어떤 방식으로 싱글톤을 보장하며, `@Configuration`의 역할은 무엇인가요?

- 정답
    - `@Configuration` 없이 `@Bean`만 사용하면 스프링이 CGLIB 프록시 객체를 생성하지 않으므로 **싱글톤 보장**이 이루어지지 않습니다.
    - 결과적으로, `memberRepository()` 메서드가 호출될 때마다 새로운 객체가 생성됩니다.
    - 따라서 스프링 설정 정보 클래스에는 항상 `@Configuration`을 붙여야 합니다.

---

### **문제 4:**

다음 중 의존관계 주입 방식에 대한 설명으로 올바른 것은?

1. 생성자 주입은 객체 생성 후 의존관계를 변경할 수 있다.
2. 필드 주입은 외부에서 의존성을 주입하기 어려워 테스트 코드 작성이 용이하다.
3. 수정자 주입은 선택적 의존성이나 변경 가능성이 있는 경우 적합하다.
4. 일반 메서드 주입은 항상 사용하며, 다른 주입 방식보다 권장된다.
- 정답

  3.수정자 주입은 선택적 의존성이나 변경 가능성이 있는 경우 적합하다.

    - 생성자 주입은 의존관계를 변경할 수 없어 불변성을 보장한다.
    - 필드 주입은 외부에서 의존성을 주입하기 어려워 테스트 작성이 어렵다.
    - 일반 메서드 주입은 잘 사용되지 않는다.

---

### **문제 5:**

`@Autowired`를 사용할 때, 의존성 주입이 **자동으로 실패하지 않도록** 설정하는 방법이 아닌 것은?

1. `@Autowired(required = false)`를 사용한다.
2. `@Nullable` 애노테이션을 사용하여 주입할 빈이 없을 경우 `null`로 주입받는다.
3. `Optional`을 사용하여 주입할 빈이 없을 경우 `Optional.empty`를 주입받는다.
4. 빈이 없는 경우 자동으로 빈을 생성하도록 설정한다.
- 정답

  4.빈이 없는 경우 자동으로 빈을 생성하도록 설정한다.

    - `@Autowired(required = false)`, `@Nullable`, 또는 `Optional`을 사용하면 의존성 주입 실패를 방지할 수 있지만, 빈 생성 자체를 자동으로 설정하지는 않습니다.

---