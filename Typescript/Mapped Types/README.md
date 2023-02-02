# Mapped Types - 맵드 타입

## Mapped Types란?

기존 타입을 다른 타입으로 변환하여 사용할 수 있도록 한다.

```ts
type UserConfig {
  id: string;
  password: string;
  name: string;
}

type EditUserConfig {
  id: boolean;
  password: boolean;
  name: boolean;
}
```

위 예제 코드에서 `UserConfig`는 사용자 설정에 대한 타입을 정의했고 `EditUserConfig`는 사용자 설정에 대한 수정 가능 여부에 대한 타입을 정의했다.  
여기서 알 수 있는건 `UserConfig`와 `EditUserConfig`는 암묵적으로 연관 되어있는 타입이라는 것이다.  
왜냐? 사용자 설정에 대한 타입이 추가되면 수정 가능 여부에 대한 타입이 추가될 가능성이 있기 때문이다.

예제 코드처럼 작성하면 유지보수성이 매우 떨어질 것이다. `UserConfig`에 추가하면 `EditUserConfig`에도 동일하게 추가를 해줘야 하기 때문에 휴먼 에러가 발생할 수 있다.

아래 예제를 보면서 조금 더 맵드 타입을 이해해보자

---

## 간단한 예제 1

```ts
type UserConfig {
  id: string;
  password: string;
  name: string;
}

type EditUserConfig = {
  [Property in keyof UserConfig]: boolean
}

const userConfig: UserConfig = {
  id: 'userId',
  password: 'userPassword',
  name: 'user01'
};

const editUserConfig: EditUserConfig = {
  id: false,
  password: true,
  name: true
}
```

이렇게 작성하면 어떤가 `UserConfig`에 다른 속성을 추가해도 이전과 다르게 `EditUserConfig`에 개발자가 직접 추가하지 않아도 된다.

## 간단한 예제 2

```ts
type LocationCategory = "country" | "area" | "etc";
type LocationValue = {
  [key in Location]: string;
};
```

`LocationCategory`에는 지역 데이터에 해당할 수 있는 카테고리를 정의했다.  
그리고 `LocationValue`에는 지역 데이터에 해당할 수 있는 카테고리에 대한 값의 타입을 정의했다.

위 코드에 `[key in Location]`은 javascript의 [for in](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...in) 문법과 유사하게 동작한다.
`LocationCategory`에서 정의한 타입을 순회하며 `string`을 값으로 가지는 객체가 된다.

`LocationValue` 타입을 풀어서 보면 아래와 같다고 볼 수 있다.

```ts
type LocationValue = {
  country: string;
  area: string;
  etc: string;
};
```

## Typescript 내장 유틸리티 타입으로 알아보기

Typescript에 [`Pick`](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys), [`Partial`](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype), [`Readonly`](https://www.typescriptlang.org/docs/handbook/utility-types.html#readonlytype) 3가지 유틸리티 타입으로 Mapped Types를 이해해보자

### Pick

[`Pick`](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)은 정의되어 있는 타입에서 키를 선택하여 또 다른 타입을 구성할 수 있는 유틸리티 타입이다.

```ts
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

### Partial

[`Partial`](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)은 정의되어 있는 타입을 모두 다 옵셔널하게 만들어주는 유틸리티 타입이다.

```ts
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

### Readonly

[`Readonly`](https://www.typescriptlang.org/docs/handbook/utility-types.html#readonlytype)는 정의되어 있는 타입을 모두 다 readonly 상태로 만들어주는 유틸리티 타입이다.

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};
```

## 참고

- [타입스크립트 핸드북 - 맵드 타입](https://joshua1988.github.io/ts/usage/mapped-type.html#%EB%A7%B5%EB%93%9C-%ED%83%80%EC%9E%85-%EC%8B%A4%EC%9A%A9-%EC%98%88%EC%A0%9C-2)
- [logrocket - typescript-mapped-types](https://blog.logrocket.com/typescript-mapped-types/)
