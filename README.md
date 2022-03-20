# DesignPattern_Study_TemplateMethod

# Notion Link
https://five-cosmos-fb9.notion.site/Template-Method-fb44bf18bbc048679efabd2a8cfb4e5f

# 템플릿 메소드 (Template Method)

### 의도

알고리즘의 구조를 **메소드에 정의하고**

하위 클래스에서 알고리즘 구조의 **변경없이** 알고리즘을 **재정의하는** 패턴이다.

알고리즘이 단계별로 나누어 지거나, 같은 역할을 하는 메소드이지만 여러곳에서 다른형태로 사용이 필요한 경우 유용한 패턴이다.

<aside>
🎈 in. 토비의 스프링
- 상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 가장 대표적인 방법
- 변하지 않는 기능은 슈퍼클래스에 만들어두고 
- 자주 변경되며 확장될 기능은 서브 클래스에서 만들도록 한다.

</aside>

<aside>
🎈 in. WiKi
- 동작 상의 알고리즘의 프로그램 뼈대를 정의하는 행위 디자인 패턴이다.
- 알고리즘의 구조를 변경하지 않고 알고리즘의 특정 단계들을 다시 정의할 수 있게 한다.

</aside>

<aside>
🎈 in. 인프런 스프링 핵심원리 고급편 강의 중

### 변하는 것과 변하지 않는 것을 분리

**좋은 설계는 변하는 것과 변하지 않는 것을 분리하는 것이다.**

위 예를 본다면 **“핵심 기능”**은 변하고, **“부가 기능”**은 변하지 않는 부분이다.

**즉, 이 둘을 분리하여 모듈화 해야 한다.**

→ 이러한 문제를 해결하는 좋은 패턴이 **`템플릿 메서드 패턴 (Template Method Pattern)`** 이다. 

---

부모 클래스에 알고리즘의 골격인 템플릿을 정의

일부 변경되는 로직은 자식 클래스에서 정의

- 이렇게 하여 자식 클래스가 알고리즘의 전체 구조를 변경하지 않고, 특정 부분만 재정의
- 결국 상속과 오버라이딩을 통한 다형성으로 문제를 해결하는 것
</aside>

### 구조

![image](https://user-images.githubusercontent.com/18654358/159145248-211830ad-0b6e-4903-8416-8b64f334dbaf.png)

![image](https://user-images.githubusercontent.com/18654358/159145259-05743ea0-52b1-454f-90d1-a5dbbba9ea5b.png)

### 간단한 예제로 알아보자.

**템플릿 메서드 패턴을 적용하기 전**

- logic1(), logic2() 를 구현하고 실행한다.
- 두개 메소드는 각각 **본인이 해야하는 핵심 비즈니스 로직**이 있고, 
**공통적으로 처리되는 부가기능**이 있다.

```java
package hello.advanced.trace.template;

import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;

@Slf4j
public class TemplateMethodTest {

    @Test
    void templateMethodV0(){
        logic1();
        logic2();
    }

    // 비즈니스 로직 = 핵심 로직
    // 실행 시간을 확인하기 위한 로직 = 부가 기능
    private void logic1() {
        long startTime = System.currentTimeMillis();

        // 비즈니스 로직 실행
        log.info("비즈니스 로직 1 실행");
        // 비즈니스 로직 종료

        long endTime = System.currentTimeMillis();
        long resultTime = endTime - startTime;

        log.info("resultTime = {}", resultTime);
    }

    // 비즈니스 로직 = 핵심 로직
    // 실행 시간을 확인하기 위한 로직 = 부가 기능
    private void logic2() {
        long startTime = System.currentTimeMillis();

        // 비즈니스 로직 실행
        log.info("비즈니스 로직 2 실행");
        // 비즈니스 로직 종료

        long endTime = System.currentTimeMillis();
        long resultTime = endTime - startTime;

        log.info("resultTime = {}", resultTime);
    }
}
```

**템플릿 메서드 패턴 적용 시작**

**AbstractTemplate.java**

- **추상클래스** 작성
- **변하지 않는 부분(부가기능) 을 execute() 메소드에 정의**한다.
- 그 중간에 수행되는 **변하는 부분(핵심 비즈니스 로직)을** **상속으로** 처리할 수 있게 한다.

```java
package hello.advanced.trace.template.code;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public abstract class AbstractTemplate {

		// 변하지 않는 부가기능 입니다.
    public void execute(){
        long startTime = System.currentTimeMillis();

        // 비즈니스 로직 실행
        call(); // 상속!
        // 비즈니스 로직 종료

        long endTime = System.currentTimeMillis();
        long resultTime = endTime - startTime;

        log.info("resultTime = {}", resultTime);
    }

		// 변하는 핵심 비즈니스 로직을 상속해서 구현하세요!
    protected abstract void call();
}
```

- 변하지 않는 부분인 시간 측정 로직을 몰아 둔 것을 확인할 수 있다.
- 이것이 하나의 템플릿이 된다.
- 그리고 템플릿 안에서 변하는 부분은 **`“call()”`** 메서드를 호출하여 처리한다.
- 템플릿 메서드 패턴은 부모 클래스에 변하지 않는 템플릿 코드를 둔다.
- **`변하는 부분은 자식 클래스에 두고 상속과 오버라이딩을 사용해 처리한다.`**

SubClassLogic1.java

```java
package hello.advanced.trace.template.code;

import hello.advanced.trace.template.code.AbstractTemplate;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class SubClassLogic1 extends AbstractTemplate {
    @Override
    protected void call() {
        log.info("비즈니스 로직 1");
    }
}
```

SubClassLogic2.java

```java
package hello.advanced.trace.template.code;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class SubClassLogic2 extends AbstractTemplate {
    @Override
    protected void call() {
        log.info("비즈니스 로직 2");
    }
}
```

Test

```java
@Test
void templateMethodV1(){
    AbstractTemplate template1 = new SubClassLogic1();
    template1.execute();
    AbstractTemplate template2 = new SubClassLogic2();
    template2.execute();
}
```

**템플릿 메서드 패턴 인스턴스 호출 흐름**

![image](https://user-images.githubusercontent.com/18654358/159145267-fe154f42-8422-4430-a04b-1a67e6f30298.png)

---

---

### **개선 → 익명 내부 클래스 활용**

위와 같이 구성한다면 SubClassLogic1, SubClassLogic2, .... 와 같이 클래스를 계속해서 만들어내야 하는 구조가 발생된다.

**→ 익명 내부 클래스를 이용하여 단점을 보완할 수 있다.**

**→ 이는 Java의 Lambda를 이용하여 더 간결하게 표현 가능하다.**

```java
@Test
void templateMethodV2() {
    AbstractTemplate template1 = new AbstractTemplate() {
        @Override
        protected void call() {
            log.info("비즈니스 로직 1 실행");
        }
    };
    log.info("클래스 이름1 ={}", template1.getClass());
    template1.execute();

    AbstractTemplate template2 = new AbstractTemplate() {
        @Override
        protected void call() {
            log.info("비즈니스 로직 2 실행");
        }
    };
    log.info("클래스 이름2 ={}", template2.getClass());
    template2.execute();
}
```

---

---

### 좋은 설계란?

좋은 설계란 것은 무엇일까?

수 많은 멋진 정의가 있겠지만, 진정한 좋은 설계는 **`“변경”`**가 일어날 때 자연스럽게 드러난다.

**변하지 않고 공통적으로 처리해야 하는 부분을 모아서 하나로 모듈화** 하고, 

**비즈니스 로직 부분을 분리**했다.

**여기서 만약 공통적으로 처리해야하는 부분을 변경해야 한다고 생각해보자.**

- 그래서 AbstractTemplate 코드를 변경해야 한다 가정해보자.
- **`단순히 AbstractTemplate 코드만 변경하면 된다. !`**

**템플릿이 없는공통으로 처리해야하는 로직을 변경해야 한다고 생각해보자.**

이 경우 모든 클래스를 다 찾아서 (사용하는) 다 고쳐야 한다.

- 클래스가 수백개라면 생각만해도 끔.찍.하.다.

**단일 책임 원칙 (SRP)**

- 변경 지점을 하나로 모아서 변경에 쉽게 대처할 수 있는 구조를 만든 것이 포인트이다.

---

---

### 템플릿 메서드 패턴은 상속을 사용한다.

→ 즉, 상속에서 오는 단점을 그대로 안고 있다.

특히, 자식 클래스가 부모 클래스와 컴파일 시점에 강하게 결합되는 문제가 있다.

- 이것은 의존관계에 대한 문제이다.
- **`자식 클래스 입장에서는 부모 클래스의 기능을 전혀 사용하지 않는다.`**
    - 그럼에도 불구하고 자식 클래스는 부모 클래스를 상속받고 있다.
    

**상속받는 다는 것은?**

→ 자식 클래스가 부모 클래스를 의존하고 있다는 것이다.

<aside>
🎈 즉, 부모 클래스의 기능을 사용하든 사용하지 않든 간에 부모 클래스를 강하게 의존한다.

</aside>

**강하게 의존한다는 것은?**

- 자식 클래스의 코드에 부모 클래스의 코드가 명확히 적혀있따는 것이다.

자식 클래스 입장에서는 부모 클래스 기능을 전혀 사용하지 않는데, 부모 클래스를 알아야 한다는 것이다. → 이것은 좋은 설계가 아니다.

- 잘못된 의존관계로 인해 부모 클래스를 수정하게 되면 자식 클래스에도 영향을 미치게 될 수 밖에 없다.

**그렇다면, 이런 템플릿 메소드 패턴 “상속”의 단점을 제거할 수 있는게 무엇일까?**

→ 전략 패턴이다. 

- 일반적으로 더 자주 사용되고 더 나은 패턴이다.

<aside>
🎈 상속 보다는 위임. 
= 상속 보다는 컴포지션.
= 상속 보다는 구성.

</aside>
