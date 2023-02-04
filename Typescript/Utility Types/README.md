# Utility Types - 유틸리티 타입

- [Utility Types - 유틸리티 타입](#utility-types---유틸리티-타입)
  - [Utility Types?](#utility-types)
  - [`Partial<T>`](#partialt)
  - [`Required<T>`](#requiredt)
  - [`Readonly<T>`](#readonlyt)
  - [`Record<Keys, T>`](#recordkeys-t)
  - [`Pick<T, K>`](#pickt-k)
  - [`Omit<T, K>`](#omitt-k)
  - [`Exclude<T, U>`](#excludet-u)
  - [`Extract<T, U>`](#extractt-u)
  - [`NonNullable<T>`](#nonnullablet)

## Utility Types?

Typescript는 타입 변환을 쉽게 하기 위해 전역적으로 사용이 가능한 내장된 유틸리티 타입을 제공한다.  
하나씩 알아보자

---

## `Partial<T>`

T의 모든 프로퍼티를 선택적으로 만들어준다.  
주어진 타입의 모든 하위 집합 타입을 반환한다.

```ts
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

**예제**

```ts
interface User {
  name: string;
  age: number;
}

const updateUser = (user: User, fields: Partial<User>) => {
  return { ...user, ...fields };
};

const user = {
  name: "한윤성",
  age: 22,
};

const user2 = updateUser(user, {
  age: 23,
});
```

## `Required<T>`

T의 모든 프로퍼티를 필수로 설정된 타입을 생성한다.  
-> [`Partial<T>`](#partialt)와 반대

```ts
type Required<T> = {
  [P in keyof T]-?: T[P];
};
```

**예제**

```ts
interface Props {
  a?: string;
  b?: number;
}

const props1: Props = {
  a: "string",
};

// Property 'b' is missing in type '{ a: string; }' but required in type 'Required<Props>'
const props2: Required<Props> = {
  a: "string",
};
```

## `Readonly<T>`

T의 모든 프로퍼티를 [읽기 전용(readonly)](https://radlohead.gitbook.io/typescript-deep-dive/type-system/readonly)으로 설정된 타입을 생성한다.  
생성된 타입의 프로퍼티는 재할당될 수 없다.

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};
```

**예제**

```ts
interface User {
  email: string;
  name: string;
}

const user: Readonly<User> = {
  email: "hanyunseong.dev@gmail.com",
  name: "한윤성",
};

// Cannot assign to 'name' because it is a read-only property.
user.name = "루카스";
```

## `Record<Keys, T>`

타입 T의 프로퍼티의 집합 K로 타입을 생성한다.  
타입의 프로퍼티를 다른 타입에 매핑 시키는데 사용할 수 있다.

```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
};
```

**예제**

```ts
interface AirlineInfo {
  name: string;
}

type Airline = "KE" | "OZ";

const airlineInfo: Record<Airline, AirlineInfo> = {
  KE: {
    name: "대한항공",
  },
  OZ: {
    name: "아시아나항공",
  },
};
```

## `Pick<T, K>`

T에서 프로퍼티 K의 집합을 선택해 타입을 생성한다.

```ts
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

**예제**

```ts
interface Todo {
  title: string;
  description: string;
  isCompleted: boolean;
}

type TodoPreview = Pick<Todo, "title" | "isCompleted">;
const todo: TodoPreview = {
  title: "제육볶음 만들기",
  isCompleted: true,
};
```

## `Omit<T, K>`

T에서 모든 프로퍼티를 선택하고 K를 제거한 타입을 생성한다.

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

**예제**

```ts
interface Todo {
  title: string;
  description: string;
  isCompleted: boolean;
}

type TodoPreview = Omit<Todo, "description">;
const todo: TodoPreview = {
  title: "제육볶음 만들기",
  isCompleted: true,
};
```

## `Exclude<T, U>`

U에서 할당할 수 있는 모든 속성을 T에서 제외하여 타입을 생성한다.

```ts
type Exclude<T, U> = T extends U ? never : T;
```

**예제**

```ts
type T0 = Exclude<"a" | "b" | "c", "a">;
// 'b' | 'c'

type T1 = Exclude<"a" | "b" | "c", "a" | "b">;
// 'c'

type T2 = Exclude<string | number | (() => void), Function>;
// string | number
```

## `Extract<T, U>`

U에 할당할 수 있는 모든 속성을 T에서 가져와 타입을 생성한다.

```ts
type Extract<T, U> = T extends U ? T : never;
```

**예제**

```ts
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
// 'a'

type T1 = Extract<string | number | (() => void), Function>;
// () => void
```

## `NonNullable<T>`

T에서 null과 undefined를 제외하고 타입을 생성한다.

```ts
type NonNullable<T> = T & {};
```

**예제**

```ts
type T0 = NonNullable<string | null>;
// string

type T1 = NonNullable<number[] | null>;
// number[]
```
