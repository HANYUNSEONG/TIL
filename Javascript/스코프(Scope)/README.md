# 스코프 (Scope)
변수에 접근할 수 있는 범위라고 이해합니다.

## 스코프의 2가지 타입
### 1. 전역
전역 스코프는 전역에 선언이 되어있어 어느 곳에서든 변수에 접근이 가능합니다.
### 2. 지역
지역 스코프는 해당 지역에서만 접근할 수 있어 외부에서는 접근할 수 없습니다.

### MDN 예제
- 에러
```js
function exampleFunction() {
    var x = "declared inside function";
    // x는 오직 exampleFunction 내부에서만 사용 가능.
    console.log("Inside function");
    console.log(x);
}

console.log(x);  // 에러 발생
```
- 정상 실행
```js
var x = "declared outside function";

exampleFunction();

function exampleFunction() {
    console.log("Inside function");
    console.log(x);
}

console.log("Outside function");
console.log(x);
```

## ✅ 참고
[MDN - 스코프](https://developer.mozilla.org/ko/docs/Glossary/Scope)
