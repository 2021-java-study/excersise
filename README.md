# 2021.09.13 (월)

## **이번 주 학습**

------------------------

### 1. VO와 DTO

1. ***VO***(**V**alue **O**bject): 사용 되는 값이 객체로 표현되며, 값 변경이 거의 없다.
2. ***DTO***(**D**ata **T**ransfer **O**bject): 데이터의 전송을 위한 객체이며, 특정 테이블의 정보를 레코드 단위로(1줄) 담는다.

* ***DAO***(**D**atabase **A**ccess **O**bject): 데이터베이스 접근을 위한 객체이며, 비지니스 로직까지 담아서 사용하기 한다.

- 유사점: 둘 다 POJO이다.

- 차이점
  1) VO는 <u>변경할 수 없다는(immutable, readonly) 특성</u>에 따라 프로그램 내부 속성값 등을 취급할 객체이고,
  2) DTO는 사용자, DB 등 프로그램 외부와 <u>데이터 교환을 하기 위한 객체</u>이다.
​

------------------------

### 2. TDD(테스트 주도 개발: Test Driven Development)

- 발표에서는 그 개념을 제대로 이해하지 못한 상태에서 말했지만, 지금 정리함.
  1. 테스트 코드를 먼저 짠다.
  2. 대상 코드가 없을테니 당연히 에러가 난다.
  3. 테스트 통과를 할 수 있는 최소한의 코드를 짠다.
  4. 리팩토링
  5. 1~4 반복

- 출처: ***[테스트 주도 개발방법론 TDD(Test-driven Development)이란?](https://nesoy.github.io/articles/2017-01/TDD)***

```
스터디 교재를 보면, 테스트 코드 작성을 계속 시킨다.
예제를 열심히 따라칠 때에는 위의 프로세스와 전혀 무관했지만, 예제 순서는 은근히 비슷했다.
위 프로세스를 머리에 넣어놓고 코드를 짜보면 괜찮을 것 같다.
```

------------------------

### 3. DB 작업(?) 시 생각해봐야 할 것들(교재 3장)

- Oracle DB 에서의 Sorting 작업
  1. ORDER BY: Select 결과를 정렬해주는 구문 뒤에 ASC(오름차순), DESC(내림차순)을 붙인다.
  2. 인덱스: 하술할 힌트를 활용하여 인덱스에 따라 Select를 하는 방법. 인덱스는 내림차순 정렬이 되어있음.
  
- 쿼리 처리 순서
  + 테이블 예시

    STUDENT_ID  | NAME   | MAJOR          | GRADE
    ----------  | ------ | -------------- | -----
    201081001   | 김개불 | 불교학         | 4
    201411106   | 소고집 | 불교학         | 4
    201632013   | 다람쥐 | 사회복지학     | 2
    201742009   | 라일락 | 중국어통번역학 | 3

  + SQL 쿼리 예시
    ```sql
    SELECT * WHERE grade=4 FROM people;
    ```

  + 처리 순서
    1) 쿼리 실행
    2) people 테이블에 접근
    3) 모든 레코드 중 GRADE가 4인 레코드인지 판별
    4) 2의 결과가 참인 레코드만 반환

  + 순서도
    ![Flow Chart of Query Processing Order](https://oracle.readthedocs.io/en/latest/_images/query-proc-order.svg)
    * 출처: ***[Query Processing Order - Oracle SQL & PL/SQL Optimization for Developers](https://oracle.readthedocs.io/en/latest/sql/basics/query-processing-order.html)***

- 힌트(Hint)): 오라클 DB에서 사용되는 SQL 표현식
  + 표현식: 주석시작 부분에 +를 추가하면 힌트절로 인식됨
    ```sql
    SELECT /*+ INDEX_DESC(tbl_board pk_board) */ * FROM tbl_board; -- 힌트가 올바르지 못하면 일반 주석으로 처리됨
    ```

- DB에서의 실행계획(Execution Plan) 또는 쿼리플랜(Query Plan)
  - SQL문을 실행하면 DB 내 옵티마이저가 최적의 처리 경로(과정)을 찾아내는데, 이를 실행계획 또는 쿼리 플랜이라 한다.
  - 엄밀한 비교는 아니나, 개발자 입장에서 쉽게 비유하자면 DB(SQL)계의 디버거라고 할 수 있겠다.

```
어떻게든 화면만 나오게 하는 것도 중요하지만, 에러가 안 나는 코드, 좀 더 실행이 빨리 잘 되는 코드는 더욱 중요하다.

데이터의 성질, 실행순서 등을 종합적으로 고려하여 프로그래밍을 해야 할 것이다.
```

------------------------

### 4. 의존성 주입 방법 -> 생성자 주입 패턴 사용!

- 출처 1: ***[[Design pattern - 실무편] 의존성 주입 (Dependency Injection) 구현하기](https://nowonbun.tistory.com/485)***
- 출처 2: ***[생성자 주입을 @Autowired를 사용하는 필드 주입보다 권장하는 하는 이유](https://madplay.github.io/post/why-constructor-injection-is-better-than-field-injection)***

- 싱글턴의 약점을 극복하기 위해 의존성 주입(DI)를 잘 써먹고 있을 것이다.

- 스프링에서는 낮은 결합도를 위해 아래와 같이 크게 세 가지 방법으로 객체 간에 의존성을 주입한다.
  1. 필드 의존성 주입(Field Injection)
      ```java
      public class Cat {
        @Autowired
        private Dog dog;  // 필드 의존성 주입

        void meow() {
          dog.bark();
        }
      }

      public class Dog {
        @Autowired
        private Cat cat;  // 필드 의존성 주입

        void bark() {
          cat.meow();
        }
      }
      ```

  2. Setter를 통한 의존성 주입(Setter based Injection)
      ```java
      public class Cat {
        private Dog dog;

        @Autowired
        public void setDog(Dog dog) {  // Setter 의존성 주입
          this.dog = dog;
        }

        void meow() {
          dog.bark();
        }
      }

      public class Dog {
        private Cat cat;

        @Autowired
        public void setCat(Cat cat) {  // Setter 의존성 주입
          this.cat = cat;
        }

        void bark() {
          cat.meow();
        }
      }
      ```

  3. 생성자를 통한 의존성 주입(Constructor based Injection)
      ```java
      public class Cat {
        private final Dog dog;  // final

        @Autowired  // 단일 생성자일 때는 생략 가능!
        public Cat(Dog dog) {   // 생성자 의존성 주입
          this.dog = dog;
        }

        void meow() {
          dog.bark();
        }
      }

      public class Dog {
        private final Cat cat;  // final

        @Autowired  // 단일 생성자일 때는 생략 가능!
        public Dog(Cat cat) {   // 생성자 의존성 주입
          this.cat = cat;
        }

        void bark() {
          cat.meow();
        }
      }
      ```

- 코드로 보면 알겠지만, 필드 인젝션이 가장 쉽고 코드도 적다.

- 하지만 위의 코드는 두 객체가 순환참조되는 코드인데, ```bark()```나 ```meow()``` 중 하나만 호출되면 에러를 뱉게 된다.
  + 하지만 생성자 인젝션은 객체 생성 시에 스프링 프레임워크가 순환참조를 감지하게 되어 앱을 실행시키지 않는다. 간단히 비유하자면 논리 오류가 컴파일 에러로 바뀌는 셈이다.

  + 필드/세터 인젝션은 객체 생성 후 비즈니스 로직 상에서 순환참조가 일어나게 되므로, 일단 실행은 잘 되나 문제의 함수를 호출했을 때 에러가 나게 된다.

- 따라서 책에 있는 예제를 따라칠 때에도 생성자 인젝션을 연습해보는 것이 좋겠다.

------------------------

### 5. JUnit 5 적용

- 책에서는 JUnit 4 기준으로 예제가 나와있다. 최신 버전인 JUnit 5를 적용하는 방법은 다음과 같다.

  1. **JUnit 4**  
     우선 구 버전부터 적어두는 게 비교에 좋을 것 같다.

  > + POM.xml(Maven)
  >   ```xml
  >   <dependency>
  >       <groupId>junit</groupId>
  >       <artifactId>junit</artifactId>
  >       <version>4.13.2</version>
  >       <scope>test</scope>
  >   </dependency>
  >   ```
  >
  > + Java 테스트 코드
  >   ```java
  >   @RunWith(SpringJUnit4ClassRunner.class)
  >   @ContextConfiguration(classes = {RootConfig.class})
  >   @Log4j
  >   public class SampleTest {
  >
  >     private final DataSource dataSource;
  >     private final SqlSessionFactory sqlSessionFactory;
  >
  >     @Autowired
  >     public SampleTest(DataSource dataSource, SqlSessionFactory sqlSessionFactory) {
  >       this.dataSource = dataSource;
  >       this.sqlSessionFactory = sqlSessionFactory;
  >     }
  >
  >     @Test
  >     public void testCode() {
  >       try(Connection conn = dataSource.getConnection()) {
  >         log.info(conn);
  >         log.info(sqlSessionFactory);
  >       } catch(Exception e) {
  >         fail(e.getMessage());
  >       }
  >     }
  >   }
  >   ```

  2. JUnit 5

  > + POM.xml(Maven)
  >   ```xml
  >   <dependency>
  >     <groupId>org.junit.jupiter</groupId>
  >     <artifactId>junit-jupiter-api</artifactId>
  >     <version>5.7.2</version>
  >   </dependency>
  >
  >   <dependency>
  >     <groupId>org.junit.jupiter</groupId>
  >     <artifactId>junit-jupiter-engine</artifactId>
  >     <version>5.7.2</version>
  >     <scope>test</scope>
  >   </dependency>
  >   ```
  >
  > + Java 테스트 코드
  >   ```java
  >   @ExtendWith(SpringExtension.class)  // 어노테이션이 달라졌다!
  >   @ContextConfiguration(classes = {RootConfig.class})
  >   @Log4j
  >   public class SampleTest {
  >
  >     private final DataSource dataSource;
  >     private final SqlSessionFactory sqlSessionFactory;
  >
  >     @Autowired
  >     public SampleTest(DataSource dataSource, SqlSessionFactory sqlSessionFactory) {
  >       this.dataSource = dataSource;
  >       this.sqlSessionFactory = sqlSessionFactory;
  >     }
  >
  >     @Test  // 나머지 사용은 동일!
  >     public void testCode() {
  >       try(Connection conn = dataSource.getConnection()) {
  >         log.info(conn);
  >         log.info(sqlSessionFactory);
  >       } catch(Exception e) {
  >         fail(e.getMessage());
  >       }
  >     }
  >   }
  >   ```

------------------------

### 6. ***final***, ***protected***, ***private***를 잘 쓰자!

- 런타임 에러 감소
- 유연한 것과 명확치 못한 것은 다르다!
- 디버그도 빡세게 해야 하지만, 에러를 줄일 수 있는 습관을 들여야 함.
  + 그 외에도 매크로(텍스트 에디터), 멀티커서, 자동완성 등의 기능을 잘 활용하여 클래스명이나 변수명 등을 일일이 손으로 입력하는 경우를 줄이자.
  + 연산식을 적을 때, 상수는 가급적 왼쪽에 두자. +를 =로 오타냈을 때 컴파일에러가 나서 디버그가 수월해짐.
  + 컴파일 단계에서 최대한 오류를 잡을 수 있도록 고민해야 함.

------------------------

### 7. JSTL과 EL

- 출처 1: ***[Java Web Foundation: introduction to EL expression and JSTL tag library](https://www.fatalerrors.org/a/java-web-foundation-introduction-to-el-expression-and-jstl-tag-library.html)***
- 출처 2: ***[java, jstl, html, javascript 페이지 로딩 순서 / JSTL과 EL 같이 사용하기](https://defacto-standard.tistory.com/249)***
- 출처 3: ***[자바스크립트 내에서 쓰이는 EL과 JSTL에 대하여 질문있습니다.](https://okky.kr/article/430283)***

- EL(**Expression **L**anguage)
  > EL이란 JSP에서 저장객체를 출력할때 스크립팅을 전혀 쓰지 않을 수 있는 기술이다.  
  > 이것으로 인해 `<%=request.getParameter("name")%>` 와 같은 스크립팅 들을 쓸필요가 없어진다.  
  >
  > HTML, JavaScript, CSS는 자바 입장에서는 그저 문자열 덩어리일 뿐이다.  
  > 이 점을 염두에 두고 생각해보면, ***EL은 데이터 표시를 담당한다.***는 말이 매우 와닿을 것이다.

- JSTL(**J**SP - **S**tandard **T**ag **L**ibrary)
  > JSP에서는 커스텀 태그를 만들 수 있다.  
  > 이러한 점을 이용하여 논리판단, 반복문 처리와 같은 많이 사용하는 논리 처리를 표준화시킨 커스텀 태그가 JSTL이다.  
  > 
  > JSP 상에서의 논리 처리를 담당한다.  
  >> foreach, HTML 문자 탈락(ex: &lt;&gt;  ->  \&lt; \&gt;), if, choose 등

  + JSTL 안에 EL 은 사용 가능하지만 EL 안에 JSTL 은 사용할 수 없다.

- JSP view페이지에서 문자열을 출력할 때 ‘#{}’ EL을 사용하지 않고 <c:out> 을 사용하는 이유는? ***결론은, <u>보안성</u>때문이 크다.***
  > XSS(Cross-Site Scripting) 란?
  >> XSS공격은 웹사이트에 스크립트 코드를 주입시키는 방법으로 웹사이트 공격방법 중 기초적인 것에 해당된다.
  >> 해결 방법은 html코드를 해석하지 않게 만들면 간단히 방어할 수 있다.
  >>
  >> JSP view페이지를 만들 때 XSS공격 방지를 위해 "`<c:out>`"를 사용하여 막을 수 있다.  

  > JSTL `<c:out>`를 사용하는 이유?  
  > 1) HTML 문자를 탈락(escpae)가 적용된다. 태그로 인식되지 않고 브라우저 화면에까지 문자로만 출력된다.  
  > 2) 이를 통해 XSS를 막을 수 있다.  


------------------------

## **To-Do(다음 주 학습 내용)**
1. 책 ***Part 4까지*** 학습
2. 싱글턴 & 빌더 패턴 & 전략패턴
3. SOLID 원칙
4. 의존성 주입
5. 스프링 프로젝트 기본 요소: Pom web root servlet
6. 디렉토리 구조(다음주)
7. 스프링 실행 흐름
    1. 사용자의 모든 요청을 DispatcherServlet 이 받은 후 HandlerMapping 객체에 Controller 객체 검색을 요청한다.
    2. HandlerMapping 객체는 프로젝트에 존재하는 모든 Controller 객체를 검색한다.
    3. HandlerMapping 객체가 Controller 객체를 검색해서 DispatcherServlet 객체에 알려주면 DispatcherServlet 객체는 다시 HandlerAdapter 객체에 사용자의 요청에 부합하는 메소드 검색을 요청한다.
    4. HandlerAdapter 객체는 사용자의 요청에 부합하는 메소드를 찾아서 해당 Controller 객체의 메소드를 실행한다.
    5. Controller 객체의 메소드가 실행된 후 Controller 객체는 HandlerAdapter 객체에 ModelAndView 객체를 반환하는데 ModelAndView 객체에는 사용자 응답에 필요한  데이터 정보와 뷰 정보(JSP 파일)가 담겨있다.
    7. 다음으로 HandlerAdapter 객체는 ModelAndView 객체를 다시 DispatcherServlet 객체에 반환한다.
8. 알고리즘 풀어보기
9. 개인별 과제
  + YJ
    1) EL, JSTL 등 표현식 익히기
    2) 오라클 페이징 처리: Order by & Hint
    3) 쿼리 순서: (1) From (2) Where (3) Select
    4) HTML 페이지 처리: **뒤로가기** 스택 구조와 엮어서
  + SM
    1) 오답노트 기록하기(톰캣 실행 등)
