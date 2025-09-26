---
title: typeChallenges
date: 2017-05-21 11:23:51
categories: js #文章分类
tags: [typeChallenges] #文章标签，可以一次添加多个标签
---

type chanllenges...

<!-- more -->

### 类型体操,类型编程

TS 的类型系统是图灵完备的，这意味着它是可以用来进行逻辑运算，也就是我们戏称的类型体操

值空间和类型空间
在 TS 中，存在两个空间：值空间和类型空间。值空间用于存放会被编译成 JS 的实体内容，而类型空间用于存放各种类型信息，且这些信息会在编译后被完全擦除。两个空间彼此联系，但又互不影响。

- 泛型基础
- 索引类型&映射类型
- 条件类型&分布式条件类型
- infer 关键字

### simple tips

- keyin
  keyin 的方式和获取对象中属性的方式十分相似。并且 TS 只支持数组形式获取成员类型，不支持.语法。并且如果当前类型是**数组或者元组**，还可以通过/**[number]或者[1]/**进一步获取数组子项类型。

```ts
type FriendList = {
  count: number;
  friends: {
    firstName: string;
    lastName: string;
  }[];
};

type Friends = FriendList["friends"];
// 等价于
// type Friends = {
//   firstName: string;
//   lastName: string;
// }[];

type Friend = Friends[number];
// 等价于
// type Friend = {
//   firstName: string;
//   lastName: string;
// };
```

- keyof
  keyof 用于获取对象属性名，并用这些属性名构成联合类型
  keyof 操作的类型是类，则会获取该类所有的 public 属性名

```ts
type Friend = {
  firstName: string;
  lastName: string;
};

type Keys = keyof Friend; // "firstName" | "lastName"
class Person {
  public name: string;
  protected age: number;
  private sex: boolean;

  public foo() {}
  protected bar() {}
  private baz() {}
}

type Keys = keyof Person; // "name" | "foo"
```

- 擦除类型限制，通过+/-操作符

```ts
type RemoveReadonly<T> = {
  -readonly [P in keyof T]: T[P];
};
// Readonly 工具类型中到 readonly 等价于 +readonly，不过通常忽略不写，就像正数不写 + 前缀一样

type ReadonlyFriend = {
  readonly firstName: string;
  readonly lastName: string;
};

type Friend = RemoveReadonly<ReadonlyFriend>;
// 等价于
// type Friend = {
//     firstName: string;
//     lastName: string;
//   };
```

- TS 泛型的 extends 属性
  extends 的类型分发
  extends 前面的参数如果为裸联合类型时则会分解联合类型进行判断（依次遍历所有的子类型进行条件判断）。然后将最终的结果组成新的联合类型。

```ts
// Type 是裸类型，会进行分发
type NakedToArray<Type> = Type extends any ? Type[] : never;
type t1 = NakedToArray<string | number>; // string[] | number[];

// [Type] 不是裸类型，不会进行分发
type ToArray<Type> = [Type] extends [any] ? Type[] : never;
type t2 = ToArray<string | number>; // (string | number)[]
```

- never
  never 在类型编程中是一个十分有用的类型，它表示不存在的类型，所以在类型编程中任何包含 never 的类型都会将其中 never 忽略掉

```ts
type Exclude<T, U> = T extends U ? never : T;

type t1 = "a" | "b" | "c" | "d";
type t2 = "a" | "b";

// 按照分发特性，t3 应该是 never | never | "c" | "d"，而 never 会被省略，所以最终结果是 "c" | "d"
type t3 = Exclude<t1, t2>; // "c" | "d"
```

### 常见工具类型

- Partial<T> -- 将传入的属性由非可选变为可选 (对象类型)
- Pick<T,K extends keyof T> -- 从传入的属性中摘取某些返回 (对象类型,key 的联合类型)
- Exclude<T, U> -- 从 T 中剔除可以赋值给 U 的类型。(联合类型,具体类型 string)
- NonNullable<T> -- 从 T 中剔除 null 和 undefined。(含有 null 和 undefined 的联合类型)
- Extract<T, U> -- 提取 T 中可以赋值给 U 的类型。(联合类型，具体类型 string)
- ReturnType<T> -- 获取函数返回值类型。(function 定义)
- InstanceType<T> -- 获取构造函数类型的实例类型。(class)
- Parameters<T> -- 获取函数类型的参数类型 (function 定义)
- ConstructorParameters<T> -- 获取构造函数的参数类型 (function 定义)
- Readonly<T> -- 为传入的属性每一项都加上 readonly 修饰符来实现 (对象类型)
- Record<K,T> -- 具有一组属性 K，每个属性的类型为 T。可用于将一个类型的属性映射为另一个类型。(联合类型,对象类型)
- Omit<K,T> -- 基于已经声明的类型进行属性剔除获得新类型 (对象类型,keyName)

### 参考链接

[重学 ts 练习题](https://github.com/semlinker/awesome-typescript/issues)

[type-challenges](https://github.com/type-challenges/type-challenges)

### 示例

1. 实现 ts 内置的 Pick<T, K>

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = MyPick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

```ts
type MyPick<T, K extends keyof T> = {
  [key in K]: T[key];
};
```

2. 实现内置的 Readonly<T>

```
interface Todo {
    title: string
    description: string
}
const todo: MyReadonly<Todo> = {
    title: "hey",
    description: "foobar"
}
todo.title = "Hello" // Error: cannot reassign a readonly property
```

```ts
MyReadonly<T> = {
    readonly [k in keyof T]: T[k]
}
```

3. 元组转对象

```ts
const tuple = ["tesla", "model 3", "model X", "model Y"] as const;
type result = TupleToObject<typeof tuple>; // expected {tesla: 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

```ts
type TupleToObject<T extends readonly (string | number | symbol)[]> = {
  [P in T[number]]: P;
};
```

使用const来缩小到最具体的类型

```ts
let foo = { name: 'foo' }; // typed: { name: string }
let Bar = { name: 'bar' } as const; // typed: { name: 'bar' }

let a = [1, 2]; // typed: number[]
let b = [1, 2] as const; // typed: [1, 2]

// typed { kind: 'circle; radius: number }
let circle = { kind: 'circle' as const, radius: 1.0 };

// 如果circle没有使用const关键字进行初始化，则以下内容将无法正常工作
let shape: { kind: 'circle' | 'rect' } = circle;
```

4. 获取元组长度

```ts
type tesla = ["tesla", "model 3", "model X", "model Y"];
type spaceX = [
  "FALCON 9",
  "FALCON HEAVY",
  "DRAGON",
  "STAPSHIP",
  "HUMAN SPACEFLIGHT"
];
type teslaLength = Length<tesla>; // expected 4
type spaceXLength = Length<spaceX>; // expected 5
```

```ts
type Length<T> = T extends readonly any[] ? T["length"] : never;
```

5. 实现两个数的加法
   使用元组的特性：元组是包含了固定长度的数组，所以 TS 能确切的知道元组的长度

```ts
type getLength<R> = R extends any[] ? R["length"] : never;

type Hello = getLength<[1, 2, 3]>;
```

思路:

1. 构建长度为 T1 和 T2 的元组
2. 将元组合并，合并后的元组长度就是加法运算的结果

```ts
// 构建长度为T的元组
type GetTuple<T extends number, R extends any[] = []> = R["length"] extends T
  ? R
  : GetTuple<T, [...R, any]>;
type Add<T1 extends numbder, T2 extends numbder> = [
  ...GetTuple<T1>,
  ...GetTuple<T2>
]["length"];
type Five = Add<2, 3>;
```

6. 字符串字面量类型拆分为元组类型

```ts
type StrToTuple<T extends string> = T extends `${infer F}${infer L}`
  ? [F, ...StrToTuple<L>]
  : [];

type t1 = StrToTuple<"foo">; // ["f", "o", "o"]
```

7. 实现柯里化的类型定义

8. 联合转交叉
   在逆变位置上，同一类型变量的多个候选类型将会被推断为交叉类型：????

```ts
type UnionToIntersection<T> = (T extends any ? (x: T) => void : never) extends (
  x: infer R
) => void
  ? R
  : never;
type u1 = UnionToIntersection<"str" | string>; // string
type u2 = UnionToIntersection<"str1" | "str2">; // "str1" & "str2" ->never
```

9. 联合转元组

- 首先将 union 转化为函数类型 union
- 再利用 union 转 intersection 的特性，将函数 union 变成函数重载
- 最后利用重载类型推断时以最后一个为准，就能分离出最后一个 union 类型
- 剩下的 union 类型重复上述过程直到结束

```ts
type LastOfUnion<T> = UnionToIntersection<
  T extends any ? (x: T) => void : never
> extends (x: infer L) => void
  ? L
  : never;

type UnionToTuple<T, R extends any[] = []> = isNever<T> extends true
  ? R
  : UnionToTuple<Exclude<T, LastOfUnion<T>>, [LastOfUnion<T>, ...R]>;

type t1 = UnionToTuple<"1" | "2" | "3" | "4">; // ["1", "2", "3", "4"]
```

10. 实现 IsEqual
    [how does the `Equals` work in typescript](https://stackoverflow.com/questions/68961864/how-does-the-equals-work-in-typescript)

```ts
type IsEqual<T1, T2> = (<U>() => U extends T1 ? true : false) extends <
  U
>() => U extends T2 ? true : false
  ? true
  : false;

type e1 = IsEqual<{ name: string }, { name: string }>; // true
type e2 = IsEqual<{ name: string }, { readonly name: string }>; // false
type e3 = IsEqual<number, string>; // false
```
