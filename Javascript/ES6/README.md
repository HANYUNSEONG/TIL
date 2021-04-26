# ES6 (ECMAScript2015)

## ES6란?
- ES6(ECMAScript) 표준의 6번째 버전
- Ecma International이 ECMA-262 기술 규격에 따라 정의하고 있는 표준화된 스크립트이고 그 중 ES6는 2015년에 나왔습니다.


## 주요 기능 맛보기
### 1. let, const
- 블록 스코프입니다.
```js
let text = "hello world!"
text = "hello"
let text = "hi!" // Uncaught SyntaxError: Identifier 'text' has already been declared
```
- const는 변수에 값을 재할당하지 못합니다.
```js
const text = "hello world!"
text = "hello" // Uncaught SyntaxError: Identifier 'text' has already been declared
```


### 2. 기본 매개 변수 (Default Parameters)
```js
function test(a = 1) {
  console.log(a)
}

test()  // 1
test(2) // 2
```


### 3. 템플릿 리터럴
```js
let name = "Han"
console.log(`Hello ${name}!`); // Hello Han!
```


### 4. 비구조화 할당
- 배열
```js
const [seoul, incheon] = ["Seoul", "Incheon"]
```
- 나머지 패턴
```js
const city = ["Seoul", "Incheon", "Jeju", "Busan"];
const [seoul, ...citys] = city

console.log(seoul) // Seoul
console.log(citys) // ["Incheon", "Jeju", "Busan"]
```


- 오브젝트
```js
const { seoul, incheon, jeju } = {
  seoul: "Seoul",
  incheon: "Incheon",
  jeju: "Jeju",
}
```
- 나머지 패턴
```js
const { seoul, ...citys } = {
  seoul: "Seoul",
  incheon: "Incheon",
  jeju: "Jeju",
}

console.log(seoul) // Seoul
console.log(citys) // {incheon: "Incheon", jeju: "Jeju"}
```


### 5. 화살표 함수
- function 키워드를 사용하지 않고 함수를 만들 수 있습니다.
- 화살표 함수는 항상 **익명**입니다.
```js
const arr = [1, 2, 3];
const pow = arr.map(x => x * 2);

console.log(pow); // [2, 4, 6]
```


### 6. Promise
- ES6 이전에도 사용이 가능했지만 ES6에서는 공식으로 지원합니다.
```js
const fetchData = new Promise ((resolve, reject) => {
  try {
    // 비동기 작업
    resolve(결과)
  } catch (error) {
    reject(error)
  }
})

fetchData.then((result) => {
  console.log(result)
}).catch((error) => {
  console.log(error)
})
```


### 7. 모듈
```js
// config.js
export const KEY = "Key"

// main.js
import { KEY } from './config.js'
console.log(KEY)
```


## ES6 지원 브라우저
- [지원 브라우저 확인 사이트](http://kangax.github.io/compat-table/es6/)
![image](https://user-images.githubusercontent.com/58321856/116017934-d8000400-a67b-11eb-88b7-9fd0885990c2.png)

- 아직 모든 브라우저에서 ES6를 지원하지 않는다. 하지만 Babel를 사용하면 지원하지 않는 브라우저에서도 사용이 가능합니다.
- babel은 ES6+ 버전을 ES5로 바꿔주는 트랜스파일러입니다.
