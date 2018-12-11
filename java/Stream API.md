# Java 1.8 Stream API 관련
참고 : [Eric Han's IT Blog](https://futurecreator.github.io/2018/08/26/java-8-streams/)

## 1 - Basic

1. 스트림은 데이터의 흐름이다.
1. 간결하며 함수형프로그래밍을 할 수 있다는 장점이 있다.
1. 병렬처리를 지원한다.

- 스트림동작방식 : 생성 - 가공 - 결과

---
### 생성

1. 배열로 스트림 만들기
```java
String[] arr = new String[] {"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);
```

2. 스트림 데이터 직접 만들기
```java
Stream<String> builder =
    Stream.<String>builder()
        .add("a")
        .add("b")
        .add("c")
        .build();
```

3. 기본형 테이터 스트림 활용
```java
IntStream intStream = IntStream.range(1, 3);  // [1,2]
IntStream intStreamClosed = IntStream.rangeClosed(1, 3);  // [1,2,3]
```

4. 스트림 연결
```java
Stream<String> stream1 = Stream.of("a", "b");
Stream<String> stream2 = Stream.of("c", "d");
Stream<String> concat = Stream.concat(stream1, stream2);  // [a,b,c,d]
```

---

### 가공

1. Filtering
   - 스트림 내의 요소를 평가해 거른다(말그대로 필터링)
   - filter()의 파라미터로는 boolean을 리턴하는 평가식이 들어가야 한다.(Predicate)
```java
List<String> targetList = Arrays.asList("a", "b", "c");
Stream<String> filtering = 
    targetList.stream()
              .filter(item -> item.equals("a"));  // [a]
```

2. Mapping
   - 스트림 내의 요소를 특정값으로 변환한다.
   - 요소가 객체인경우 객체내의 메소드를 활용해 객체내부속성을 모은다던가 할 수 있다.
```java
List<String> targetList = Arrays.asList("a", "b", "c");
Stream<String> mapping = 
    targetList.stream()
              .map(String::toUpperCase);  // [A, B, C]
```

3. Sorting
```java
List<String> targetList = Arrays.asList("a", "b", "c");
List<String> orderedList =
	  targetList.stream()
              .sorted(Comparator.reverseOrder())
              .collect(Collectors.toList());  // [c, b, a]
```

4. Reducing
```java
// 1+2+3 => 결과:6
IntStream.range(1, 4).reduce(Integer::sum);
// 기본값 10 + 1+2+3 => 결과:16
IntStream.range(1, 4).reduce(10, Integer::sum);
```

5. Collecting
```java
...
```

6. Matching
```java
List<String> names = Arrays.asList("Kim", "Park", "Lee");

// K를포함한게 하나라도 있음(Kim) true.
boolean any = names.stream().anyMatch(name -> name.contains("K"));

// 길이가3인게 하나라도 있음(Kim, Lee) true.
boolean all = names.stream().allMatch(name -> name.length() == 3);

// z로 끝나는게 하나도 없음 true.
boolean none = names.stream().noneMatch(name -> name.endsWith("z"));
```

7. Iterating
```java
// peek 작업중간에 실행.
names.stream()
     .peek(System.out::println)
     .map(String::toUpperCase)
     .collect(Collectors.toList());

// forEach 작업마지막에 실행
names.stream()
     .forEach(System.out::println);
```

---

## 2 - Advanced
   1. Stream은 수직적인 실행구조를 갖는다.
      - 체이닝으로 데이터를 가공하는 경우 모든 요소가 각 체이닝에서 가공되는 것이 아니라, 각 요소별로 체이닝 파이프란인을 하나씩 통과하며 수행된다.

### 수행결과예측
```java
List<String> target = Arrays.asList("a", "b", "c");
  target.stream()
        .filter(item -> {
            System.out.println("filter() called");
            return item.contains("b") || item.contains("c");
        })
        .map(item -> {
            System.out.println("map() called");
            return item.toUpperCase();
        })
        .findFirst();
```
답:<br>
filter() called<br>
filter() called<br>
map() called

---

   2. Stream은 lazy-invocation된다.
      - 즉 최종작업을 실행하지 않으면 앞에서 가공하는 함수들도 작동하지 않는다.

### 수행결과예측
```java
private long counter = 0;
private void called() { counter++; }

List<String> target = Arrays.asList("a", "b", "c");
target.stream()
  .filter(item -> {
      called();
      return item.contains("a");
});
System.out.println(counter);
```

답:<br>
counter는 0이 찍힌다.
  - 최종연산을 하지 않았기 때문에 실제로 스트림내의 가공연산은 일어나지 않았다.
  - 스트림은 미래에 수행할 흐름(stream)을 미리 정해놓고, 최종연산을 통해 그 흐름을 실행하는 개념을 갖는다는걸 알 수 있다.

---

   3. null-safe stream
      - Optional 클래스를 사용해  npe를 방지할 수 있다.
```java
List<String> list = Arrays.asList("a", "b", "c");
  Stream<String> stream =
    Optional.ofNullable(list)
            .map(Collection::stream)
            .orElseGet(Stream::empty);
```
