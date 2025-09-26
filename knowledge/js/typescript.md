---
title: typescript
date: 2017-05-21 11:23:51
categories: js #文章分类
tags: [typescript]
---

typescript...

<!-- more -->

### questions

1. type 和 interface 的用法

- type 和 interface 的区别

  type 的含义是定义自定义类型，当 TS 提供给你的基础类型都不满足的时候，可以使用 type 自由组合出你的新类型，而 interface 应该是对外输出的接口。
  他们都支持扩展另一个些接口或类型。
  类型别名通过并集类型(&)来实现，接口通过 extends 关键字。
  也就是说，我们建议您使用接口而不是类型别名，因为你可以在接口中获得更好的错误提示。
  一个接口和类型别名的主要区别是，接口是开放的，类型别名是封闭的。
  这意味着你可以你可以通过多次声明同一个接口来扩展它。
  与此同时，类型别名不可以在外部变更它的声明。
  type 不可以被继承，但 interface 可以

- interface 可以给 Function/Array/Class 做声明吗

```ts
interface Say {
  (name: string): void;
}
let say: Say = (name: string): void => {};

interface NumberArray {
  [index: number]: number;
}
let fibonacci: NumberArray = [1, 2, 3, 4];

interface Person {
  name: string;
  sayHi(name: string): string;
}
```

```ts
interface iPeople {
  name: string;
  age: number;
}

type T = keyof iPeople;
// -> "name" | "age"
```

```ts
type Keys = "a" | "b";
type Obj = {
  [p in Keys]: any;
};
// -> { a: any, b: any }
```

2. keyof 用来取得一个对象接口的所有 key 值

```ts
interface Person {
  name: string;
  age: number;
  gender: "male" | "female";
}
//type PersonKey = 'name'|'age'|'gender';
type PersonKey = keyof Person;

function getValueByKey(p: Person, key: PersonKey) {
  return p[key];
}
let val = getValueByKey({ name: "hello", age: 10, gender: "male" }, "name");
console.log(val);
```

3. function 的定义

```ts
// demo1
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
// demo2

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: { <T>(arg: T): T } = identity; // { <T>(arg: T): T } 带有调用签名的对象字面量
```

4. declare 可以创建 \*.d.ts 文件中的变量，declare 只能作用域最外层
   declare 是用来定义全局变量、全局函数、全局命名空间、js modules、class 等
   declare global 为全局对象 window 增加的新属性

```ts
declare global {
  interface Window {
    csrf: string;
  }
}
```

5. 属性名前用 readonly 来指定只读属性，只读类型的声明，适合纯函数

- const 和 readonly 的区别
- const 是编译时常量，readonly 是运行时常量，const 较高效，readonly 更灵活，在应用上一般都以 static readonly 来代替 const，以平衡 const 在灵活性上的不足，所以 static readonly 等效于 const。
- static readonly 允许在类的静态构造函数中进行赋值

```ts
let a: number[] = [1, 2, 3, 4];
// ReadonlyArray<T>是一个真正的只读数组，没有任何可以更改该数组的方法。
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!
```

6. 定义任意参数，除了两个可选参数，还包括任意个数的其他属性

```ts
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;
}
```

7. 键是 PropertyKey 类型，这是 Typescript 中预设的类型，指 string | number | symbol 的联合类型，也就我们平时用作键的类型

8. new( )作为接口的属性时也被称为“构造器字面量”

```ts
type Constructor = new (...args: any[]) => any;
```

9. <>类型断言和!. 类型断言
   as foo 与 <\foo>
   在 JSX 中使用 <\foo> 的断言语法时，这会与 JSX 的语法存在歧义
   为了一致性，建议使用 as foo 的语法来为类型断言

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
或者是用尖括号(<string>someValue);
```

如果编译器不能够去除 null 或 undefined，你可以使用类型断言手动去除。 语法是添加 !后缀： identifier!从 identifier 的类型里去除了 null 和 undefined

```ts
let flag: null | undefined | string;
flag!.toString(); // ok
flag.toString(); // error
```

10. 类型别名

类型别名会给一个类型起个新名字。 类型别名有时和接口很像，但是可以作用于原始值，联合类型，元组以及其它任何你需要手写的类型。

```ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
  if (typeof n === "string") {
    return n;
  } else {
    return n();
  }
}
```

11. never 类型的完整性检查

```ts
function assertNever(x: never): never {
  throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
  switch (s.kind) {
    case "square":
      return s.size * s.size;
    case "rectangle":
      return s.height * s.width;
    case "circle":
      return Math.PI * s.radius ** 2;
    default:
      return assertNever(s); // error here if there are missing cases
  }
}
```

```ts
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement {
  animate(dx: number, dy: number, easing: Easing) {
    if (easing === "ease-in") {
      // ...
    } else if (easing === "ease-out") {
    } else if (easing === "ease-in-out") {
    } else {
      // error! should not pass null or undefined.
    }
  }
}

let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy"); // error: "uneasy" is not allowed here
```

12. 索引类型查询操作符

```ts
interface Person {
  name: string;
  age: number;
}

type x = Person["name"]; // x is string
```

13. jquery.d.ts

```ts
declare const $: (selector: string) => {
  click(): void;
  width(length: number): void;
};
```

14. ts 中?.、??、!、!.、\_、\*\* 符号的含义

- ?.可选链遇到 null 和 undefined 可以立即停止表达式的运行。
  TypeScript 3.7 实现了呼声最高的 ECMAScript 功能之一：可选链（Optional Chaining）。有了可选链后，我们编写代码时如果遇到 null 或 undefined 就可以立即停止某些表达式的运行,直接返回 undefined，这也是我在 ts 使用中避免数据异常最常用的方法。可选链的核心是新的 ?. 运算符，它支持以下语法：

```ts
obj?.prop > obj?.[expr] > arr?.[index] > func?.(args);
```

- ??空值合并运算符当左侧操作数为 null 或 undefined 时，其返回右侧的操作数，否则返回左侧的操作数。
- !非空断言运算符 x！将从 x 值域中排出 null 和 undefined。
- !.在变量名后添加，可以断言排除 undefined 和 null
- \_数字分隔符分隔符不会改变数字字面量的值，是人更容易读懂数字。例如 1_101_123。
  \*\*求幂

15. any、never、unknown、null、undefined、void 有什么区别

- any:
  动态的变量类型（丢失了类型检查的作用）
- never:
  永不存在的值的类型，例如：never 类型是那些总是抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型。
- unknown：
  任何类型的值都可以赋值给 unknown 类型，但是 unknown 只能赋值给 unknown 本身和 any 类型。若需要复制给其他类型前，必须被断言。
- null 和 undefined：
  默认情况是所有类型的子类型。可以把这两个类型赋值给 number 类型。但是当我们指定 strictNullChecks: true 标记是，他们只能赋值给自身类型。
- void：
  没有任何类型。例如：一个函数没有返回值，那么返回值可以定义为 void。

- unknown 和 any
  unknown 和 any 的主要区别是 unknown 类型会更加严格 在对 unknown 类型的值执行大多数操作之前 我们必须进行某种形式的检查 而在对 any 类型的值执行操作之前 我们不必进行任何检查
  所有类型都可以被归为 unknown 但 unknown 类型只能被赋值给 any 类型和 unknown 类型本身 而 any 啥都能分配和被分配

- never 和 void
  void 可以被赋值为 null 和 undefined 的类型。 never 则是一个不包含值的类型。
  拥有 void 返回值类型的函数能正常运行。拥有 never 返回值类型的函数无法正常返回，无法终止，或会抛出异常。

17. infer 关键字
    infer 表示在 extends 条件语句中待推断的类型变量。

```ts
// 用于提取函数类型的返回值类型,内置类型
type ReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : any;

type Func = () => string;
type Test = ReturnType<Func>;
// Test = string

// 获取参数类型
type ConstructorParameters<T extends new (...args: any[]) => any> =
  T extends new (...args: infer P) => any ? P : never;

// 获取实例类型
type InstanceType<T extends new (...args: any[]) => any> = T extends new (
  ...args: any[]
) => infer R
  ? R
  : any;

class TestClass {
  constructor(public name: string, public string: number) {}
}

type Params = ConstructorParameters<typeof TestClass>; // [string, numbder]

type Instance = InstanceType<typeof TestClass>; // TestClass
```

18. infer 应用
    question:

```ts
interface Action<T> {
  payload?: T;
  type: string;
}
// 假设有Modle这样一个interface
interface Module {
  count: number;
  message: string;
  asyncMethod<T, U>(action: Promise<T>): Promise<Action<U>>;
  syncMethod<T, U>(action: Action<T>): Action<U>;
}
// 实现type Connect
// 保留属性为函数类型，其余的摒弃掉
// 把函数类型转化为<T, U>(args: T) => Action<U>
type Connect<T> = {}; /** 你需要实现的逻辑 */
type Result = Connect<Module>;
// Result = {
//   asyncMethod<T, U>(input: T): Action<U>;
//   syncMethod<T, U>(action: T): Action<U>;
// }
```

answer:

```ts
type PickFuncProp<T> = {
  [P in keyof T]: T[P] extends Function ? P : never;
}[keyof T]; // PickFuncProp<Module> <=> 'asyncMethod'|'syncMethod'

type TransitionFunc<F> = F extends (
  action: Promise<infer T>
) => Promise<Action<infer U>>
  ? <T, U>(action: T) => Action<U>
  : F extends (action: Action<infer T>) => Action<infer U>
  ? <T, U>(action: T) => Action<U>
  : F;
type syncMethod<T, U> = (action: Action<T>) => Action<U>;
type asyncMethod<T, U> = (input: Promise<T>) => Promise<Action<U>>;
// TransitionFunc<syncMethod> <=> <T, U>(action: T) => Action<U>;
// TransitionFunc<asyncMethod> <=> <T, U>(action: T) => Action<U>;

type Connect<T> = {[P in PickFuncProp<T>]: TransitionFunc<T[P]>;};
type Result = Connect(Module);
// Result = {
//   asyncMethod: <T, U>(action: T) => Action<U>;
//   syncMethod: <T, U>(action: T) => Action<U>;
// }
```

19. 如果你有一个含有很多参数或者相同类型参数的函数，那么你可能需要考虑将函数改为接收对象的形式,
    可能会记错位置顺序

```ts
function foo(flagA: boolean, flagB: boolean) {
  // 函数主体
}
// 改造成接受对象
function foo(config: { flagA: boolean; flagB: boolean }) {
  const { flagA, flagB } = config;
  // 函数主体
}
```

20. lib.d.ts
    当你安装 TypeScript 时，会顺带安装一个 lib.d.ts 声明文件。这个文件包含 JavaScript 运行时以及 DOM 中存在各种常见的环境声明。

- 它自动包含在 TypeScript 项目的编译上下文中；
- 它能让你快速开始书写经过类型检查的 JavaScript 代码。
- 可以通过指定 --noLib 的编译器命令行标志（或者在 tsconfig.json 中指定选项 noLib: true）从上下文中排除此文件。

21. 修改原始类型

- 在 TypeScript 中，接口是开放式的，这意味着当你想使用不存在的成员时，只需要将它们添加至 lib.d.ts 中的接口声明中即可，TypeScript 将会自动接收它。注意，你需要在全局模块中做这些修改，以使这些接口与 lib.d.ts 相关联。我们推荐你创建一个称为 global.d.ts 的特殊文件。

- 也可以通过使用 declare global { /_ global namespace _/ }，从文件模块中进入全局命名空间

22. 编译目标对 lib.d.ts 的影响

```json
// tsconfig.json
"compilerOptions": {
    "lib": ["dom", "es6"]
}
```

--lib 选项提供非常精细的控制，因此你最有可能从运行环境与 JavaScript 功能类别中分别选择一项，如果你没有指定 --lib，则会导入默认库：
--target 选项为 es5 时，会导入 es5, dom, scripthost。
--target 选项为 es6 时，会导入 es6, dom, dom.iterable, scripthost。

23. type-->any[]-->number
    [number] 用来代指所有数组下标，不管下标 0 还是下标 1，它们都是 number 型。
    ['a','b'][number] === ['a','b'][0|1] === 'a'|'b'

24. import type 和 import 的区别
    枚举 enum 即是值空间也是类型空间
    import type 是用来协助进行类型检查和声明的，在运行时是完全不存在的。

    ```ts
    import type { SomeThing } from "./some-module.js";
    export type { SomeThing };
    ```

25. 不要在 interface 中使用函数重载
    正确的做法是在 class 中声明重载，在 class 中实现

26. 在接口中有两种方式可以定义一个函数，一个被定义在实例上，一个被定义在原型链上

```ts
interface PersonalIntl {
  func1(): any; // 原型链方法
  func2: () => any; // 实例属性
}
class Personal implements PersonalIntl {
  func1() {
    console.log(this);
  }
  func2 = () => {
    console.log(this);
  };
}
var Personal = /** @class */ (function () {
  function Personal() {
    var _this = this;
    this.func2 = function () {
      console.log(_this);
    };
  }
  Personal.prototype.func1 = function () {
    console.log(this);
  };
  return Personal;
})();
```

27. 或联合(佛怒火莲),与交叉(渔叉)，谐音记忆

```ts
interface Dictionary<T> {
  [index: string]: T;
};
//key为string , value为number
var map: { [key: string]: number; } = {
    "t" : 3,
    "o" : 5,
    "g" :10
};
for(let k in map){
    egret.log(map[k]);
```

28. 在TS1.5的版本后，推荐全面使用namespace来代替module
 “内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”
任何使用 module关键字来声明一个内部模块的地方都应该使用namespace关键字来替换


29. Typescript 4.9 引入了一个新的satisfies关键字，允许你在不改变推断类型的情况下检查类型。

```ts
type NamedCircle = {
    radius: number;
    name?: string;
};

// error because radius violates NamedCircle
const wrongCircle = { radius: '1.0', name: 'ha' }
    satisfies NamedCircle;

const circle = { radius: 1.0, name: 'yeah' }
    satisfies NamedCircle;

// circle.name can't be undefined now
console.log(circle.name.length);
```
### 基本类型

#### 元组类型

在 TypeScript 的基础类型中，元组（ Tuple ）表示一个**已知数量和类型的数组** 其实可以理解为他是一种特殊的数组

数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。

当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：

```ts
const flag: [string, number] = ["hello", 1];
```

#### 枚举类型

```ts
// 普通枚举 初始值默认为 0 其余的成员会会按顺序自动增长 可以理解为数组下标
enum Color {
  RED,
  PINK,
  BLUE,
}

const pink: Color = Color.PINK;
console.log(pink); // 1

// 设置初始值
enum Color {
  RED = 10,
  PINK,
  BLUE,
}
const pink: Color = Color.PINK;
console.log(pink); // 11

// 字符串枚举 每个都需要声明
enum Color {
  RED = "红色",
  PINK = "粉色",
  BLUE = "蓝色",
}

const pink: Color = Color.PINK;
console.log(pink); // 粉色

// 常量枚举 它是使用 const 关键字修饰的枚举，常量枚举与普通枚举的区别是，整个枚举会在编译阶段被删除 我们可以看下编译之后的效果

const enum Color {
  RED,
  PINK,
  BLUE,
}

const color: Color[] = [Color.RED, Color.PINK, Color.BLUE];

//编译之后的js如下：
var color = [0 /* RED */, 1 /* PINK */, 2 /* BLUE */];
// 可以看到我们的枚举并没有被编译成js代码 只是把color这个数组变量编译出来了

// 获取枚举的key
enum Str {
  A,
  B,
  C,
}
type strUnion = keyof typeof str; // 'A' | 'B' | 'C'
```

#### 字面量类型

字符串字面量类型,数字字面量类型,布尔字面量类型
目前，TypeScript 支持 3 种字面量类型：字符串字面量类型、数字字面量类型、布尔字面量类型，对应的字符串字面量、数字字面量、布尔字面量分别拥有与其值一样的字面量类型

```ts
let flag1: "hello" = "hello";
let flag2: 1 = 1;
let flag3: true = true;
```

#### 预设类型,内置工具类型

TypeScript 2.8 在 lib.d.ts 里增加了一些预定义的有条件类型：

- Exclude<T, U> -- 从 T 中剔除可以赋值给 U 的类型。

```ts
type Foo = 'a' | 'b' | 'c';
type A = Exclude<Foo, 'a'>; // 'b' | 'c'

type Exclude<T, K> = T extens K ? never : T;
```

- Extract<T, U> -- 提取 T 中可以赋值给 U 的类型。

```ts
type Key = "a" | "b" | "c";
type A = Extract<Key, "a">; // 'a'

type Extract<T, U> = T extends U ? T : never;
```

- NonNullable<T> -- 从 T 中剔除 null 和 undefined。

```ts
type Foo = "a" | "b" | null | undefined;
type A = NonNullable<Foo>;
"a" | "b";

type NonNullable<T> = T extends null | undefined ? never : T;
```

- ReturnType<T> -- 获取函数返回值类型。

```ts
function getUser() {
  return { name: "XXX", age: 10 };
}

type getUserType = typeof getUser;
type ReturnUser = ReturnType<getUserType>;
```

- InstanceType<T> -- 获取构造函数类型的实例类型。

```ts
class C {
  x = 0;
  y = 0;
}
type T0 = InstanceType<typeof C>;
type T0 = C;
```

- Parameters<T> --获取函数类型的参数类型

```ts
type Parameters<T> = T extends (...args: infer R) => any ? R : any;

type T0 = Parameters<() => string>; // []
type T1 = Parameters<(s: string) => void>; // [string]
type T2 = Parameters<<T>(arg: T) => T>; // [unknown]
```

- ConstructorParameters --获取构造函数的参数类型

```ts
class Person {
  name: string,
  constructor(name: string) {
    this.name = name
  }
  getName() {
    console.log(this.name)
  }
  // 获取函数类型的参数类型
  type Params = ConstructorParameters<typeof Person>
}
```

- Partial<T> --将传入的属性由非可选变为可选

```ts
type Partial<T> = { [P in keyof T]?: T[P] };
interface A {
  a1: string;
  a2: number;
  a3: boolean;
}
type aPartial = Partial<A>;
const a: aPartial = {}; // 不会报错
```

- Required --可以将传入的属性中的可选项变为必选项，这里用了 -? 修饰符来实现

```ts
interface Person {
  name: string;
  age: number;
  gender?: "male" | "female";
}
/**
 * type Required<T> = { [P in keyof T]-?: T[P] };
 */
let p: Required<Person> = {
  name: "hello",
  age: 10,
  gender: "male",
};
```

- Readonly<T> --为传入的属性每一项都加上 readonly 修饰符来实现

```ts
interface Person {
  name: string;
  age: number;
  gender?: "male" | "female";
}
//type Readonly<T> = { readonly [P in keyof T]: T[P] };
let p: Readonly<Person> = {
  name: "hello",
  age: 10,
  gender: "male",
};
p.age = 11; //error
```

- Pick<T,K extends keyof T> -- 从传入的属性中摘取某些返回

```ts
interface Todo {
  title: string;
  description: string;
  done: boolean;
}
/**
 * From T pick a set of properties K
 * type Pick<T, K extends keyof T> = { [P in K]: T[P] };
 */
type TodoBase = Pick<Todo, "title" | "done">;

// 等于
type TodoBase = {
  title: string;
  done: boolean;
};
```

- Record<K,T> -- 具有一组属性 K，每个属性的类型为 T。可用于将一个类型的属性映射为另一个类型。Record 后面的泛型就是对象键和值的类型。

```ts
// type Record<K extends keyof any, T> = {
// [P in K]: T;
// };
// 大家不会用 object 来约束，而是用 Record<string, any> 来约束索引类型，这俩其实是一样的，但是 Record<string, any> 更语义化一些
type Point = "x" | "y";
type PointList = Record<Point, { value: number }>;
const cars: PointList = {
  x: { value: 10 },
  y: { value: 20 },
};
```

- Omit<K,T> -- 基于已经声明的类型进行属性剔除获得新类型

```ts
// type Omit=Pick<T,Exclude<keyof T,K>>
type User = {
id: string;
name: string;
email: string;
};
type UserWithoutEmail = Omit<User, "email">;// UserWithoutEmail ={id: string;name: string;}
};
```

#### 交叉类型(intersection type) T&U

交叉类型是将多个类型合并为一个类型。通过 & 运算符可以将现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性

```ts
type Flag1 = { x: number };
type Flag2 = Flag1 & { y: string };

let flag3: Flag2 = {
  x: 1,
  y: "hello",
  henb,
};
```

#### 联合类型(union type) T|U

联合类型（Union Types）表示取值可以为多种类型中的一种
未赋值时联合类型上只能访问两个类型共有的属性和方法

```ts
let name: string | number;
console.log(name.toString());
name = 1;
console.log(name.toFixed(2));
name = "hello";
console.log(name.length);
```

#### 辨证联合类型

#### 类型保护

在编译的时候就能通过类型信息确保某个作用域内变量的类型 其主要思想是尝试检测属性、方法或原型，以确定如何处理值

1. typeof 类型保护

```ts
// 很常见
function double(input: string | number | boolean) {
  if (typeof input === "string") {
    return input + input;
  } else {
    if (typeof input === "number") {
      return input * 2;
    } else {
      return !input;
    }
  }
}
```

2. in 关键字

```ts
interface Bird {
  fly: number;
}

interface Dog {
  leg: number;
}

function getNumber(value: Bird | Dog) {
  if ("fly" in value) {
    return value.fly;
  }
  return value.leg;
}
```

3. instanceof 类型保护

```ts
class Animal {
  name!: string;
}
class Bird extends Animal {
  fly!: number;
}
function getName(animal: Animal) {
  if (animal instanceof Bird) {
    console.log(animal.fly);
  } else {
    console.log(animal.name);
  }
}
```

4. 自定义类型保护
   通过 type is xxx 这样的类型谓词来进行类型保护

```ts
function isObject(value: unknown): value is object {
  return typeof value === "object" && value !== null;
}

function fn(x: string | object) {
  if (isObject(x)) {
    // ....
  } else {
    // .....
  }
}
```

#### ThisType

通过 ThisType 我们可以在对象字面量中键入 this，并提供通过上下文类型控制 this 类型的便捷方式。它只有在 **--noImplicitThis** 的选项下才有效。

1. 方法显式指定了 this 参数，那么 this 具有该参数的类型

```ts
let bar = {
  x: "hello",
  f(this: { message: string }) {
    this; // { message: string }
  },
};
```

2. 方法由带 this 参数的签名进行上下文键入，那么 this 具有该参数的类型

```ts
let foo = {
  x: "hello",
  f(n: number) {
    this; // { x: string, f(n: number): void }
  },
};
```

3. 对象字面量中包含由 ThisType<T> 键入的上下文类型，那么 this 的类型为 T
   对象字面量中不包含由 ThisType<T> 键入的上下文类型，那么 this 的类型为该上下文类型

```ts
// Compile with --noImplicitThis

type ObjectDescriptor<D, M> = {
  data?: D;
  methods?: M & ThisType<D & M>; // Type of 'this' in methods is D & M
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
      this.x += dx; // Strongly typed this
      this.y += dy; // Strongly typed this
    },
  },
});

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

5. this 具有该对象字面量的类型

### 函数

- 剩余函数

```ts
function sum(...numbers: number[]) {
  return numbers.reduce((val, item) => (val += item), 0);
}
console.log(sum(1, 2, 3));
```

### class 类

- 采用 implements interface 来约束 class 的实现

- readonly
  readonly 修饰的变量只能在构造函数中初始化 TypeScript 的类型系统同样也允许将 interface、type、 class 上的属性标识为 readonly readonly 实际上只是在编译阶段进行代码检查

```ts
class Animal {
  public readonly name: string;
  constructor(name: string) {
    this.name = name;
  }
  changeName(name: string) {
    this.name = name; //这个ts是报错的
  }
}

let a = new Animal("hello");
```

- 类里面的修饰符

public 类里面 子类 其它任何地方外边都可以访问 protected 类里面 子类 都可以访问,其它任何地方不能访问 private 类里面可以访问，子类和其它任何地方都不可以访问

- 抽象类和抽象方法
  抽象类，无法被实例化，只能被继承并且无法创建抽象类的实例
  子类可以对抽象类进行不同的实现
  抽象方法只能出现在抽象类中并且抽象方法不能在抽象类中被具体实现，只能在抽象类的子类中实现（必须要实现）
  使用场景：
  我们一般用抽象类和抽象方法抽离出事物的共性 以后所有继承的子类必须按照规范去实现自己的具体逻辑 这样可以增加代码的可维护性和复用性
  使用 abstract 关键字来定义抽象类和抽象方法

```ts
abstract class Animal {
  name!: string;
  abstract speak(): void;
}
class Cat extends Animal {
  speak() {
    console.log("喵喵喵");
  }
}
let animal = new Animal(); //直接报错 无法创建抽象类的实例
let cat = new Cat();
cat.speak();
```

#### 混合

(混合)是一个函数：
传入一个构造函数；
创建一个带有新功能，并且扩展构造函数的新类；
返回这个新类。
TypeScript (和 JavaScript) 类只能严格的单继承
混合实现组合类

```ts
// 所有 mixins 都需要
type Constructor<T = {}> = new (...args: any[]) => T;

/////////////
// mixins 例子
////////////

// 添加属性的混合例子
function Timestamped<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    timestamp = Date.now();
  };
}

// 添加属性和方法的混合例子
function Activatable<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    isActivated = false;

    activate() {
      this.isActivated = true;
    }

    deactivate() {
      this.isActivated = false;
    }
  };
}

///////////
// 组合类
///////////

// 简单的类
class User {
  name = "";
}

// 添加 Timestamped 的 User
const TimestampedUser = Timestamped(User);

// Tina Timestamped 和 Activatable 的类
const TimestampedActivatableUser = Timestamped(Activatable(User));

//////////
// 使用组合类
//////////

const timestampedUserExample = new TimestampedUser();
console.log(timestampedUserExample.timestamp);

const timestampedActivatableUserExample = new TimestampedActivatableUser();
console.log(timestampedActivatableUserExample.timestamp);
console.log(timestampedActivatableUserExample.isActivated);
```

### 高级类型

#### 泛型

1. 泛型变量 T

```ts
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length); // Error: T doesn't have .length
  return arg;
}
// 多个泛型变量
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]];
}

swap([7, "seven"]); // ['seven', 7]
```

2. 泛型函数

```ts
let output = identity("myString");
let output = identity<string>("myString");
```

3. 泛型接口
   定义接口的时候也可以指定泛型

```ts
interface Cart<T> {
  list: T[];
}
let cart: Cart<{ name: string; price: number }> = {
  list: [{ name: "hello", price: 10 }],
};
console.log(cart.list[0].name, cart.list[0].price);
```

我们定义了接口传入的类型 T 之后返回的对象数组里面 T 就是当时传入的参数类型

4. 泛型类
   除了泛型接口，我们还可以创建泛型类。 注意，无法创建泛型枚举和泛型命名空间

```ts
// 类名后的尖括号
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};
```

5. 泛型约束

```ts
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // Now we know it has a .length property, so no more error
  return arg;
}
```

6. 泛型类型别名

```ts
type Cart<T> = { list: T[] } | T[];
let c1: Cart<string> = { list: ["1"] };
let c2: Cart<number> = [1];
```

### 内部模块和命名空间

“内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”，这是为了与 ECMAScript 2015 里的术语保持一致，(也就是说 module X { 相当于现在推荐的写法 namespace X {)。

- import

- export

```ts
/// <reference path="Validation.ts" />
namespace Validation {
  const lettersRegexp = /^[A-Za-z]+$/;
  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
      return lettersRegexp.test(s);
    }
  }
}
```

#### 全局类型声明

dts 的类型声明默认是全局的，除非有 es module 的 import、export 的声明，这时候就要手动 declare global 了。为了避免这种情况，可以用 reference 的编译器指令。

#### 短路运算符 return sn || "default";

```ts
interface Map<T> {
  [key: string]: T;
}
let keys: keyof Map<number>; // string
let value: Map<number>["foo"]; // number
```

新增 d.ts 文件后重新打开一下文件夹，不然不生效

//精华
!是和?相对的，是 typescript 的语法，表示强制解析（也就是告诉 typescript 编译器，我这里一定有值）。
你写?的时候再调用，typescript 会提示可能为 undefined
// anyscript
搞不定的时候记得用 (interface as any).attribute
//命名空间

```ts
namespace Shuaige {
  export class DeHua {
    public name: string = "刘德华";
    say() {
      console.log(`我是${this.name}`);
    }
  }
}
const de: Shuaige.DeHua = new Shuaige.DeHua();
de.say();
```

#### 逆变、协变、双向协变、不变

ts 通过给 js 添加了静态类型系统来保证了类型安全，大多数情况下不同类型之间是不能赋值的，但是为了增加类型系统灵活性，设计了父子类型的概念。父子类型之间自然应该能赋值，也就是会发生型变。

型变分为逆变和协变。协变很容易理解，就是子类型赋值给父类型。逆变主要是函数赋值的时候函数参数的性质，参数的父类型可以赋值给子类型，这是因为按照子类型来声明的参数，访问父类型的属性和方法自然没问题，依然是类型安全的。但反过来就不一定了。

不过 ts 2.x 之前反过来依然是可以赋值的，也就是既逆变又协变，叫做双向协变。

为了更严格的保证类型安全，ts 添加了 strictFunctionTypes 的编译选项，开启以后函数参数就只支持逆变，否则支持双向协变。

型变都是针对父子类型来说的，非父子类型自然就不会型变也就是不变。

像 java 里面的类型都是通过 extends 继承的，如果 A extends B，那 A 就是 B 的子类型。这种叫做名义类型系统（nominal type）

而 ts 里不看这个，只要结构上是一致的，那么就可以确定父子关系，这种叫做结构类型系统（structual type）

ts 中父子类型的判定是按照结构来看的，更具体的那个是子类型。

```ts
interface Person {
  name: string;
  age: number;
}
interface Guang {
  name: string;
  age: numbder;
  hobbies: string[];
}
```

Guang 和 Person 没有 extends 关系,但结构一致，，**结构更具体的是子类型** ,所以 Guang 是 Person 的子类型
'a' | 'b' 是 'a' | 'b' | 'c' 的子类型

```ts
// 联合类型父子关系的时候， 'a' | 'b' 和 'a' | 'b' | 'c' 哪个更具体？
type res = "a" | "b" extends "a" | "b" | "c" ? true : false;
// 结果 true
```

#### Reflect metaData

### 装饰器

装饰器是一种特殊类型的声明，它能够被附加到类声明、方法、属性或参数上，可以修改类的行为
装饰器的写法分为普通装饰器和装饰器工厂
使用@装饰器的写法需要把 tsconfig.json 的 experimentalDecorators 字段设置为 true

#### 类装饰器

#### 属性装饰器

#### 方法装饰器

#### 参数装饰器

#### 装饰器执行顺序

有多个参数装饰器时：从最后一个参数依次向前执行
方法和方法参数装饰器中参数装饰器先执行。
方法和属性装饰器，谁在前面谁先执行。因为参数属于方法一部分，所以参数会一直紧紧挨着方法执行

类装饰器总是最后执行

### 异常处理

#### 错误

```ts
try {
  throw new Error("Something bad happened");
} catch (e) {
  console.log(e);
}

function validate(value: number): {
  error?: string;
} {
  if (value < 0 || value > 100) {
    return { error: "Invalid value" };
  }
}
```

#### 内置错误子类型

1. RangeError
2. ReferenceError
3. SyntaxError
4. TypeError
5. URIError

### vue 文件使用 ts

由于 TypeScript 默认并不支持 \*.vue 后缀的文件，所以在 vue 项目中引入的时候需要创建一个 vue-shim.d.ts 文件，放在根目录下

```ts
declare module "*.vue" {
  import Vue from "vue";
  export default Vue;
}
```

### 基于导出配置自动生成一个 TS 声明文件
```json
{
  scripts: {
  "build:dts": "vue-tsc src/components/table/src/Table.vue  --declaration --emitDeclarationOnly"
  }  
}
// ts.config.json
{
  "compilerOptions": {
    "declaration": true //Generates corresponding d.ts files.
  }
}

```


### 编译工具

ts-node == tsc + node
sucrase-node (sucrase 主要特性是编译 js 运行时永远不会支持的语言拓展(jsx,typescript,flow),是 babel 的子集，比他快，但不生成 es5 的代码,有些新的 es6 语法不支持,,兼容性变差)

### 参考链接

[深入理解 TypeScript](https://jkchao.github.io/typescript-book-chinese/)

[TypeScript 入门教程](https://ts.xcatliu.com/advanced/)

[TypeScript 中提升幸福感的 10 个高级技巧](https://juejin.cn/post/6919478002925453320#heading-6)

[最全的 TypeScript 学习指南](https://juejin.cn/post/7031787942691471396#heading-65)

[三方库类型声明查找](http://definitelytyped.org/)

[类型拓展库](https://github.com/millsp/ts-toolbelt)

[工具类型库](https://github.com/piotrwitek/utility-types)
