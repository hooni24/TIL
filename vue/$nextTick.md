# $nextTick()

- vue 개발을 하다 보면.. 함수내에서 동시에 여러 작업을 할 때 하나가 씹히는 경우가 발생한다.
- 어느 경우 어느 맥락인지 아직 정확히 파악은 못 했으나.. 변수값을 변경하고 -> 엘리먼트에 focus를 주는 경우에는 거의 항상 일어난다.

### 이런 경우
```javascript
someFunction() {
    this.someData = {}
    this.$el.querySelector('#id').focus()
}
```
- 이럴땐 데이터만 바뀌고 focus가 가질 않는다..

### 이렇게 해결 가능
```javascript
someFunction() {
    this.someData = {}
    this.$nextTick(()=> this.$el.querySelector('#id').focus())
}
```


### 나중에 $nextTick()에 대해 좀 더 알아보고 추가로 정리하자..