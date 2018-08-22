---
layout:     post
title:      Typescript 基础类型 / 接口 
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

// Array
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];

// Tuple 元组 值与类型一一对应，当有越界元素出现，使用联合类型代替
let x: [string, number];   // Declare a tuple type
x = ["hello", 10]; // OK  // Initialize it
x = [10, "hello"]; // Error  // Initialize it incorrectly
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
x[3] = "world";  // OK, 'string' can be assigned to 'string | number'
console.log(x[5].toString());  // OK, 'string' and 'number' both have 'toString'
x[6] = true;  // Error, 'boolean' isn't 'string | number'

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

// Any  为不确定类型的变量指定类型
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
function warnUser(): void {   // do not return a value
    console.log("This is my warning message");
}
let unusable: void = undefined; // variables of type void can only assign undefined or null 

// Null and Undefined 
let u: undefined = undefined;
let n: null = null;
// 当指定--strictNullChecks标记，null和undefined只能赋值给void和它们各自。
// 当要传入一个 string或null或undefined，可使用联合类型string | null | undefined。

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

## | 接口 Interfaces 

```ts
interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = { firstName: "Jane", lastName: "User" };

document.body.innerHTML = greeter(user);
```

接口一般首字母大写。    
赋值时，变量的形状必须和接口的形状保持一致。

```ts
interface Person {
    name: string;
    age: number;
}
let tom: Person = {
    name: 'Tom',
    age: 25
};
```

上面例子中，定义了一个接口 Person，接着定义了一个变量 tom，它的类型是 Person。这样就约束了 tom 的形状必须和接口 Person 一致。

定义的变量比接口少/多了一些属性是不允许的：

```ts
interface Person {
    name: string;
    age: number;
}
let tom: Person = {
    name: 'Tom'
};
// index.ts(6,5): error TS2322: Type '{ name: string; }' is not assignable to type 'Person'.
//   Property 'age' is missing in type '{ name: string; }'.
```

```ts
interface Person {
    name: string;
    age: number;
}
let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
// index.ts(9,5): error TS2322: Type '{ name: string; age: number; gender: string; }' 
// is not assignable to type 'Person'.
// Object literal may only specify known properties, 
// and 'gender' does not exist in type 'Person'.
```

#### 可选属性 

有时我们希望不要完全匹配一个形状，那么可以用可选属性。带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个 ? 符号。  

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。  

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
};
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

可选属性的含义是该属性可以不存在。 这时仍然不允许添加未定义的属性：

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// examples/playground/index.ts(9,5): 
// error TS2322: Type '{ name: string; age: number; gender: string; }' 
// is not assignable to type 'Person'.
// Object literal may only specify known properties, and 'gender' does not exist in type 'Person
```

#### 任意属性 

有时候我们希望一个接口允许有任意的属性，可以使用如下方式：

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}
let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

使用 [propName: string] 定义了任意属性取 string 类型的值。

需要注意的是，一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性：

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
// index.ts(3,5): error TS2411: 
// Property 'age' of type 'number' is not assignable to string index type 'string'. 
// index.ts(7,5): error TS2322: 
// Type '{ [x: string]: string | number; name: string; age: number; gender: string; }'  
// is not assignable to type 'Person'.
//  Index signatures are incompatible. 
// Type 'string | number' is not assignable to type 'string'. 
// Type 'number' is not assignable to type 'string'. 
```

上例中，任意属性的值允许是 string，但是可选属性 age 的值却是 number，number 不是 string 的子属性，所以报错了。

另外，在报错信息中可以看出，此时 { name: 'Tom', age: 25, gender: 'male' } 的类型被推断成了 { [x: string]: string \| number; name: string; age: number; gender: string; }，这是联合类型和接口的结合。

#### 只读属性 

属性名前用 readonly来指定只读属性

```ts
// 只读属性
interface Point {
    readonly x: number;
    readonly y: number;
}
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```

注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候： 

```ts
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};

tom.id = 89757;
// index.ts(8,5): error TS2322: 
// Type '{ name: string; gender: string; }' is not assignable to type 'Person'.
// Property 'id' is missing in type '{ name: string; gender: string; }'.
// index.ts(13,5): error TS2540: 
// Cannot assign to 'id' because it is a constant or a read-only property.
```

例中，报错信息有两处，第一处是在对 tom 进行赋值的时候，没有给 id 赋值。 第二处是在给 tom.id 赋值的时候，由于它是只读属性，所以报错了。

TypeScript 具有 ReadonlyArray<T> 类型，它与 Array<T> 相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改：

```ts
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!
```

上面代码的最后一行，可以看到就算把整个 ReadonlyArray 赋值到一个普通数组也是不可以的。 但是你可以用类型断言重写：

`a = ro as number[];`

> readonly vs const：    

`最简单判断该用 readonly 还是 const 的方法是看要把它做为变量使用还是做为一个属性。`  
`做为变量使用的话用 const，若做为属性则使用 readonly。`  

#### 可索引的类型 

```ts
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

上面例子里，我们定义了 StringArray 接口，它具有索引签名。这个索引签名表示了当用 number 去索引 StringArray 时会得到 string 类型的返回值。

## 参考链接 

[typescript官方文档](https://www.typescriptlang.org/) | [typescript中文网](https://www.tslang.cn/) | [TypeScript 资源集](https://segmentfault.com/a/1190000010130073)
[TypeScript Handbook（中文版）](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Type%20Inference.html) | [TypeScript 入门教程](https://ts.xcatliu.com/basics/type-inference.html)


