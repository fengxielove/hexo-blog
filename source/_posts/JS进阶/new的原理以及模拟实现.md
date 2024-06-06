---
title: new 关键字的原理与实现
excerpt: 助你掌握new的执行顺序，以及 call 和 apply 的使用
date: 2023年1月12日 19:17:32
tags: [JS进阶]
categories: [javascript]
---


相信大家和我一样在刷面试题的时候，会经常遇到关于this指向的问题，那么必然也就会遇到 new 关键字干了什么的问题。许久以来一直都没有去想过一些深层的东西，总觉得会用就行了。。。于此不再言，希望此篇文章会成为我下一个阶段的开始！

## 面经中的 new
1. 创建一个新的空对象
2. 将 this 指向这个空对象
3. 执行 构造函数中的代码
4. 没有显式返回非空对象时，则默认返回这个对象

## MDN中的 new
new 运算符 创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。
描述-- new 关键字会进行如下的操作：
1. 创建一个空的简单 JavaScript 对象（即 {}）；
2. 为步骤 1 新创建的对象添加属性 __proto__，将该属性链接至构造函数的原型对象；
3. 将步骤 1 新创建的对象作为 this 的上下文；
4. 如果该函数没有返回对象，则返回 this。

### new example：
```javascript
function Car(color) {
    this.color = color
}

Car.prototype.desc = function() {
console.log(`this is a ${this.color} car`)
}

let blueCar = new Car('blue')
console.log(blueCar.color)  // blue
blueCar.desc() // this is a blue car
```

## 版本一：
```javascript
//	new 为关键字，这里我们使用 reNew 代替
function reNew() {
// 创建一个空对象
let obj = new Object()
// 获取 new() 时传递的构造函数
let rConstructor = [].shift.call(arguments)
// 链接到原型，使得 obj 可以访问 构造函数原型中 的属性
obj.__proto__ = rConstructor.prototype

//  rConstructor.apply(obj, arguments) // 这里体现一下 apply与call的用法区别，详细区别下一篇文章说明
// 绑定 this ，让 obj 可以访问 构造函数中 的属性
// rConstructor.apply(obj, arguments)
rConstructor.call(obj, ...arguments)

// 返回创建的对象
return obj
}

let rNewCar = reNew(Car, 'red')
console.log(rNewCar.color) // red
rNewCar.desc() // this is a red car
```

### 存在的问题： 首先要考虑到构造函数会有不同情况的返回值。
    ● 返回一个对象
    ● 没有return，即返回 undefined
    ● 返回 undefined 以外的其他基本类型

#### 问题一举例：
```javascript
function Car(color, name) {
  this.color = color
  return {
    name
  }
}

let blueCar = new Car('blue', '深蓝SL03')
console.log(blueCar.color) // undefined
console.log(blueCar.name) // 深蓝SL03
```
此时，实例对象中只能访问到 返回的对象中的属性。

#### 问题二举例：
```javascript
function Car(color, name) {
  this.color = color
}

let blueCar = new Car('blue', '深蓝SL03')
console.log(blueCar.color) // blue
console.log(blueCar.name) // undefined
```
此时，实例对象中只能访问到 构造函数中的属性。

#### 问题三举例：
```javascript
function Car(color, name) {
  this.color = color
  return false
}

let blueCar = new Car('blue', '深蓝SL03')
console.log(blueCar.color) // blue
console.log(blueCar.name) // undefined
```
返回 非对象时，实例中只能访问到 构造函数中的属性， 和情况一完全相反，结果相当于没有返回值。

    总结：在 rNew 的实现中，返回时需要判断一下返回的值是不是一个对象，如果是对象则返回这个对象，否则返回新创建的 obj 对象。

## 版本二：
    Object.create() 方法用于创建一个新对象，使用现有的对象来作为新创建对象的原型（prototype）。
```javascript
function rNew2() {
    // 获取构造函数
    let rConstructor = [].shift.call(arguments)
    // 新建一个空对象, 并且链接到原型
    let obj = Object.create(rConstructor.prototype)
    // 将构造函数的 this 显式指向 新创建的对象
    let finalObj = rConstructor.apply(obj, arguments)
    // 优先返回 构造函数返回的对象，其次返回 具有构造函数的内置对象
    return finalObj instanceof Object ? finalObj : obj
}
```
