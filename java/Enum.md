# 상수관리
참고 : [우아한형제들 기술블로그](http://woowabros.github.io/tools/2017/07/10/java-enum-uses.html)
/ [이동욱님 개인블로그](https://jojoldu.tistory.com/122)

- 일본회사에서 썼던 상수모음 인터페이스
- 아래와 같은 인터페이스를 구현하는 클래스에서 상수를 사용하도록 강제했음.

```java
public interface Consts {
    /** 카운트 관련 */
    Integer MIN_COUNT = 10;
    Integer MAX_COUNT = 100;

    /* 공통코드 관련 */
    String MAN = "m";
    String WOMAN = "f";

    // ...
}
```

- 문제점?
  - 점점 많아질수록 관심사가 비슷한 상수모음을 관리하기 어려워진다 -> interface를 쪼개는 방법으로 해결가능하긴 함
  - 이름에 대한 값이 보장되지 않음.(변수명을 얻어낼 수 없음)
  - type safe하지 않음. 예를들면 신규인원이 해당 인터페이스의 상수값을 사용해야 하는 부분에 인터페이스를 사용해야 하는줄 모르도 10, "m"등을 사용해도 체크할 수 없음

- 그럼 왜 enum을 써야하지?
   - 아직 잘 모르겠다.. 그래서 이걸 쓴다..

## Enum 설명

- 이동욱님이 소개한 Enum을 통해 얻는 장점
  - IDE의 적극적인 지원 (이것은 인정)
  - 허용 가능한 값 제한 (타입체크 말씀하신듯)
  - 리팩토링시 변경 범위가 최소화 (동일의미를 공유하는 다른값의 상수가 존재하는 경우 효과가 큰거 같음)

- 부수적인 장점
  - switch문에서 사용가능하다
     
---

- 사용예제
```java
public enum EndSlct {
    ON_SERVICE("n"),    // 서비스중 
    FINISHED("y"),         // 종료
    EXPIRED("c"),         // 만료
    SAVED("i");                // 저장

    private String value;
    
    EndSlct(String value) {
        this.value = value;
    }
    
    public String getKey() {
        return name();
    }
    
    public String getValue() {
        return value;
    }
}
```

- Enum 클래스 활용
```java
Class endSlctClass = EndSlct.class
endSlctClass.getEnumConstants();    // [Enum이 갖는 name배열 반환]

/**
 * Web에서 사용하기위해 JSON형태로 가공이 필요한 경우
 *
 *  - EnumModel은 key,value를 반환하는 함수를 가진 인터페이스
 *  - 여기서 예로 들면 EndSlct implements EnumModel인 셈.
 *
 *  - EnumValue는 화면으로 가져갈 VO클래스 (key, value값을 가짐)
 */
private List<EnumValue> toEnumValues(Class<? extends EnumModel> e){
    /* Java8이 아닐경우
        List<EnumValue> enumValues = new ArrayList<>();
        for (EnumModel enumType : e.getEnumConstants()) {
                enumValues.add(new EnumValue(enumType));
        }
        return enumValues;
    */
    return Arrays
            .stream(e.getEnumConstants())
            .map(EnumValue::new)
            .collect(Collectors.toList());
}

```

- 위 코드에서 필요한 클래스들 정의는 아래 참조
```java
public interface EnumModel {
    String getKey();
    String getValue();
}
```
```java
public class EnumValue {
    private String key;
    private String value;

    public EnumValue(EnumModel enumModel) {
        key = enumModel.getKey();
        value = enumModel.getValue();
    }

    public String getKey() {
        return key;
    }

    public String getValue() {
        return value;
    }
}
```

- toEnumValues를 매번 호출하는것보다 Enum을 모아놓고 꺼내쓸 수 있는 Map이나 새로운 클래스를 만들어 스프링빈으로 관리하는 것도 좋은 방법이다.

---

## Enum에 함수를 담자

- java 8 미만
```java
enum Currency {
    DOLLAR(1200) {
        @Override
        void showCurrency () {
            System.out.println("DOLLAR !!");
        }
    },
    YEN(1000) {
        @Override
        void showCurrency () {
            System.out.println("YEN ##");
        }
    };
    
    private Integer value;
    
    Currency (Integer value) {
        this.value = value;
    }
    
    // enum별 각각 다른 처리가 필요한 함수는 추상메서드로 선언후 구현 (여러개 가능)
    abstract void showCurrency();
    
    // 각 enum에게 모두 필요한 동일한 메소드
    public Integer exchange(Integer won) {
        return getValue() * won;
    }
    
    public Integer getValue () {
        return value;
    }
}
```

- java 8 이상
  - FunctionalInterface를 필드로 갖게 해서 구현할 수 있다.
    - 아래 예제의 Consumer뿐만 아니라 Fucntion, Predicate 이외의 모든 F/I 가능
```java
enum Currency {
    DOLLAR(1200, name -> System.out.println(name + "!!")),
    YEN(1000, name -> System.out.println(name + "##"));
    
    private Integer value;
    private Consumer<String> expression;
    
    Currency (Integer value, Consumer<String> expression) {
        this.value = value;
        this.expression = expression;
    }
    
    // enum별 각각 다른 처리가 필요한 함수는 추상메서드로 선언후 구현 (여러개 가능)
    public void showCurrency() {
        expression.accept(name());
    }
    
    // 각 enum에게 모두 필요한 동일한 메소드
    public Integer exchange(Integer won) {
        return getValue() * won;
    }
    
    public Integer getValue () {
        return value;
    }
}
```