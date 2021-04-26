# 함수 표현식과 함수 선언식

## 함수 선언식
```js
function 함수명() {
  // 함수 내용
}
```

## 함수 표현식
```js
var 함수명 = function() {
  // 함수 내용
}
```

## 함수 선언식과 함수 표현식의 차이점
- 함수 선언식은 **호이스팅**에 영향을 받지만 함수 표현식은 영향을 받지 않습니다.
```js
alert(test());  // 정상 동작
function test() {
  return "Hello"
}

alert(test());  // 에러 발생! Uncaught TypeError: test is not a function
var test = function() {
  return "Hello";
}
```

## 참고
- [함수 표현식 / MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/function)
