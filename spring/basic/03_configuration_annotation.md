# Annotation을 사용한 설정

## @Bean

- class에 `@Configuration`을 사용
- 등록할 빈에 `@Bean`사용
<br><br>
- VO클래스
```java
// Mic.java
public interface Mic {
    void turnOn();
    void turnOff();
}

// WirelessMic.java
public class WirelessMic implements Mic {
    private String brand;

    public void turnOn () {
        System.out.println("WirelessMic On");
    }

    public void turnOff () {
        System.out.println("WirelessMic Off");
    }
    //... getter, setter, toString...
}

// Human.java
public class Human {
    String name;

    public void sing (Mic mic) {
        mic.turnOn();
        System.out.println("Sing a Song");
        mic.turnOff();
    }
    //... getter, setter, toString...
}
```
- 설정 클래스
```java
@Configuration
public class SpringConfig {

    @Bean
    public Human human() {
        Human human = new Human();
        human.setName("Kim");
        human.sing(wirelessMic());
        return human;
    }

    @Bean
    public Mic wirelessMic() {
        return new WirelessMic();
    }
}
```
- 위에서 `wirelessMic()`가 여러번 호출 되더라도 Mic객체 레퍼런스는 동일하다. (스프링컨테이너에서 싱글톤으로 관리해줌)
- 최초 어플리케이션 로딩때 빈을 전부 생성한 뒤 싱글톤으로 관리하는거 같다.
 
## @Autowired

- 자동 와이어링이 필요한 대상에 `@Autowired` 사용
- `@Autowired`는 타입으로 추론해 주입한다.
```java
@Configuration
public class SpringConfig {

    @Autowired 
    Mic wirelessMic;

    @Bean
    public Human human() {
        System.out.println("human() Bean");
        Human human = new Human();
        human.setName("Kim");
        human.sing(wirelessMic); // Autowired된 객체 사용
        return human;
    }

    @Bean
    public Mic wirelessMic() {
        System.out.println("wirelessMic() Bean");
        return new WirelessMic();
    }
}
```

- 만약 동일타입 빈이 여러개인 경우는?
```java
// ....
@Bean
public Mic wirelessMic1() {
    System.out.println("wirelessMic1() Bean");
    return new WirelessMic();
}
@Bean
public Mic wirelessMic2() {
    System.out.println("wirelessMic2() Bean");
    return new WirelessMic();
}
// ....
```
- 위와 같은 경우 어느 빈을 사용할지 모르기 때문에 Exception 발생함..
- `@Qualifier`를 사용해 아래와 같이 지정해 줄 수 있다.
```java
// ....
@Autowired
@Qualifier("wireless01")
Mic wirelessMic;    // 여기에는 wirelessMic1()로 생서된 빈이 주입된다.

// ....
@Bean
@Qualifier("wireless01")
public Mic wirelessMic1() {
    System.out.println("wirelessMic1() Bean");
    return new WirelessMic();
}
@Bean
@Qualifier("wireless02")
public Mic wirelessMic2() {
    System.out.println("wirelessMic2() Bean");
    return new WirelessMic();
}
// ....
```

- 만약 `@Qualifier`가 없는데 동일빈이 복수개 존재하는 경우(즉 타입으로 찾을 수 없는 경우), 이름이 일치하는 빈을 주입한다.
```java
@Autowired
Mic wirelessMic1;   
// Mic타입으로 추론된 빈이 복수개이지만, wirelessMic1을 이름으로 갖는(xml상 id) 빈이 존재하므로 wirelessMic1()을 통해 주입된다.

@Bean
public Mic wirelessMic1() {
    System.out.println("wirelessMic1() Bean");
    return new WirelessMic();
}
@Bean
public Mic wirelessMic2() {
    System.out.println("wirelessMic2() Bean");
    return new WirelessMic();
}
```
- 즉, `@Autowired`는 먼저 타입으로 추론. 없는경우 이름으로 추론한다.

## @Resource

- 위와 같이 `@Autowired`를 사용해 주입할 수도 있고, 동일한 기능을 하는 `@Resource` 어노테이션도 있다.
- 차이점 : `@Autowired`는 타입으로 추론하고, `@Resource`는 이름으로 추론한다.

```java
@Resource
Mic wirelessMic;    // 주입받지 못함

@Bean("wirelessMic01")
public Mic wirelessMic1() {
    System.out.println("wirelessMic1() Bean");
    return new WirelessMic();
}

@Bean("wirelessMic02")
public Mic wirelessMic2() {
    System.out.println("wirelessMic2() Bean");
    return new WirelessMic();
}
```
```java
@Resource(name="wirelessMic01")
Mic wirelessMic;    // wirelessMic1()을 통해 주입받음

// 또는 아래와 같이 할 수도 있음
// @Resource Mic wirelessMic01; // wirelessMic1()을 통해 주입받음

@Bean("wirelessMic01")
public Mic wirelessMic1() {
    System.out.println("wirelessMic1() Bean");
    return new WirelessMic();
}

@Bean("wirelessMic02")
public Mic wirelessMic2() {
    System.out.println("wirelessMic2() Bean");
    return new WirelessMic();
}
```
