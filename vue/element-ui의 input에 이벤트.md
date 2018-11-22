# [Element-ui](http://element.eleme.io/#/en-US)를 사용할 때 폼태그에 기본 이벤트를 거는 방법

```html
<el-input type="text" @keyup.enter="enterAction"></el-input>
```

- 이렇게 하면 될거 같지만 안되더라..

```html
<el-input type="text" @keyup.enter.native="enterAction"></el-input>
```
- 이렇게 할것.