---
layout:     post
title:      Typescript 基础类型 | 类型断言
summary:
categories: Ts
technique: true
---

TypeScript 由微软开发的自由和开源的编程语言，它是JavaScript的一个超集，扩展了JavaScript的语法。

编译 ts， 命令行执行: `tsc greeter.ts`

## | 基础类型 Basic Types

```typescript
// boolean
let isDone: boolean = false;

// number
let decimal: number = 6;
let hex: number = 0xf00d;  // 十六进制
let binary: number = 0b1010;
let octal: number = 0o744;  // 八进制

// string
let color: string = "blue";
color = 'red';


// Any 任意类型- 为不确定类型的变量指定类型
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

let notSure: any = 4; // any 类型允许在编译时可选择地包含或移除类型检查。可赋任意值，可调用任意方法
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4; // Object 类型只允许赋任意值，但不能调用任意的方法，即便真有这些方法
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.

let list: any[] = [1, true, "free"]; // an array has a mix of different types
list[1] = 100;

// Void 没有任何类型
function warnUser(): void {   // do not return a value （函数没有返回值时）
    console.log("This is my warning message");
}
let unusable: void = undefined; // variables of type void can only assign undefined or null
// void 类型的变量只能赋值为 undefined 和 null，其他类型不能赋值给 void 类型的变量。

// Never  represents the type of values that never occur.
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
function infiniteLoop(): never {
    while (true) {
    }
}
// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// unknown 类型的值不是可以随便操作的

// Object
declare function create(o: object | null): void;
create({ prop: 0 }); // OK
create(null); // OK
create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error

// Type assertions 类型断言
let someValue: any = "this is a string";  // 'angle-bracket' syntax
let strLength: number = (<string>someValue).length;

let someValue: any = "this is a string";  // as-syntax
let strLength: number = (someValue as string).length;
```

**Type annotations**

```ts
function greeter(person: string) {
    return "Hello, " + person;
}

let user = [0, 1, 2];

document.body.innerHTML = greeter(user);
```

greeter 需要一个字符串类型参数，当传了数组，报错如下:

```
error TS2345: Argument of type 'number[]' is not assignable to parameter of type 'string'.
```
### 数组

```javascript
// 两种定义数组的方式：
let list1: number[] = [1, 2, 3];
let list2: Array<number> = [1, 2, 3];
// 当你使用第二种形式定义时，tslint 可能会警告让你使用第一种形式定义，如果你就是想用第二种形式，可以通过在 tslint.json 的 rules 中加入"array-type": [false]关闭 tslint 对这条的检测。
```

### 元组


```javascript

// Tuple 元组 值与类型一一对应，在 2.6 及之前版本中当有越界元素出现，使用联合类型代替;2.6 之后的版本要求元组赋值必须类型和个数都对应。
let x: [string, number];   // Declare a tuple type
x = ["hello", 10]; // OK  // Initialize it
x = [10, "hello"]; // Error  // Initialize it incorrectly
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
x[3] = "world";  // OK, 'string' can be assigned to 'string | number'
console.log(x[5].toString());  // OK, 'string' and 'number' both have 'toString'
x[6] = true;  // Error, 'boolean' isn't 'string | number'

// 数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。
// let tuple: [string, number]元组类型的声明效果上可以看做等同于下面的声明：
interface Tuple extends Array<number | string> {
  0: string;
  1: number;
  length: 2;
}
```

### 枚举

#### 数字枚举

```javascript

// Enum 枚举 便利：可以由枚举的值得到名字
enum Color {Red, Green, Blue}
let c: Color = Color[1];
console.log(c);  // Green  index 默认从 0 开始 0 1 2

enum Color {Red=1, Green, Blue}
let c: Color = Color[1];
console.log(c);  // Red  index 指定从 1 开始 1 2 3

enum Color {Red=1, Green=3, Blue=5}
let c: Color = Color[1];
console.log(c);  // Red  指定 index  Color[3] = Green; Color[5] = Blue; Color[2] = undefined

const superAdmin = Color.Green;
console.log(superAdmin); // 3

// 指定部分字段，其他使用默认递增索引
enum Status {
  Ok = 200,
  Created,
  Accepted,
  BadRequest = 400,
  Unauthorized
}
console.log(Status.Created, Status.Accepted, Status.Unauthorized); // 201 202 401

// 数字枚举在定义值的时候，可以使用计算值和常量。但是要注意，如果某个字段使用了计算值或常量，那么该字段后面紧接着的字段必须设置初始值，这里不能使用默认的递增值了

const getValue = () => {
  return 0;
};
enum ErrorIndex {
  a = getValue(),
  b, // error 枚举成员必须具有初始化的值
  c
}
enum RightIndex {
  a = getValue(),
  b = 1,
  c
}
const Start = 1;
enum Index {
  a = Start,
  b, // error 枚举成员必须具有初始化的值
  c

```

#### 反向映射

定义一个枚举值的时候，可以通过 Enum[‘key’]或者 Enum.key 的形式获取到对应的值 value。TypeScript 还支持反向映射，但是反向映射只支持数字枚举，字符串枚举是不支持的。

```javascript
enum Status {
  Success = 200,
  NotFound = 404,
  Error = 500
}
console.log(Status["Success"]); // 200
console.log(Status[200]); // 'Success'
console.log(Status[Status["Success"]]); // 'Success'
```

#### 字符串枚举

字符串枚举，字符串枚举值要求每个字段的值都必须是字符串字面量，或者是该枚举值中另一个字符串枚举成员

```javascript
enum Message {
  Error = "Sorry, error",
  Success = "Hoho, success"
}
console.log(Message.Error); // 'Sorry, error'

// 枚举值中其他枚举成员的例子
enum Message {
  Error = "error message",
  ServerError = Error,
  ClientError = Error
}
console.log(Message.Error); // 'error message'
console.log(Message.ServerError); // 'error message'

```

#### 异构枚举

异构枚举就是枚举值中成员值既有数字类型又有字符串类型。这种如果不是真的需要，不建议使用。因为往往我们将一类值整理为一个枚举值的时候，它们的特点是相似的。比如我们在做接口请求时的返回状态码，如果是状态码都是数值，如果是提示信息，都是字符串，所以在使用枚举的时候，往往是可以避免使用异构枚举的，重点是做好类型的整理。

```javascript
enum Result {
  Faild = 0,
  Success = "Success"
}

```


## null 和 undefined

let u: undefined = undefined; // 这里可能会报一个tslint的错误：Unnecessary initialization to 'undefined'，就是不能给一个值赋undefined，但我们知道这是可以的，所以如果你的代码规范想让这种代码合理化，可以配置tslint，将"no-unnecessary-initializer"设为 false 即可

> 默认情况下 undefined 和 null 可以赋值给任意类型的值，也就是说你可以把 undefined 赋值给 void 类型，也可以赋值给 number 类型。当你在 tsconfig.json 的"compilerOptions"里设置了"strictNullChecks": true时，那必须严格对待。undefined 和 null 将只能赋值给它们自身和 void 类型，

```javascript
// Null and Undefined
let u: undefined = undefined;
let n: null = null;
// 当指定--strictNullChecks标记，null和undefined只能赋值给void和它们各自。
// 当要传入一个 string或null或undefined，可使用联合类型string | null | undefined。
```


## symbol

Symbol 前面不能加new关键字，直接调用即可创建一个独一无二的 symbol 类型的值。

```javascript
const s = Symbol();
typeof s; // 'symbol'

const s1 = Symbol("lison");
const s2 = Symbol("lison");
console.log(s1 === s2); // false

// Symbol 不可以和其他类型的值进行运算，但是可以转为字符串和布尔类型值：
let s = Symbol("lison");
console.log(s.toString()); // 'Symbol(lison)'
console.log(Boolean(s)); // true
console.log(!s); // false
```

Symbol 可作为属性名，但这个属性不会被for…in遍历到，也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()获取到：

```javascript
const name = Symbol("name");
const obj = {
  [name]: "lison",
  age: 18
};
for (const key in obj) {
  console.log(key);
}
// => 'age'
console.log(Object.keys(obj));
// ['age']
console.log(Object.getOwnPropertyNames(obj));
// ['age']
console.log(JSON.stringify(obj));
// '{ "age": 18 }'
```
虽然这么多方法都无法遍历和访问到 Symbol 类型的属性名，但是 Symbol 类型的属性并不是私有属性。我们可以使用Object.getOwnPropertySymbols方法获取对象的所有symbol类型的属性名。 还可以使用ES6 新提供的 Reflect 对象的静态方法Reflect.ownKeys，它可以返回所有类型的属性名，所以 Symbol 类型的也会返回。

```javascript
// 1、getOwnPropertySymbols方法
const name = Symbol("name");
const obj = {
  [name]: "lison",
  age: 18
};
const SymbolPropNames = Object.getOwnPropertySymbols(obj);
console.log(SymbolPropNames);
// [ Symbol(name) ]
console.log(obj[SymbolPropNames[0]]);
// 'lison'
// 如果最后一行代码这里报错提示：元素隐式具有 "any" 类型，因为类型“{ [name]: string; age: number; }”没有索引签名。 那可能是在tsconfig.json里开启了noImplicitAny。因为这里我们还没有学习接口等高级类型，所以你可以先忽略这个错误，或者关闭noImplicitAny。

// 2、Reflect.ownKeys 方法
const name = Symbol("name");
const obj = {
  [name]: "lison",
  age: 18
};
console.log(Reflect.ownKeys(obj));
// [ 'age', Symbol(name) ]
```

### Symbol.for()和 Symbol.keyFor()

Symbol 包含两个静态方法，for 和 keyFor。

***Symbol.for()***

- 该方法传入字符串，会先检查有没有使用该字符串调用 Symbol.for 方法创建的 symbol 值，如果有，返回该值，如果没有，则使用该字符串新创建一个

- 使用该方法创建 symbol 值后会在全局范围进行注册，包括当前页面和页面中包含的 iframe，以及 service sorker


```javascript
const s1 = Symbol("lison");
const s2 = Symbol("lison");
const s3 = Symbol.for("lison");
const s4 = Symbol.for("lison");
s3 === s4; // true
s1 === s3; // false
```

***Symbol.keyFor()***

该方法传入一个 symbol 值，返回该值在全局注册的键名

```javascript
const sym = Symbol.for("lison");
console.log(Symbol.keyFor(sym)); // 'lison'
```

**类型推论**

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。以下代码虽没指定类型，但会在编译时报错：

```typescript
let a = 'hello';
a = 7;
// index.ts(2,1): error TS2322: Type '7' is not assignable to type 'string'.
```

事实上，它等价于：

```typescript
let a: string = 'hello';
a = 7;
// index.ts(2,1): error TS2322: Type '7' is not assignable to type 'string'.
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查：

```typescript
let a;
a = 'hello';
a = 7;
```




# 类型断言

虽然 TypeScript 很强大，但有时它还是不如我们了解一个值的类型，这时候我们更希望 TypeScript 不要帮我们进行类型检查，而是交给我们自己来，所以就用到了类型断言。类型断言有点像是一种类型转换，它把某个值强行指定为特定类型

类型断言的用法：
1. 在需要断言的变量前加上 <Type> 。
2. value as type 例如：target as string

类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的：

```ts
function toBoolean(something: string | number): boolean {
    return <boolean>something;
    // return something as tring;
}
// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.
```
