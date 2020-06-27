# Spring Framework 정리 자료
스프링 petclinic 샘플 프로젝트와 인프런 백기선 강사님 예제로 배우는 스프링 입문(개정판) 정리 문서

### IOC(Inversion Of Control)
- 제어의 역전    
    - 보통 아래와 같이 자신의 제어권을 자기 자신이 가짐  
    ```java
    class OwnerController {
      private OwnerRepository repository = new OwnerRepossitory();
    }
    ```  
    - 제어의 역전은 아래와 같이 제어권이 자기 자신에 있지 않고 외부에 있는 것을 말함  
    - 실제 의존성 주입은 스프링(ApplicationContext)가 해줌  
    - 테스트 코드를 작성하기가 훨씬 쉬움  
    ```java
    class OwnerController {
      private OwnerRepository repo;

      public OwnerController(OwnerRepository repo) {
        this.repo = repo;
      }
    }

    class OwnerControllerTest {
      @Test
      public void create() {
        OwnerRepository repo = new OwnerRepository();
        OwnerController controller = new OwnerController(repo);
      }
    }
    ```


### @MockBean
- `@MockBean`
  - 애노테이션으로 가짜 OwnerRepository 객체를 주입 받아서 사용할 수 있음


### IOC(Inversion Of Container) 컨테이너
- IOC 컨테이너
  - 직접 쓸 일이 거의 없음
  - Bean을 만들고 Bean 사이의 의존성을 엮어주고 만들어진 Bean들을 제공해주는 일을 함  
  - BeanFactory
  - ApplicationContext: 자기가 컨테이너 내부에 만든객체들 Bean들의 의존성을 관리해주는 역할을 하는 BeanFactory의 서브 클래스로 우리가 사용하는 IOC 컨테이너


### 빈(Bean)
- 빈(Bean): 스프링 IoC 컨테이너가 관리하는 객체로 ApplicationContext를 통해서 객체를 받와야만 Bean
- 빈으로 등록하는 방법
  - `Component Scanning`을 아용: @Repository, @Service, @Controller, @Configuration 등등을 Annotation으로 하여 등록 
  - `Bean`으로 직접 등록: Java 설정 파일을 통해서 그 안에 `@Bean`이라는 Annotation으로 등록
- 빈을 얻는 방법
  - `@Autowired`을 사용하여 의존성 주입하는 방법으로 얻기
  - `ApplicationContext`을 활용해서 얻기


### 의존성 주입
- 의존성: 현재 객체가 다른 객체와 상호작용(참조)하고 있다면 현재 객체는 다른 객체에 의존성을 가진다고 함
- 의존성을 사용자가 제어할 경우 모듈이 바뀌면 의존한 다른 모듈을 변경하는 문제를 일일히 처리해야함
- 의존성 주입: 프레임워크에 의해 객체의 의존성을 주입하여 동적으로 주입하여 객체 간의 결합을 줄임
  - 생성자, 세터, 필드에 `@Autowired`를 이용해 빈을 꺼내와서 의존성을 주입해줄 수 있음(주로 생성자 사용)
  - IOC 컨테이너 자체가 Bean으로 등록 되어 있어 아래와 같이 가져 올 수 있음  
    ```java
    @Autowired
    ApplicationContext applicationContext
    ```


### AOP(Abspect Oriented Programming)
- AOP: 흩어진 코드를 한 곳으로 모으는 코딩 기법  
  - 기존의 코드를 수정하지 않기 때문에 여러 부분을 한번에 추가 및 수정할 때 매우 유용한 기능
  - 스프링에서는 `Proxy 패턴`을 사용하여 AOP를 처리함
- Annotation: 아무 기능이 없음 주석과 같은 역할임  
  - Annotation을 동작하기 위해 `Aspect` 클래스로 기능을 구현해야함  
  - 이후의 애노테이션을 읽어서 처리하는 코드를 만들어서 기능을 추가하는 방식
- AOP를 위한 Annotation
  - @Target(ElementType.METHOD)
    - 메서드에 붙일 것이기 때문에 METHOD로 지정
  - @Retention(RetentionPolicy.RUNTIME)
    - 애노테이션을 사용한 코드를 언제까지 유지할건지 런타임때까지 유지  
  - @Around("@annotation(LogExecutionTime)")
    - 이 애노테이션 주변으로 할일 정의  
    - 메서드가 호출되기 전, 후, 오류가 났을 때 다 사용할 수 있는게 Around
    - Bean 이라는 어노테이션을 써도됨
    - 특정 표현식을 쓰면 @annotation 을 안붙이고도 정의할 수 있음
  - annotation: LogExecutionTime
    ```java
    //메서드에 붙일 거기 때문에 METHOD로 지정
    @Target(ElementType.METHOD)
    //애노테이션을 사용한 코드를 언제까지 유지할건지 런타임때까지 유지로 지정
    @Retention(RetentionPolicy.RUNTIME)
    public @interface LogExecutionTime {

    }
    ```

  - class: LogAspect
    ```java
    //Component에서 Bean으로 등록
    @Component
    @Aspect
    public class LogAspect {

        Logger logger = LoggerFactory.getLogger(LogAspect.class);

        //이 애노테이션 주변으로 할일 정의
        //메서드가 호출되기 전, 후, 오류가 났을 때 다 사용할 수 있는게 Around
        //특정 표현식을 쓰면 @annotation 을 안붙이고도 정의할 수 있음
        @Around("@annotation(LogExecutionTime)")
        public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
            //스프링이 제공하는 StopWatch
            StopWatch stopWatch = new StopWatch();
            stopWatch.start();

            Object ret = joinPoint.proceed();

            stopWatch.stop();
            logger.info(stopWatch.prettyPrint());

            return ret;
        }
    }
    ```

### PSA(Portable Service Abstration)
- PSA
  - 잘 만든 인터페이스  
  - 스프링은 거의 모든 인터페이스가 PSA임  
  - 추상화 되어있는 Abstraction 계층  
  - 조금 더 유연하게 코드를 작성할 수 있도록 좋은 인터페이스를 만들어서 제공  
  - PSA 인터페이스 대부분의 코드는 추상화 되어있으므로 구현체가 바뀌더라도 Aspect 코드가 바뀌지 않음  


### 스프링 웹 MVC(@Controller와 @RequestMapping)
- `@Controller`과 `@RequestMapping` 애노테이션만 보고 서블릿을 쓰는지 리액티브를 쓰는지 의존성을 확인해보기 전에 알 수 없음
- `@GetMapping`을 통해서 URL과 호출 메소드를 연동
- 기술에 독립적으로 만들어진 추상화 구현체  


### 스프링 트랜잭션(PlatformTransactionManager)
- PSA 중 하나로 `@Transactional` 애노테이션이 붙여진 매서드는 자동적으로 SQL 트랜잭션 처리가 됨
- 기술에 독립적인 `PlatformTransactionManager` 인터페이스로 구현을 해놓음
- `@Transactional` Aspect 안에서 `PlatformTransactionManager`인터페이스를 가져다가 쓰게 됨  
- 구현체들이 바뀌더라도 `@Transactional` Aspect의 코드는 바뀌지 않음  
- 구현체
  - JpaTransactionManager
  - DatasourceTransactionManager
  - HibernateTransactionManager


### 스프링 캐시(CacheManager)
- PSA 중 하나로 스프링 캐시의 구현체가 바뀌더라도 `@Cacheable`, `@CacheEvict` Aspect 코드는 바뀌지 않음  
- 구현체
  - JCacheManager
  - ConcurrentMapCacheManager
  - EhCacheCacheManager
