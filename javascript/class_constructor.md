# ES6 class문법 constructor 수행순서 관련

```javascript
class Foo {
    constructor() {
        this.init();
        this.foo = null;
        this.bar = null;
    }

    init() {
        this.foo = "foo";
        this.bar = "bar";
    }
}
```

- 위와 같은 클래스 선언이 있는 경우, `init()`으로 인해 `foo`,`bar`가 초기화되나, 다시 null 대입으로 인해 값이 사라지게 된다.

- 바람직한 방법은 아래와 같다
```javascript
class Foo {
    constructor() {
        this.foo = null;
        this.bar = null;
        this.init();
    }

    init() {
        this.foo = "foo";
        this.bar = "bar";
    }
}
```

- 당연한 이야기일 수도 있지만.. 자바 클래스랑 혼동될 경우가 있을 수도 있을거 같다.
- `this.foo = null;` 구문은 변수선언구문이 아니라 변수초기화 구문이라는 것.
