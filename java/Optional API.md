# Java 8 Optional API 관련
참고 : [daleseo](http://www.daleseo.com/java8-optional-before/)
/
[Eric Han's IT Blog](https://futurecreator.github.io/2018/08/14/java-8-optional/)

### Optional의 기본 컨셉
- null의 래퍼클래스.
- 값이 null이더라도 NPE를 발생시키지 않도록 한다.
- Optional을 최대 1개의 원소를 갖는 Stream이라고 생각하면 편하다.
- 서로 사용법과 기본개념, 철학이 매우 유사하기 때문

---

- java8 이전
```java
List<String> source = getList();
List<String> list = source != null ? source : new ArrayList();
```
- java8 이후
```java
List<String> listOpt =
  Optional.ofNullable(getList())
          .orElseGet(() -> new ArrayList());
```

---

- Optional의 잘못된 사용
- 아래 두 코드의 차이는?
```java
int length;
Optional<List> maybeList = Optional.ofNullable(getList());
if(maybeList.isPresent()) {
    length = maybeList.get().size();
} else {
    length = 0;
}
```
```java
int length;
List<String> maybeList2 = getList();
if(maybeList2 != null) {
    length = maybeList2.size();
} else {
    length = 0;
}
```
---
### 우문현답
```
Optional 적용 후 null 체크는 어떻게 해야 하나요?
    -> null 체크를 할 필요가 없으니 안하면 된다.
```

---

### ifPresent() 메소드
- Optional 객체가 감싸는 값이 존재할 경우에만 실행될 로직을 넘긴다.
- 있을때만 실행되는 콜백함수와 유사
```java
Optional<List> maybeList3 = Optional.ofNullable(getList());
maybeList3.ifPresent(el -> System.out.println("list존재.. 길이 : " + el.size()));
```