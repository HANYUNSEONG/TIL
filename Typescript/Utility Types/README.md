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
  - [`Parameters<T>`](#parameterst)
  - [`ConstructorParameters<T>`](#constructorparameterst)
  - [`ReturnType<T>`](#returntypet)
  - [`InstanceType<T>`](#instancetypet)
  - [`ThisParameterType<T>`](#thisparametertypet)
  - [`OmitThisParameter<T>`](#omitthisparametert)
  - [`ThisType<T>`](#thistypet)
  - [내장 문자열 조작 타입](#내장-문자열-조작-타입)
    - [`Uppercase<StringType>`](#uppercasestringtype)
    - [`Lowercase<StringType>`](#lowercasestringtype)
    - [`Capitalize<StringType>`](#capitalizestringtype)
    - [`Uncapitalize<StringType>`](#uncapitalizestringtype)
  - [참고](#참고)

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

## `Parameters<T>`

함수 타입 T의 매개변수에 사용된 타입에서 튜플 타입을 생성한다.

**예제**

```ts
declare function f1(arg: { a: number; b: string }): void;
type T0 = Parameters<() => string>; // []
type T1 = Parameters<(s: string) => void>; // [string]
type T2 = Parameters<<T>(arg: T) => T>; // [unknown]
type T4 = Parameters<typeof f1>; // [{ a: number, b: string }]
type T5 = Parameters<any>; // unknown[]
type T6 = Parameters<never>; // never
type T7 = Parameters<string>; // 오류
type T8 = Parameters<Function>; // 오류
```

## `ConstructorParameters<T>`

생성자 함수 타입의 모든 매개변수 타입을 추출할 수 있게 해준다.  
모든 매개변수 타입을 가지는 튜플 타입을 생성한다.

> T가 함수가 아닌 경우 never를 생성한다.

**예제**

```ts
type T0 = ConstructorParameters<ErrorConstructor>; // [(string | undefined)?]
type T1 = ConstructorParameters<FunctionConstructor>; // string[]
type T2 = ConstructorParameters<RegExpConstructor>; // [string, (string | undefined)?]
```

## `ReturnType<T>`

함수 T의 반환 타입으로 구성된 타입을 생성한다.

**예제**

```ts
declare function f1(): { a: number; b: string };
type T0 = ReturnType<() => string>; // string
type T1 = ReturnType<(s: string) => void>; // void
type T2 = ReturnType<<T>() => T>; // {}
type T3 = ReturnType<<T extends U, U extends number[]>() => T>; // number[]
type T4 = ReturnType<typeof f1>; // { a: number, b: string }
type T5 = ReturnType<any>; // any
type T6 = ReturnType<never>; // any
type T7 = ReturnType<string>; // 오류
type T8 = ReturnType<Function>; // 오류
```

## `InstanceType<T>`

생성자 함수 타입 T의 인스턴스 타입으로 구성된 타입을 만든다.

**예제**

```ts
class C {
  x = 0;
  y = 0;
}

type T0 = InstanceType<typeof C>; // C
type T1 = InstanceType<any>; // any
type T2 = InstanceType<never>; // any
type T3 = InstanceType<string>; // 오류
type T4 = InstanceType<Function>; // 오류
```

## `ThisParameterType<T>`

함수 타입의 this 매개변수의 타입, 또는 함수 타입에 this 매개변수가 없을 경우 unknown을 추출한다.

**예제**

```ts
function toHex(this: Number) {
  return this.toString(16);
}

function numberToString(n: ThisParameterType<typeof toHex>) {
  return toHex.apply(n);
}
```

## `OmitThisParameter<T>`

T에서 this 매개변수를 제거한다.

```ts
function toHex(this: Number) {
  return this.toString(16);
}

// `bind`의 반환 타입은 이미 `OmitThisParameter`을 사용하고 있습니다, 이는 단지 예제를 위한 것입니다.
const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5);

console.log(fiveToHex());
```

## `ThisType<T>`

이 유틸리티 타입은 변형된 타입을 반환하지 않고 대신 문맥적 this 타입에 표시하는 역할을 한다.

> 이 유틸리티를 사용하기 위해선 `--noImplicitThis` 플래그를 사용해야 한다.

```ts
// --noImplicitThis 로 컴파일

type ObjectDescriptor<D, M> = {
  data?: D;
  methods?: M & ThisType<D & M>; // 메서드 안의 'this 타입은 D & M 입니다.
};

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  let data: object = desc.data || {};
  let methods: object = desc.methods || {};
  return { ...data, ...methods } as D & M;
}

let obj = makeObject({
  data: { x: 0, y: 0 },
  methods: {
    moveBy(dx: number, dy: number) {
      this.x += dx; // 강하게 타입이 정해진 this
      this.y += dy; // 강하게 타입이 정해진 this
    },
  },
});

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

위 예제에서 makeObject의 인자로 넘겨지는 methods 객체는 `ThisType<D & M>`을 포함한 문맥적 타입을 가지고 있다.  
따라서 methods 객체의 메서드 안에 this의 타입은 `{ x: number, y: number } & { moveBy(dx: number, dy: number): number }`다.  
methods 프로퍼티의 타입이 추론의 대상이며 동시에 메서드 안의 this 타입의 출처이다.

## 내장 문자열 조작 타입

문자열 조작을 돕기 위해서 Typescript에는 문자열 조작에 사용할 수 있는 유틸리티 타입들이 있다.

### `Uppercase<StringType>`

문자열의 각 문자를 대문자로 변환한다.

```ts
type Greeting = "Hello, world";
type ShoutyGreeting = Uppercase<Greeting>;
// "HELLO, WORLD"
```

### `Lowercase<StringType>`

문자열의 각 문자를 소문자로 변환한다.

```ts
type Greeting = "Hello, world";
type ShoutyGreeting = Lowercase<Greeting>;
// "hello, world"
```

### `Capitalize<StringType>`

문자열의 첫 번재 문자를 대문자로 변환한다.

```ts
type Greeting = "hello, world";
type ShoutyGreeting = Capitalize<Greeting>;
// "Hello, world"
```

### `Uncapitalize<StringType>`

문자열의 첫 번재 문자를 소문자로 변환한다.

```ts
type Greeting = "Hello, world";
type ShoutyGreeting = Uncapitalize<Greeting>;
// "hello, world"
```

## 참고

- [typescript handbook - ko](https://www.typescriptlang.org/ko/docs/handbook/utility-types.html)
- [typescript-kr - 유틸리티 타입](https://typescript-kr.github.io/pages/utility-types.html)
