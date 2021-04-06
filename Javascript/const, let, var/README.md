# const, let, var

## 1. var
- 재선언이 가능하다.
- 재할당이 가능하다.
- **블록 단위가 아니다.**
```js
var text = "hello world!"
text = "hello"
var text = "hi!"
console.log(text) // hi!
```


## 2. let
- 재선언이 불가능하다.
- 재할당이 가능하다
- **블록 단위다.**
```js
let text = "hello world!"
text = "hello"
let text = "hi!" // Uncaught SyntaxError: Identifier 'text' has already been declared
```


## 3. const
- 재선언이 불가능하다.
- 재할당이 불가능하다.
- **블록 단위다.**
```js
const text = "hello world!"
text = "hello" // Uncaught SyntaxError: Identifier 'text' has already been declared
```

## 호이스팅
- 호이스팅이 발생하면 var는 초기 값을 undefined로 할당하지만 let, const는 초기 값을 할당하지 않는다. 
