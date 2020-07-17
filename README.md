# Spring Framework 정리 자료
스프링 petclinic 샘플 프로젝트와 예제로 배우는 스프링 입문(개정판) 정리 문서

## 3 Triangle - IOC/DI

#### IOC(Inversion Of Control)
- 제어의 역전    
    - 보통 아래와 같이 자신의 제어권을 자기 자신이 가짐  
    ```java
    class OwnerController {
      private OwnerRepository repository = new OwnerRepossitory();
    }
    ```  
    - 제어의 역전은 아래와 같이 `의존성 제어권이 자기 자신에 있지 않고 외부에 있는 것`을 말함
    - 즉, 의존성을 만드는 일을 외부에 맡기는 것
    - 실제 의존성 주입은 스프링(ApplicationContext)가 해줌
    - 아래 코드는 생성자에서 의존성을 주입함
    ```java
    class OwnerController {
      private OwnerRepository repo;

      public OwnerController(OwnerRepository repo) {
        this.repo = repo;
      }
    }
    ```
    - OwnerController 객체를 만들려면 OwnerRepository 객체가 먼저 만들어져서 생성자에서 주입이 되어야만함 
    ```java
    class OwnerControllerTest {
      public void create() {
        OwnerRepository repo = new OwnerRepository();
        OwnerController controller = new OwnerController(repo);
      }
    }
    ```
 

#### IOC(Inversion Of Container) 컨테이너
- IOC 컨테이너
  - `IOC 컨테이너(스프링 컨테이너)`: 객체가 스프링이 관리하는 객체인 Bean에 등록이 되면 Bean을 만들고 Bean 사이의 의존성을 엮어주고 만들어진 Bean들을 제공해주는 일을 함
  - 의존성을 관리해주는 일이라는 것은 애노테이션이나 특정한 생성자를 보고서 필요한 의존성을 자동으로 주입해주는 것을 의미
  - IOC 컨테이너는 BeanFactory 클래스나 ApplicationContext 클래스로 직접 쓸 일이 거의 없음
  - `ApplicationContext`: 자기가 컨테이너 내부에 만든객체들 Bean들의 의존성을 관리해주는 역할을 하는 BeanFactory의 서브 클래스로 우리가 사용하는 IOC 컨테이너
  - IOC 컨테이너에 등록한 빈 객체들을 사용하면 멀티스레딩 상황에서도 안전한 싱글톤 스코프 객체를 사용할 수 있음


#### 빈(Bean)
- 빈(Bean): 스프링 IoC 컨테이너가 관리하는 객체로 ApplicationContext를 통해서 객체를 받와야만 Bean임
- IOC 컨테이너에 빈으로 등록하는 방법
  - `Component Scanning`을 이용: @Component이라는 애노테이션을 활용한 @Repository, @Service, @Controller, @Configuration 등등의 Annotation으로 활용하여 등록(대부분 이 방법 사용)
  - `Bean`으로 직접 등록: @Configuation Annotation이 들어가 있는 자바 설정 파일에서 `@Bean`이라는 Annotation으로 등록
  - `ApplicationContext.xml` 파일에 직접 등록: <bean></bean>안에 정보를 넣어 등록
  - 특정한 인터페이스를 상속 받고 있는 클래스를 상속하여 등록
- 등록된 빈을 얻는 방법
  - 의존성 주입을 할 때 `@Autowired`을 사용하여 방법으로 얻기(대부분 이 방법 사용)
  - `ApplicationContext`을 활용해서 직접 얻기


#### 의존성 주입
- 의존성: `현재 객체가 다른 객체와 상호작용(참조)하고 있다면` 현재 객체는 다른 객체에 의존성을 가진다고 함
- 의존성을 사용자가 제어할 경우 모듈이 바뀌면 의존한 다른 모듈을 변경하는 문제를 일일히 처리해야함
- 의존성 주입: 프레임워크에 의해 객체의 의존성을 주입하여 동적으로 주입하여 객체 간의 결합을 줄임
- 생성자, 세터, 필드에 `@Autowired`를 이용해 빈을 꺼내와서 의존성을 주입해줄 수 있음(주로 생성자 사용)


## 3 Triangle - AOP

#### AOP(Abspect Oriented Programming)
- AOP: 프로젝트 구조를 바라 보는 관점을 바꿔서 공통된 기능을 재사용하여 흩어진 코드를 한 곳으로 모으는 코딩 기법  
  - 기존의 코드를 수정하지 않고 새로운 코드를 넣기 때문에 여러 부분을 한번에 추가 및 수정할 때 매우 유용한 기능
  - 스프링에서는 `Proxy 패턴`을 사용하여 AOP를 구현함
- Annotation: 그 자체는 아무 기능이 없는 주석과 같은 역할임  
  - Annotation을 동작시키 위해 `Aspect` 클래스로 기능을 구현해야함  
  - 이후의 애노테이션을 읽어서 처리하는 코드를 만들어서 기능을 추가
- AOP를 구현을 위한 Java의 Annotation
  - `@Target(ElementType.METHOD)`
    - 메서드에 붙일 것이기 때문에 METHOD로 지정
  - `@Retention(RetentionPolicy.RUNTIME)`
    - 애노테이션을 사용한 코드를 언제까지 유지할건지 런타임때까지 유지  
- AOP를 구현을 위한 Java의 Annotation를 처리할 Aspect 클래스
  - `@Component`
    - 빈으로 등록하기 위한 애노테이션
  - `@Aspect`
    - Aspect라는 것을 알리기 위한 애노테이션
  - `@Around("@annotation(LogExecutionTime)")`
    - 메서드가 호출되기 전과 후에 오류가 났을 때 다 사용할 수 있는게 Around
    - joinPoint라는 인터페이스 타입으로 타겟 메소드가 Around 안에 들어옴
  - annotation: LogExecutionTime
    ```java
    //메서드에 붙이는 애노테이션이기 때문에 METHOD로 지정
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

## 3 Triangle - PSA

#### PSA(Portable Service Abstration)
- Service Abstraction: 추상화 계층을 사용해서 어떤 기술을 내부에 숨기고 개발자에게 편의성을 제공해주는 것
- PSA(Portable Service Abstraction): Service Abstraction으로 제공되는 기술을 다른 기술 스택으로 간편하게 바꿀 수 있게 추상화하여 일관성있게 제어하는 기능이 확장성을 갖고 있는 것
  - 스프링은 거의 모든 인터페이스가 PSA
  - 어떤 기술을 내부에 숨기고 개발자에게 편의성을 제공해주는 것
  - 조금 더 유연하게 코드를 작성할 수 있도록 좋은 인터페이스를 만들어서 제공  
  - PSA 인터페이스 대부분의 코드는 추상화 되어 있으므로 구현체가 바뀌더라도 내부 코드가 바뀌지 않음


#### 스프링 웹 MVC(@Controller와 @RequestMapping)
- PSA 중 하나로 MVC에 해당하는 애노테이션이 붙여진 메소드들에 대해서 구현체에 상관없이 일관성 있는 처리가 됨
- C
  - `@Controller` 애노테이션을 사용하면 요청을 처리하는 컨트롤러 클래스로 정의
  - `@GetMapping`, `@PostMapping`, `@PutMapping`, 등등을 통해서 URL과 호출 메소드를 연동
    - 추가적인 속성 설정을 통해 기능을 더 강하게 할 수 있음
- V
  - template 디렉토리에 파일들을 통해서 매핑하여 리턴할 수 있음
  - 타임리프같은 템플릿 엔진을 통해서 뷰를 더 강하게 만들 수 있음
- M
  - 웹 MVC에 모델에 해당하는 객체로 뷰와 컨트롤러에서 사용하는 객체
- 웹 MVC 구현체
  - 톰캣
  - 제티
  - 네티
  - 언더토우

#### 스프링 트랜잭션(PlatformTransactionManager)
- PSA 중 하나로 `@Transactional` 애노테이션이 붙여진 매서드는 자동적으로 DB에서 의존성이 있는 특정한 일련의 처리들이 묶인 SQL 트랜잭션 처리가 됨
- 구현체들이 바뀌더라도 `@Transactional` Aspect의 코드는 바뀌지 않음  
- 트랜잭션 구현체
  - JpaTransactionManager
  - DatasourceTransactionManager
  - HibernateTransactionManager


#### 스프링 캐시(CacheManager)
- PSA 중 하나로 스프링 캐시의 구현체가 바뀌더라도 `@Cacheable`, `@CacheEvict` Aspect 코드는 바뀌지 않음
- 캐시 구현체
  - JCacheManager
  - ConcurrentMapCacheManager
  - EhCacheCacheManager
