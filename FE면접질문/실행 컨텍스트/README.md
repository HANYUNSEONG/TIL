# 실행 컨텍스트 (Execution context)

자바스크립트의 호이스팅, 스코프, 클로저와 같은 개념을 이해를 하려면 실행 컨텍스트(Execution context)와 실행 스택(call stack)에 대한 이해가 필요합니다.

## 실행 컨텍스트란?
간단히 말하면 자바스크립트의 코드가 실행되기 위한 환경의 추상적인 개념입니다.

3가지 실행 컨텍스트가 있습니다.
1. Global Execution Context (전역 실행 컨텍스트)  
전역 실행 컨텍스트는 단 1개만 있으며 기본 실행 컨텍스트로 함수 내에 없는 코드는 모두 전역 컨텍스트에 있습니다.  
window 전역 객체를 만들고 값을 this와 동일하게 만듭니다.
2. Functional Execution Context (함수 실행 컨텍스트)  
함수가 호출될 때마다 해당 함수에 대한 실행 컨텍스트가 생성됩니다.
3. Eval Context  
eval은 비권장 함수이기 때문에 해당 글에서는 다루지 않겠습니다.


## Call Stack (실행 스택)
코드 실행 중 생성된 모든 실행 컨텍스트를 저장하는데 사용하는 LIFO(Last in, First out) 구조가 있는 스택입니다.  


![ec_1](https://user-images.githubusercontent.com/58321856/119431689-f8d87980-bd4d-11eb-98a3-c312fccd626e.png)

자바스크립트 엔진은 실행 컨텍스트를 생성하고 생성된 실행 컨텍스트를 실행 스택으로 넣습니다.  
함수 호출을 할 때 실행 컨텍스트를 생성하고 이를 스택의 맨 위로 넣습니다. 이때 완료된 함수는 실행 스택에서 제거됩니다.


## 실행 컨텍스트 생성
실행 컨텍스트는 생성 단계 - 실행 단계 이렇게 두 단계로 생성됩니다.

### 창조 단계
1. Lexical Environment 생성
2. Variable Environment 생성

#### 1. Lexical Environment
자바스크립트에서 변수와 함수를 정의하는데 사용하는 객체입니다.  
Lexical Environment는 식별자와 참조 혹은 값을 기억하는 Environment Record와 outer라는 또 다른 Lexical Environment를 참조하는 포인터로 구성됩니다.  
outer는 외부 Lexical Environment를 참조하는 포인터로 중첩된 자바스크립트 코드에서 스코프를 탐색하기 위해 사용됩니다.

아래는 Environment Record와 outer를 쉽게 이해하기 위한 예제입니다.  
*(실제로 이렇게 단순하게 동작하진 않지만 개념을 쉽게 이해할 수 있습니다.)*
```js
function foo() {
  const a = 1;
  const b = 2;
  const c = 3;
  function bar() {}
}

foo();
```

```
{
  environmentRecord: {
    a: 1,
    b: 2,
    c: 3,
    bar: <Function>
  },
  outer: foo.[[Environment]]
}
```

#### 2. Variable Environment
이 단계에서 모든 변수에 할당이 완료가 되고 코드가 최종적으로 실행됩니다.

## 정리
- 실행 컨텍스트의 스택은 LIFO(후입선출) 자료 구조입니다.
- 전역 컨텍스트은 하나만 존재합니다.
- 함수 컨텍스트는 함수를 호출할 때 마다 생성되며 새롭게 만들어진 컨텍스트는 기존에 있던 컨텍스트의 위로 올라갑니다. 그리고 함수가 실행이 완료되면 컨텍스트는 사라집니다.



## ✅ 참고
[Understanding Execution Context and Execution Stack in Javascript](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)  
[NHN Cloud  - 자바스크립트 함수(3) - Lexical Environment](https://meetup.toast.com/posts/129)
