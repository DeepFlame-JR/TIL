SpringBoot

# SpringBoot
- 스프링 프레임워크(Spring Framework)를 기반으로 한 애플리케이션을 간편하게 개발하고 실행하기 위한 도구
    - 내장된 톰캣 서버를 통해 애플리케이션을 실행
- 특징
    - 간편한 설정: Spring Boot는 자동 구성(Auto Configuration)을 제공하여 개발자가 별도의 설정 파일 작성 없이 기본적인 설정을 자동으로 처리
    - 내장된 서버: 별도의 웹 서버 설치 없이 개발 환경에서 쉽게 애플리케이션을 실행하고 테스트(Tomcat, Jetty, Undertow)
    - Spring eco system: 다양한 스프링 프로젝트와 라이브러리를 쉽게 활용할 수 있으며, 스프링의 주요 기능과 장점을 그대로 사용
    - 자동 구성과 의존성 관리: Spring Boot는 자동 구성 기능을 통해 개발자가 애플리케이션을 구성할 때 필요한 의존성을 자동으로 처리
- 단점
    - 복잡한 설정: Spring Boot는 다양한 설정 옵션을 제공하기 때문에 초기 설정에는 조금의 학습 곡선이 필요
    - 커스터마이징의 어려움: Spring Boot는 기본 설정을 제공하므로 커스텀 설정이 필요한 경우에는 약간의 추가 작업이 필요할 수 있음
    - 높은 의존성: Spring Boot는 다양한 라이브러리와의 통합을 지원하기 때문에 애플리케이션에 사용되는 의존성이 많을 수 있음 (용량 증가, 라이브러리 버전 충돌 문제 발생)

#### Tomcat
- 경량화되고 간단한 설정으로 빠르게 웹 애플리케이션을 실행할 수 있으며, Java 웹 애플리케이션 개발에 널리 사용
- 주요 특징
    - 서블릿 컨테이너
        - 톰캣은 서블릿 스펙을 구현한 서블릿 컨테이너로서 동작
        - 개발자는 자바 서블릿을 사용하여 동적인 웹 애플리케이션을 개발하고 실행
    - JSP 지원
        - 톰캣은 JavaServer Pages(JSP)를 지원하여 웹 페이지의 동적 생성을 용이
        - JSP는 HTML 내에 자바 코드를 포함하여 동적인 콘텐츠를 생성할 수 있게 함
    - 웹 서버와의 연동
        - 톰캣은 웹 서버(Apache HTTP Server 등)와 연동하여 정적 파일 처리나 로드 밸런싱과 같은 기능을 제공
        - 웹 서버는 정적 파일 처리를 담당하고, 동적인 요청은 톰캣으로 전달하여 처리
    - 관리 및 모니터링 기능
        - 톰캣은 웹 기반의 관리 인터페이스를 제공하여 애플리케이션의 설정, 배포, 모니터링 등을 수행
    - 확장성
        - 톰캣은 기본적으로 서블릿 컨테이너로서 동작하지만, 필요에 따라 다양한 확장 모듈을 추가하여 기능을 확장
        - 예를 들어, 데이터베이스 연동을 위한 JDBC 드라이버나 보안을 강화하기 위한 SSL 모듈 등을 추가

## Spring Framework
- Java 기반의 오픈 소스 애플리케이션 프레임워크
    - 엔터프라이즈급 애플리케이션 개발을 위한 다양한 기능과 추상화 계층을 제공하여 개발자가 애플리케이션을 효율적으로 개발할 수 있도록 도움
- 특징
    - 경량성
        - 스프링은 경량적인 프레임워크로, 필요한 기능만 선택하여 사용할 수 있음
        - 불필요한 부분을 배제하고 애플리케이션의 성능과 메모리 사용량을 최적화
    - 제어 역전(IoC) 컨테이너
        - 개발자가 객체의 생성과 관리를 스프링에 위임
        - 객체 간의 의존성을 관리하고 객체의 생명주기를 효과적으로 관리
    - 의존성 주입(DI)
        - 객체 간의 의존 관계를 설정 파일이나 어노테이션을 통해 자동으로 주입
        - 객체 간의 결합도를 낮출 수 있으며, 유연하고 테스트 가능한 애플리케이션을 개발
    - 관점 지향 프로그래밍(AOP)
        - 스프링은 AOP를 지원하여 애플리케이션에서 공통된 관심사를 분리하여 모듈화
        - 핵심 비즈니스 로직과 부가적인 기능(로깅, 트랜잭션 관리 등)을 분리하여 개발 가능
    - 통합 기술
        - 스프링은 다양한 기술과 라이브러리와의 통합을 지원
        - 데이터베이스 연동, 웹 개발, 보안, 메시징 등 다양한 영역에서 필요한 기능을 제공하며, 스프링 생태계의 다른 프로젝트와의 호환성이 우수

### 제어 역전(Inversion of Control, IoC) 컨테이너
- 소프트웨어 컴포넌트의 생명주기를 관리하고 의존성을 해결하는 주체를 개발자에서 컨테이너로 전환하는 개발 패턴
    - 개발자는 컨테이너에게 객체의 생성, 관리, 의존성 주입 등의 제어를 위임
    - 코드의 모듈성, 재사용성, 유지 보수성 등을 개선
- 특징
    - 객체의 생명주기 관리
        - 컨테이너는 객체의 생성, 초기화, 소멸 등의 생명주기를 관리
        - 객체를 생성하고 초기화하는 일에 집중할 필요 없이 컨테이너에게 해당 작업을 위임
    - 의존성 관리
        - 개발자는 객체가 필요로 하는 의존성을 컨테이너에게 알려주고, 컨테이너는 이를 주입
        - 객체 간의 결합도를 낮추고 유연한 구조를 유지
    - 설정 중심
        - 제어 역전 컨테이너는 설정 파일 또는 애노테이션을 통해 객체들의 관리를 설정
        - 개발자는 컨테이너의 설정을 통해 객체를 생성하고 의존성을 주입하는 방식을 조정

### 의존성 주입 (Dependency Injection)
- 코드 내에서 명시적으로 정의하는 대신 외부에서 의존 객체를 주입하는 방식
    - 스프링이 자동으로 해당 타입의 @Bean을 주입
- 장점
    - 코드의 재사용성과 유지보수성이 향상
        - 의존성 주입을 통해 객체 간의 결합도가 낮아지고, 의존 관계를 외부에서 설정
        - 코드를 더 유연하게 구성할 수 있음
    - 결합도 감소
        - 코드의 가독성과 이해도가 향상
        - 객체가 필요로 하는 의존성을 주입받기 때문에 코드의 목적과 역할을 명확하게 이해
    - 테스트 용이성
        - 의존성을 주입하여 테스트할 객체에 모의(mock) 객체를 주입
        - 테스트를 더 쉽게 작성하고 의존성에 의한 문제를 신속하게 발견
- 만약 의존성 주입이 없다면
```java
public class BugService {
    public void countLeg(){
        BugRepository bug = new Fly(); // bug에 다른 값을 넣고 싶으면 코드를 변경해야 함
        bug.legCount();
    }
}

---

public class BugService {
    private BugRepository bugRepository;

    public BugService(BugRepository bugRepository){
        this.bugRepository = bugRepository;
    }

    public void countLeg(){
        bugRepository.legCount();
    }
}

---

@Test
public void testLeg(){
    BugRepository bug = new LadyBug(); // Fly(), Mantis()... 등등
    BugService bugService = new BugService(bug); //bugService의 생성자로 의존성주입!
    bugService.countLeg();
}
```

- 의존성 주입 방법
```java
// 생성자 주입
public class UserService {
    private UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    // ...
}
 
---

// 필드 주입
// 의존성 숨김: 객체 생성 시점에 의존성을 명확하게 표현하지 않기 때문에, 코드를 읽거나 디버깅하기 어려울 수 있음
// 테스트 어려움: 테스트 시에 의존성 주입을 직접적으로 제어하기 어려움
// 순환 참조 가능성: 객체 간의 상호참조가 계속해서 이어지는 상황을 말하며, 메모리 누수와 같은 심각한 문제를 야기
// 의존성 변경의 어려움: 필드 주입은 객체의 일관성을 보장하지 않을 수 있으며, 의존성이 변경되는 경우 객체 상태를 변경할 수 있는 위험성
public class UserService {
    @Autowired
    private UserRepository userRepository;
    // ...
}
```

#### Singleton Pattern
- 객체를 하나만 생성해서 메모리 사용을 최소화, 같은 처리를 하나의 객체에 처리하게 하여 동시성 처리나 동작의 편의성 제공
    - 스프링 프레임 워크의 특징 중 하나
```java
public class Test{

    public static Test INSTANCE;

    public Test getInstance(){
        if(INSTANCE == null){
            INSTANCE = new Test();
        }
        return INSTANCE;
    }
}
```

### AOP(Aspect-Oriented Programming)
- 관점 지향 프로그래밍을 의미
    - OOP (Object-Oriented Programming)의 보완적인 개념으로서, 관심사의 분리를 위해 사용
        - 관심사란 애플리케이션의 기능을 구현하기 위해 필요한 여러 가지 요소
    - AOP는 코드의 중복을 줄이고, 관심사의 분리와 모듈화를 통해 유지보수성과 재사용성을 향상
- 주요 개념
    - Aspect (관점)
        - 애플리케이션에서 분리된 관심사
        - 예를 들어, 로깅이나 보안과 같은 관심사는 애플리케이션 전체에서 반복적으로 적용
    - Join Point (결합 지점)
        - 애플리케이션 실행 중 특정 시점에서의 메서드 호출이나 예외 발생 
        - AOP는 이러한 결합 지점을 식별하여 관심사를 적용합니다.
    - Advice (조언)
        - 관심사가 결합 지점에서 실행되는 시점
        - 예를 들어, 메서드 실행 전, 후, 예외 발생 시 등의 시점에서 관심사를 실행
    - Pointcut (적용 지점)
        - 어떤 결합 지점에 Aspect를 적용할지를 지정하는 표현식
        - 특정한 조건을 만족하는 결합 지점을 선택하여 Aspect를 적용할


## 스프링 부트 프로젝트 구성
1. 의존성 관리
    - 스프링 부트는 Maven이나 Gradle과 같은 빌드 도구를 사용하여 의존성을 관리
        - Maven: 설정 파일에 의존성 및 빌드 설정, XML, 범용적으로 쓰임
        - Gradle: 유연하고 간결한 구조, 학습 곡선이 높음, 생태계가 상대적으로 부족
    - pom.xml (Maven) 또는 build.gradle (Gradle) 파일에서 필요한 의존성을 정의하고 관리
1. 애플리케이션 클래스
    - @SpringBootApplication 어노테이션이 지정된 클래스를 애플리케이션의 시작점으로 인식
1. 구성 파일
    - 스프링 부트는 애플리케이션의 구성을 위해 application.properties 또는 application.yml과 같은 구성 파일을 지원
    - 애플리케이션의 설정 옵션을 정의하고 관리하는데 사용
1. 컨트롤러
    - 스프링 부트 애플리케이션은 RESTful API 엔드포인트를 제공하기 위해 컨트롤러
    - @RestController 어노테이션을 사용하여 정의되며, 각 엔드포인트에 대한 요청과 응답을 처리
1. 서비스
    - 서비스 계층은 비즈니스 로직을 구현하는데 사용
    - @Service 어노테이션을 사용하여 서비스 클래스를 정의
1. 데이터 액세스 계층
    - 데이터베이스와의 상호작용을 위한 데이터 액세스 계층
    - JPA, JDBC, Spring Data JPA 등 다양한 데이터 액세스 기술
1. 테스트
    - JUnit, Mockito 등의 테스트 프레임워크를 통해 단위 테스트와 통합 테스트를 지원
    - 테스트 클래스는 @RunWith(SpringRunner.class) 어노테이션과 @SpringBootTest 어노테이션을 사용하여 스프링 부트 애플리케이션을 로드하고 테스트를 수행


### 자동 구성
- 자동 구성(Auto-configuration)은 애플리케이션의 설정을 간편하게 만들어주는 기능
    - 기본적으로 Spring Boot는 여러 개의 Starter 모듈을 제공하여 각종 기능들의 자동 구성을 제공
- 원칙
    - Classpath Scanning
        - Spring Boot는 클래스패스를 스캔하여 사용할 수 있는 라이브러리나 구성 클래스를 찾음
        - @Configuration 어노테이션이 적용된 클래스를 작성하여 원하는 구성을 수동으로 정의 가능
            - @EnableAutoConfiguration 어노테이션으로 자동 구성을 활성화
            - @Bean을 통해서 @Configuration의 메서드를 빈으로 등록 
                - 빈으로 등록하면 빈이 필요한 곳에서 재사용 가능
        - 예를 들어, H2 데이터베이스가 클래스패스에 존재하면 자동으로 H2 데이터베이스에 대한 구성을 제공
    - 조건부 설정
        - Spring Boot는 자동 구성을 할 때 조건을 검사하여 필요한 경우에만 해당 구성을 적용
        - @ConditionalOnProperty, @ConditionalOnClass, @ConditionalOnBean 등의 조건 어노테이션을 사용하여 특정 조건이 충족될 때만 자동 구성을 활성화
        - 예를 들어, DataSource 구성이 필요한 경우에는 클래스패스에 데이터베이스 드라이버가 존재해야만 자동 구성
    - 프로퍼티 설정
        - application.properties 또는 application.yml 파일에 작성된 설정을 읽어와 자동 구성에 사용
        - 예를 들어, 데이터베이스 연결 정보를 프로퍼티로 설정하면 자동 구성이 이 정보를 활용하여 데이터베이스 연결을 설정

### 데이터베이스 연동
- 다양한 데이터베이스와의 연동을 간편하게 처리할 수 있는 기능을 제공
    - application.properties 또는 application.yml 파일을 사용하여 데이터베이스 연결 정보, DDL 설정, 풀링 설정 등을 지정
- 종류
    1. JDBC (Java Database Connectivity)
        - Spring Boot는 JDBC를 사용하여 데이터베이스에 접속하고 SQL 쿼리를 실행
        - HikariCP와 같은 커넥션 풀을 제공하여 효율적인 데이터베이스 연결 관리를 지원
    1. ORM (Object-Relational Mapping)
        - Spring Boot는 JPA (Java Persistence API)를 기반으로 한 ORM 기술을 지원
            - JPA를 사용하면 객체와 데이터베이스 테이블 간의 매핑을 자동으로 처리
            - 대표적인 JPA 구현체로는 Hibernate, EclipseLink 등
    1. Spring Data JPA
        - Spring Data JPA를 통해 JPA를 더욱 편리하게 사용할 수 있도록 지원
        - Spring Data JPA는 개발자가 반복적인 CRUD (Create, Read, Update, Delete) 작업을 간단한 인터페이스로 처리
        - 쿼리 메소드, 동적 쿼리, 네이티브 쿼리 등 다양한 쿼리 기능도 제공
    1. NoSQL 데이터베이스
        - NoSQL 데이터베이스와의 연동도 지원
        - MongoDB, Redis, Elasticsearch 등 다양한 NoSQL 데이터베이스와의 통합을 간편하게 처리

#### DB 형상 관리
- 데이터베이스 스키마 버전 관리와 마이그레이션을 지원
- 종류
    1. Flyway
        - Flyway는 경량의 오픈 소스 데이터베이스 마이그레이션 도구
        - SQL 기반의 마이그레이션 스크립트를 작성하고, 버전 관리를 통해 스키마 변경을 관리
        - 마이그레이션 스크립트의 실행 순서는 버전 순으로 관리됩니다.
    1. Liquibase
        - XML 또는 YAML 형식의 마이그레이션 스크립트를 작성하고, 버전 관리를 통해 스키마 변경을 관리
        - 마이그레이션 스크립트의 실행 순서는 변경 순서에 의해 관리

### 테스트
- 애플리케이션의 단위 테스트, 통합 테스트, 애플리케이션 테스트 등을 쉽게 작성
- 종류
    1. 단위 테스트(Unit Test)
        - 애플리케이션의 개별적인 컴포넌트(클래스, 메서드 등)를 격리하여 테스트
        - JUnit 또는 TestNG과 같은 단위 테스트 프레임워크를 사용하여 단위 테스트를 작성
        - 테스트할 컴포넌트를 모킹(Mocking) 또는 스텁(Stub)으로 대체하여 의존성을 관리
    1. 통합 테스트(Integration Test)
        - 애플리케이션의 여러 컴포넌트가 상호 작용하는 과정을 테스트
        - 스프링 부트에서는 스프링의 @SpringBootTest 애노테이션을 사용하여 통합 테스트를 작성
        - 테스트 컨텍스트가 생성되고, 실제 환경과 유사한 설정으로 애플리케이션을 실행하여 테스트를 수행
        - 테스트용 데이터베이스, 외부 API와의 통신 등을 테스트
    1. MVC 테스트
        - 스프링 부트는 웹 애플리케이션의 컨트롤러 계층을 테스트하기 위한 MVC 테스트 기능을 제공
        - @WebMvcTest 애노테이션을 사용하여 웹 애플리케이션의 특정 컨트롤러를 테스트
        - HTTP 요청과 응답을 시뮬레이션하여 컨트롤러의 동작을 테스트하고, 모델의 상태나 뷰의 결과를 검증
    1. 테스트 데이터베이스
        - 스프링 부트는 테스트용 데이터베이스를 쉽게 구성할 수 있는 기능을 제공
        - @DataJpaTest 애노테이션을 사용하여 테스트용 데이터베이스를 설정


#### Mocking과 Stub
- Mocking
    - 메서드 호출이나 상호작용을 모방하여 테스트 시나리오를 구축
    - 장점
        - 특정 메서드의 반환 값을 조작하거나 예외를 발생시켜 원하는 동작을 정확히 시뮬레이션
        - 테스트 코드가 빠르고 가벼움
        - 특정 상황을 시뮬레이션하여 테스트 (네트워크 연결이 끊긴 상황이나 예외 상황을 강제로 발생시켜 테스트)
    - 단점
        - 실제 의존성과의 동작 차이가 있을 수 있음
        - 모의 객체를 작성하는 데 시간과 노력이 필요
    ```java
    @Test
    public void testCreateUser() {
        // Mock 객체 생성
        UserRepository userRepository = Mockito.mock(UserRepository.class);
        // Mock 객체를 UserService에 주입
        UserService userService = new UserService(userRepository);
        
        // UserRepository의 save 메서드가 호출될 때 예상되는 동작 정의
        Mockito.when(userRepository.save(Mockito.any(User.class))).thenReturn(true);
        boolean result = userService.createUser(new User("john", "password"));
        
        // 결과 검증
        assertTrue(result);
        // UserRepository의 save 메서드가 1번 호출되었는지 검증
        Mockito.verify(userRepository, Mockito.times(1)).save(Mockito.any(User.class));
    }
    ```
- Stub
    - 실제 객체의 동작 대신 특정 값을 반환하는 가짜 객체
    - 장점
        - 테스트에 필요한 값을 쉽게 제공
        - 반환 값을 원하는 값으로 미리 설정하여 테스트
    - 단점
        - Stub은 실제 의존성과 비교해보지 않으므로 실제 동작과의 일치성을 보장하기 어려움
    ```java
    @Test
    public void testGetUserInfo() {
        // Stub 객체 생성
        ExternalApiStub externalApiStub = new ExternalApiStub();
        // UserService에 외부 API Stub 주입
        UserService userService = new UserService(externalApiStub);
        
        // 외부 API의 응답 값을 Stub으로 설정
        externalApiStub.setUserInfo("john", new UserInfo("John Doe", 25));
        UserInfo userInfo = userService.getUserInfo("john");
        
        // 결과 검증
        assertEquals("John Doe", userInfo.getName());
        assertEquals(25, userInfo.getAge());
    }

    // 외부 API Stub 클래스
    public class ExternalApiStub implements ExternalApi {
        private Map<String, UserInfo> userInfoMap = new HashMap<>();
        
        public void setUserInfo(String userId, UserInfo userInfo) {
            userInfoMap.put(userId, userInfo);
        }
        
        @Override
        public UserInfo getUserInfo(String userId) {
            return userInfoMap.get(userId);
        }
    }
    ```