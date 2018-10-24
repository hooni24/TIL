# AOP 간단개념

- Aspect oriented Programming
- 관점지향 프로그래밍.
- 프로세스를 바라보는 관점을 바꾼다고 생각하면 쉽다.<br>
ex) 트랜잭션

## 예제

```java
class Student {
    void schoolLife () {
        [[ 등교 ]]
        수업을 듣는다
        [[ 하교 ]]
    }
}

class Teacher {
    void schoolLife () {
        [[ 등교 ]]
        수업을 한다
        [[ 하교 ]]
    }
}
```
- 위와 같은 프로세스를 종(縱)으로 보면 등교와 하교가 공통되고.. 이걸 공통화 시키고 싶어 진다..
- 슈퍼클래스를 만들어서 schoolLife를 추상메서드로 만들어 처리할 수도 있겠지만.. 이런 경우 AOP를 사용해 처리할 수 있다.


```java
class Student {
    void schoolLife () {
        수업을 듣는다
    }
}

class Teacher {
    void schoolLife () {
        수업을 한다
    }
}

class Aspect {
    void 등하교 (JoinPoint joinPoint) {
        등교
        joinPoint.execute();
        하교
    }
}
```

### 추가로 참고하면 좋은 사이트
[jojoldu님 블로그 AOP 정리](https://jojoldu.tistory.com/71)