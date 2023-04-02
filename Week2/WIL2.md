# Week2

# 1. 컨트롤러, 서비스, 리포지토리의 역할

![Untitled](Week2%200ce59af3c5ea4719ae09f350ec76fe95/Untitled.png)

- 컨트롤러(Controller): 웹 MVC의 컨트롤러 역할
- 서비스(Service): 핵심 비즈니스 로직 구현
- 리포지토리(Repository): 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리

강의에서는 데이터 저장소(DB)가 선정되지 않았기 때문에, 리포지토리를 이용해서 인터페이스를 먼저 구성했다.

```java
package practice.Spring.repository;
import practice.Spring.domain.Member;
import java.util.Optional;
import java.util.List;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}
```

```java
package practice.Spring.repository;
import practice.Spring.domain.Member;
import java.util.*;

public class MemoryMemberRepository implements MemberRepository {
		private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }

    public void clearStore() {
        store.clear();
    }

}
```

코드를 작성하면서, 학교에서 객체지향언어를 배웠을 때 클래스를 구현하는 느낌을 받았다. 위의 두 코드가 마치 클래스의 선언부와 구현부를 나눈 것 같은 느낌이 들었다.

이러한 레포지토리 패턴을 사용하여 구현하는 경우 생기는 장점은 아래와 같다.

- **결합도(coupling) 감소**: 데이터 액세스 계층과 비즈니스 로직 계층 사이에 추상화 계층을 제공함으로써 코드의 결합도를 줄일 수 있다. 이렇게 하면 애플리케이션을 유지보수하기 쉬운 형태로 만들 수 있다.
- **테스트 용이성**: 데이터 액세스 코드와 비즈니스 로직 코드를 명확하게 분리함으로써 애플리케이션을 테스트하기 쉬워진다.
- **유연성**: 다른 데이터 저장소 솔루션(예: 데이터베이스, 파일 및 웹 서비스) 간에 전환할 수 있도록 하며 이렇게 하면 애플리케이션의 다른 부분에 영향을 미치지 않는다.
- **표준화**: 데이터 액세스와 조작을 위한 표준화된 방법을 제공함으로써 애플리케이션 코드를 이해하고 유지 관리하기 쉽게 한다.

장점들을 자세히 읽어보면, 레포지토리 패턴은 객체지향에서 캡슐화와 추상화의 개념을 활용하여 데이터를 관리하고, 유지보수성을 높일 수 있는 패턴이라는 것을 알 수 있다.

서비스는 비즈니스 처리적으로 설계, 리포지토리는 개발스러운 용어 선택한다.

ex)서비스에서는 회원가입 메소드를 join, 레포지토리에서는 save라고 표현한다.

# 2. TDD란? 왜 하는가?

테스트 주도 개발(테스트 후 개발 방식)을 말한다.

cmd+shift+t로 해당 controller에서 바로 테스트메소드 만들 수 있다.

Test Method 만드는 팁 : given, when, then 사용해보자.

```java
@Test
    void 회원가입() {
        //given
        Member member = new Member();
        member.setName("spring");

        //when
        Long saveId = memberService.join(member);

        //then
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());
    }
```

모든 테스트는 독립적으로 실행되야 하고, 테스트 순서에 의존관계가 있으면 테스트가 정상적으로 이루어질 수 없을 수 도 있다.

```java
@BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }
@AfterEach
    public void afterEach() {
        memberRepository.clearStore();
    }
```

`@BeforeEach` 는 각 테스트 실행 전에 호출된다.항상 새로운 객체를 생성하고, 의존관계를 새로 맺어주는 역할을 한다.

`@AfterEach` 는 각 테스트가 종료될 때 마다 이 기능을 실행한다. 여기서는 메모리 DB에 저장된 데이터를 삭제한다.
모든 테스트는 `@BeforeEach` 혹은 `@AfterEach` 사용해서 매 테스트 메소드마다 repository의 의존관계를 새로 맺어주거나 초기화시켜서 각 테스트 메소드들을 독립적으로 실행하도록 만들어야 한다.