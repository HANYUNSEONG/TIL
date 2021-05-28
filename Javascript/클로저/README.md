# 클로저 (Closure)

## 클로저란?
[MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)에서는 클로저를 **함수와 함수가 선언된 어휘적 환경의 조합** 이라고 정의하고 있습니다.  
간단하게 말하면 함수 내에 함수를 정의하고 사용하면 클로저라고 합니다.  
함수가 생성될 때 그 함수의 렉시컬 환경을 포섭하여 실행할 떄 이용됩니다.

---
```js
function foo() {
    var color = 'blue';
    function bar() {
        console.log(color);
    }
    bar();
}
foo();
```
위 코드에 bar 함수는 우리가 부르는 클로저라고 하기엔 거리가 있습니다.  
왜냐하면 bar는 foo 안에서 실행될 뿐 foo 밖으로 나오지 않기 때문입니다.

```js
var color = 'red';
function foo() {
    var color = 'blue';
    function bar() {
        console.log(color);
    }
    return bar;
}
var baz = foo();
baz();
```
위 코드는 우리가 부르는 클로저를 나타내고 있습니다.
1. bar 함수는 color 변수를 출력하는 함수입니다.
2. bar()는 외부에 foo의 환경을 기억합니다.
3. var baz = foo()로 bar 함수를 Global에 가져왔습니다.
4. bar()는 본인의 스코프에서 color를 찾지만 없기 때문에 외부 환경에 있는 color 값인 'blue'를 찾았습니다.
5. 때문에 baz(); 의 실행 결과는 blue 입니다.

#### 이게 클로저다. 조금 자세히 살펴보면
bar는 본인이 생성된 렉시컬 스코프를 벗어나 Global에 baz로 호출이 되었고  
bar()에서 color를 찾아가는 과정에서는 전혀 상관없는 foo()를 찾아갔습니다.  
bar가 baz로 Global에 정의를 할 때 bar는 이미 외부 스코프의 환경 즉 foo의 환경을 기억하고  
그로 인해 color는 blue가 됩니다. 이와 같은 함수를 우리는 클로저라고 부릅니다.



## ✅ 참고
- [NHN Cloud Meetup](https://meetup.toast.com/posts/86)
- [MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
