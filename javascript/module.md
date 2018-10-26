# 다양한 모듈 패턴 (ES5)

- 모듈패턴이라는 명칭이 맞는지는 잘 모르겠다.
- revealing pattern 이라고 하는것도 같다..

##  1. 오브젝트 리터럴을 이용한 방식
```javascript
var Book = {
    title: "title",             // public
    showAuthor: function() {    // public
        console.log("Steve Jobs")
    },
    showInfo: function() {      // public
        this.showAuthor();
        console.log(this.title);
    }
}

Book.showInfo();
```

## 2. 함수를 이용하는 방법 - 생성자 함수에서 return문이 없는 경우
```javascript
var Book = function() {
    var title = "title";            // private
    var showAuthor = function() {   // private
        console.log("Steve Jobs");
    };
    this.showInfo = function() {    // public
        showAuthor();
        console.log(title);
    }
}

var myBook = new Book();
myBook.showInfo();
```

## 3. 함수와 프로토타입을 이용하는 방법
```javascript
var Book= function() {
    this.title = "title";           // public
    this.showAuthor = function() {  // public
        console.log("Steve Jobs");
    };
}

// myBook으로는 private하나 prototype으로 접근가능
Book.prototype.showInfo = function() {
    this.showAuthor();
    console.log(this.title);
}

var myBook = new Book();
myBook.showInfo();
```

## 4. 함수를 이용하는 방법 - 생성자 함수에서 return값이 있음
```javascript
var Book = function() {
    var title = "title";            // private
    var showAuthor = function() {   // private
        console.log("Steve Jobs");
    };

    // return객체를 통해서만 접근이 가능
    return {
        showInfo: function() {      // public
            showAuthor();
            console.log(title);
        }
    }
}

var myBook = new Book();
myBook.showInfo();
```
