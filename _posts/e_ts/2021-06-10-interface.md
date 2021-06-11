---
layout:     post
title:      Typescript 接口
summary:
categories: Ts
technique: true
---


# | 接口 Interfaces

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
// 定义了 StringArray 接口，它具有索引签名。这个索引签名表示了当用 number 去索引 StringArray 时会得到 string 类型的返回值。
interface StringArray {
  [index: number]: string;
}
let myArray: StringArray;
myArray = ["Bob", "Fred"];
let myStr: string = myArray[0];


interface RoleDic {
  [id: number]: string;
}
const role1: RoleDic = {
  0: "superadmin",
  1: "admin"
};
const role2: RoleDic = {
  s: "superadmin",  // error 不能将类型"{ s: string; a: string; }"分配给类型"RoleDic"。
  a: "admin"
};
const role3: RoleDic = ["super_admin", "admin"];
```



#### 函数类型

```javascript
interface AddFunc {
  (num1: number, num2: number): number;
}
const add: AddFunc = (n1, n2) => n1 + n2;
```


#### 继承接口

 一个接口可以被多个接口继承，同样，一个接口也可以继承多个接口，多个接口用逗号隔开

```javascript
// 一个接口可以被多个接口继承
interface Vegetables {
  color: string;
}
interface Tomato extends Vegetables {
  radius: number;
}
interface Carrot extends Vegetables {
  length: number;
}
const tomato: Tomato = {
  radius: 1.2 // error  Property 'color' is missing in type '{ radius: number; }'
};
const carrot: Carrot = {
  color: "orange",
  length: 20
};
// 一个接口也可以继承多个接口
interface Vegetables {
  color: string;
}
interface Food {
  type: string;
}
interface Tomato extends Food, Vegetables {
  radius: number;
}
const tomato: Tomato = {
  type: "vegetables",
  color: "red",
  radius: 1.2
};  // 在定义tomato变量时将继承过来的color和type属性同时声明

```
#### 混合类型接口

```javascript
interface Counter {
  (): void; // 这里定义Counter这个结构必须包含一个函数，函数的要求是无参数，返回值为void，即无返回值
  count: number; // 而且这个结构还必须包含一个名为count、值的类型为number类型的属性
}
const getCounter = (): Counter => { // 这里定义一个函数用来返回这个计数器
  const c = () => { // 定义一个函数，逻辑和前面例子的一样
    c.count++;
  };
  c.count = 0; // 再给这个函数添加一个count属性初始值为0
  return c; // 最后返回这个函数对象
};
const counter: Counter = getCounter(); // 通过getCounter函数得到这个计数器
counter();
console.log(counter.count); // 1
counter();
console.log(counter.count); // 2

```
