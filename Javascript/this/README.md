# Javascript this

Javascript의 this 키워드에 대해서 정리하자

## 전역 문맥 (기본 바인딩)

첫번째로 크롬 개발자 도구를 열어서 냅다 this를 찍어보자

```js
console.log(this);
```

그럼 Window 객체가 출력되는 걸 볼 수 있다.  
자 저건 바로 Javascript 실행 환경의 전역 객체이다.

아래 코드를 보자

```js
let name = "hanyunseong";
console.log(this.name); // hanyunseong
```

전역 실행 맥락에서의 this는 엄격 여부와 상관없이 모두 전역 객체를 참조한다.

---

## 함수 내의 this

```js
function run() {
  this.age = 21;
  console.log(this);
}

console.log(this.age); // undefined

run();

console.log(this.age); // 21
```

비엄격 모드일 때 함수 내의 this도 전역 객체인 Window를 참조한다.

---

## 엄격 모드 함수 내의 this

```js
"use strict";

function run() {
  console.log(this); // undefined
  this.age = 21; // Error!
}
```

하지만 엄격 모드에서는 함수 내의 this에 기본 바인딩이 없기 때문에 함수에서 this를 출력해보면 undefined가 나온다.

---

## 함수가 아닌 메서드에서의 this (암시적 바인딩)

```js
const person = {
  name: "lucas",
  age: 21,
  getInfo: function () {
    return `${this.name}(${this.age})`;
  },
};

console.log(person.getInfo()); // lucas(21)
```

메서드 내의 함수에서의 this가 참조하는 건 전역 객체가 아닌 함수를 호출한 객체가 this의 context가 된다.  
즉 위에 코드에서는 getInfo라는 함수를 호출한 person이라는 객체에 this가 바인딩이 되어  
name, age가 person 객체 내부에 있는 값이 출력된다.

```js
const person = {
  name: "lucas",
  age: 21,
  getInfo: function () {
    age = 22;
    return `${this.name}(${this.age})`;
  },
};
```

여기서 person.getInfo()를 호출하면 어떤 값이 호출될까?  
정답은 lucas(21)이 출력된다.  
이유는 getInfo에 age = 22는 person에 age의 값을 바꾸는 게 아닌 getInfo 함수 내부에  
age라는 이름의 새로운 값? 을 생성한거나 다름없다고 볼 수 있다.

위 코드가 기대하던 결과를 출력하려면 다음과 같이 해야 한다.

```js
const person = {
  name: "lucas",
  age: 21,
  getInfo: function () {
    this.age = 22;
    return `${this.name}(${this.age})`;
  },
};

console.log(person.getInfo()); // lucas(22)
```

---

## 참고한 사이트

- [MDN - this](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this)
- [모던 자바스크립트 - 메서드와 this](https://ko.javascript.info/object-methods)
- [nana_blog - 자바스크립트에서의 this](https://nykim.work/71)
- [자바스크립트 this의 4가지 동작 방식](https://yuddomack.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-this%EC%9D%98-4%EA%B0%80%EC%A7%80-%EB%8F%99%EC%9E%91-%EB%B0%A9%EC%8B%9D)
