# Spring Framework 정리 자료
인프런 백기선 강사님 예제로 배우는 스프링 입문(개정판) 정리 문서

### IOC(Inversion Of Control)
  - 제어의 역전    
      - 보통 아래와 같이 자신의 제어권을 자기 자신이 가짐  
      ```java
      class OwnerController {
      private OwnerRepository repository = new OwnerRepository();
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
  - `@MockBean` 애노테이션으로 가짜 OwnerRepository 객체를 주입 받아서 사용할 수 있음  


## IOC(Inversion Of Container) 컨테이너
- IOC 컨테이너
    - 직접쓸일이 거의 없음  
    - BeanFactory
    - ApplicationContext: 자기가 컨테이너 내부에 만든객체들 Bean들의 의존성을 관리해줌

### 의존성 주입
- 의존성 주입
  - @Autowired를 이용해 빈을 꺼내와서 의존성을 주입해줄 수 있음  
  - IOC 컨테이너 자체가 Bean으로 등록되어 있어 아래와 같이 가져 올 수 있음  
    ```java
    @Autowired
    ApplicationContext applicationContext
    ```

## 빈(Bean)
- 빈: 스프링 IoC 컨테이너가 관리하는 객체  

## AOP(Abstract Oriented Programming)
- AOP: 흩어진 코드를 한 곳으로 모으는 코딩 기법  
  - 스프링에서는 Proxy 패턴을 사용하여 AOP를 처리함  

### Annotation
- Annotation: 아무 기능이 없음 주석과 같은 역할임  
  - Annotation을 동작하기 위해 Aspect 클래스로 기능을 구현해야함  

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
//메서드에 붙일거기때문에 METHOD로 지정
@Target(ElementType.METHOD)
//애노테이션을 사용한 코드를 언제까지 유지할건지 런타임때까지 유지
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {
}
```
