---
title: TypeScript学习记录
tags: [ts]
sticky: 100
---

## 联合类型
```ts
let a: string | number
```

## 交叉类型
```ts
interface People {
  name:string,
  age: number
}

interface Man {
  sex: number
}

// 交叉类型
let fx = (man:People & Man):void => {
  console.log(man);
}

fx({name: 'fx', age: 22, sex: 1})
```

## 类型断言
```ts
// 类型断言,不建议频繁使用
let duanyan = function(num: number | string):void {
  //    console.log(num.length);
  //   报错信息： 类型“number”上不存在属性“length”
  console.log((num as string).length)  // 第一种写法
  console.log((<string> num).length);  // 第二种写法
}
```


## TS内置对象
### ECMAScript 的内置对象
- Boolean
- Number
- String
- RegExp
- Date
- Error

### BOM 和 DOM 的内置对象
- Document
- HTMLElement
- Event
- NodeList
- ......
```ts
let date:Date = new Date()

const list:NodeList = document.querySelectorAll('#list li')

const div:HTMLDivElement = document.querySelector('div')

document.body.addEventListener('click', (e: MouseEvent) => {})

function promise(): Promise<number> {
  return new Promise<number>((resolve,reject) => {
    resolve(1)
  })
}
```

## Class 类
### class 的定义
```ts
class Student {
  // ts 中需要在 constructor 上面先声明好变量
  name: string
  age: number

  constructor(name: string, age: number) {
    this.name = name
    this,age = age
  }
}
```

### class 的修饰符

```ts
class Student {
    // ts 中需要在 constructor 上面先声明好变量
    public name: string
    private age: number
    protected gender: string

    constructor(name: string, age: number, gender: string) {
        this.name = name
        this, age = age
        this.gender = gender
        //  非静态方法中，只能通过类名. 的方式获取
        console.log(Student.avatar);
    }
    //  静态方法中可以访问静态变量
    static run() {
        console.log(this.avatar);
    }
}

let stu1 = new Student('fx', 24)
console.log(stu1.name)
console.log(stu1.age) // ❌ 属性“age”为私有属性，只能在类“Student”中访问。
console.log(Student.avatar); // 静态类型的方法与变量，只能通过类名. 的方式访问
console.log(stu1.avatar); // ❌ 属性“avatar”在类型“Student”上不存在。你的意思是改为访问静态成员“Student.avatar”吗?

class mStudent extends Student {
    constructor() {
        super();
        //  protected 修饰的属性 在 子类中是可以被访问到的
        //  private 修饰的属性，在其他类中都无法访问
        console.log(this.gender)    
    }
}
```

### interface 约束 class
```ts
// 通过 interface 接口来约束类,即类需要实现接口
interface Animal {
  run(type: boolean): boolean
}

interface H {
  bark(): void
}

class Dog implements Animal,H{
  bark(): void {
    console.log(`类“Dog”错误实现接口“H”。类型 "Dog" 中缺少属性 "bark"，但类型 "H" 中需要该属性。`);
  }
  run(type: boolean): boolean {
      return true
  }
}
```

### 抽象类
```ts
// 抽象类：使用场景
// 一个类如果在实例化后没有任何用处，那么就可以定义为抽象类
abstract class A {
  name: string
  constructor(name: string) {
    this.name = name
  }
  // 抽象类，只定义，不实现
  abstract getName():string

}

class B extends A {
  constructor() {
    super('fx')
  }
  getName(): string {
    return '非抽象类“B”不会实现继承自“A”类的抽象成员“getName”。'
  }
}
```

## 类型别名
```ts
// 类型别名
type s = string
const str:s = 'xxx'

// 函数别名
type cb = ()=> string
const fn3:cb = ()=> '13'

// 值类型别名

type T = 'off' | 'on' | boolean | 5
let str4: T = true
```

## never类型
```ts
let strr: string & number //  此时 strr 的类型是 never 类型，一个变量不可能即是 string类型，又是数字类型

// 一个函数，抛出异常，永远没有返回值，所以可以使用 never类型
function error(message: string):never {
    throw new Error(message)
}

// 死循环永远不会退出，无返回值，所以也可以用 never类型
function loop():never {
    while(true) {}
}

// never类型使用场景
export interface Dog1 {
    type: 'Dog'
}

export interface Cat1 {
    type: 'Cat'
}

export interface Mouse1 {
    type: 'Mouse'
}

type All = Dog1 | Cat1 | Mouse1

function type(val: All) {
    switch (val.type) {
        case 'Dog':
            break
        case 'Cat':
            break
        case 'Mouse':
            break
        default:
            // 使用 never 处理兜底行为
            const check:never = val
            break
    }
}
```

## symbol 类型
```ts
// symbol 类型  可以传递参数做为唯一标识，只支持 string 和 number 类型的参数
let sym1 = Symbol('fx')
let sym2 = Symbol(123)

let fxObj = {
  [sym1]: 'symbal变量作为key1',
  [sym2]: 'symbal变量作为key2',
  name: 'fx',
  age: 20
}
// symbol 类型的值作为key值时，是无法被迭代到的（for in   Object.keys()   Object.getOwnPropertyNames() ）
for(let key in fxObj) {
  console.log(key); // name，age
}

console.log(JSON.stringify(fxObj)); // 也无法获取到

// 可以使用 getOwnPropertySymbols() 获取
console.log(Object.getOwnPropertySymbols(fxObj)); // [ Symbol(fx), Symbol(123) ]

// 使用 Reflect.ownKeys(obj) 获取到该对象上的所有属性
// [ 'name', 'age', Symbol(fx), Symbol(123) ]
console.log(Reflect.ownKeys(fxObj));
```

## symbol.iterator 迭代器 和 生成器 for of
```ts

```

## 泛型

### 简单了解
```ts
// 泛型 语法
// 函数名后面跟一个<参数>,参数名随意，当使用这个函数的时候把 参数的类型传进去就行了（即动态类型）
function add<T>(a:T, b:T): Array<T> {
  return [a,b]
}

add<number>(1,2)


// 泛型多类型
function sub<T,U>(a:T, b:U): Array<T | U> {
  let arr:Array<T| U> = [a,b]
  return arr
}

sub<number,boolean>(1, false)
sub(2, true)

// 泛型约束

interface Len {
  length: number
}
// arg 不一定有length属性，通过 extends 来继承一个类型，来约束只能传有length属性的参数类型
function getLength<T extends Len>(arg:T) {
  return arg.length
}

getLength([1,2,3])
```

### keyof 关键字 约束泛型对象的 key
```ts
// 问题场景
function prop<T>(obj: T, key: string) {
  return obj[key]
}

let o = {a:1, b:2,c:3}
prop(o, 'd') // 此时 对象o 是没有  d 这个key的

// 解决方案
// 首先定义一个 T类型，然后使用 extends 关键字继承 object类型的子类型，然后使用 keyof操作符获取 T类型的所有键，它的返回类型是联合类型
function prop2<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]
}
```

### 定义泛型类
```ts
// 定义泛型类
class Sub<T> {
  attr:T[] = []
  add(a:T):T[] {
    return [a]
  }
}

let s = new Sub<number>()
s.attr = [1,2,3]
s.add(123)
```
