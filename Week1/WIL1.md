# GDSC 기초 백엔드 스터디

## 1주차

---

### MVC 패턴이란?

Model-View-Controller 이렇게 3가지 요소를 묶어 MVC라고 한다. MVC 패턴이란 소프트웨어 개발에서 이 3가지 요소를 활용한 설계 패턴을 의미한다.

1. Model : 데이터와 비즈니스 로직을 의미
2. View : 유저 인터페이스를 의미
3. Controller : Model과 View 사이에서 중간다리 역할을 의미

MVC패턴의 가장 큰 목표는, 역할의 분산에 있다. 서로 다른 요소가 서로에게 영향을 미칠 수 없도록 설계해서, 코드의 유지보수와 테스트하기 쉽게 만드는 것이 MVC 패턴의 가장 큰 목표이다.

### API와 서버? -아무 API나 사용해보길 권장

Application Programming Interface의 약자. 서버와 클라이언트, 혹은 서버와 서버간의 통신하는 방법을 말한다.

객체를 반환하면 JsonConverter로 처리하여 우리가 주로 쓰는 JSON형태로 반환하고, 문자를 반환하면 StringConverter로 처리하여 string 형태로 반환한다.

Spring에서 API는 일반적으로 객체를 반환하는 것을 의미한다.
~~~

### RESTful 이란?

ㅏㅏㅏㅏ