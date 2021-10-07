# JS 继承

本次内容主要总结了`ES5`中几种继承方式。

### 1. 原型链继承

```javascript
function SuperType() {
  this.property = true
}
SuperType.prototype.getSuperValue = function() {
  return this.property
}
function SubType() {
  this.property = false
}
SubType.prototype = new SuperType()
SubType.prototype.getSubValue = function() {
  return this.property
}
let instance = new SubType()
console.log(instance.getSuperValue())
```

- 原型链继承问题：
  - 原型中包含的引用值会在所有实例间共享
  - 子类型在实例化时不能给父类型的构造函数传参

### 2. 盗用构造函数

- 解决原型链继承的问题
- 基本思路是：在子类构造函数中调用父类构造函数。**函数是特定上下文中执行代码的简单对象**，所以可以使用`apply()`和`call()`方法以新创建的对象为上下文执行构造函数。

```javascript
function SuperType() {
  this.colors = ['red', 'green','blue']
}
function SubType() {
  // 继承SuperType，解决引用类型属性在实例间共享问题
  SuperType.call(this)
}
let instance1 = new SubType()
instance1.colors.push('black')
console.log(instance1.colors)
let instance2 = new SubType()
console.log(instance2.colors)
// =============================================================
function SuperType(name) {
  this.name = name
} 
function SubType(age) {
  // 解决不能向父类构造函数传参的问题
  SuperType.call(this, 'Mike')
  this.age = age
}
let instance = new SubType(20)
console.log(instance.name, instance.age)
```

- 盗用构造函数问题：
  - 必须在构造函数中定义方法，因此函数不能重用
  - 子类不能访问父类原型上定义的方法

### 3. 组合继承

- 组合继承综合了原型链和盗用构造函数。基本思路是：使用原型链继承原型上的属性和方法，通过盗用构造函数继承实例属性。这样既能够重用原型上的方法，又能让每个实例拥有自己的属性。

```javascript
function SuperType(name) {
  this.name = name
  this.colors = ['red', 'green', 'blue']
}
SuperType.prototype.sayName = function() {
  console.log(this.name)
}
function SubType(name, age) {
  // 继承SuperType
  SuperType.call(this, name)
  // 子类自身属性
  this.age = age
}
SubType.prototype = new SuperType()
SubType.prototype.sayAge = function() {
  console.log(this.age)
}
let instance1 = new SubType('Jack', 20)
instance1.colors.push('yellow')
console.log(instance1.colors)
instance1.sayName() // 访问父类原型上的方法
instance1.sayAge()  // 子类自身的方法

let instance2 = new SubType('Rose', 25)
console.log(instance2.colors)
instance2.sayName()
instance2.sayAge()
```

- 问题：效率问题——父类构造函数始终会被调用两次，一次是在子类构造函数中调用，一次是在创建子类原型对象时被调用

### 4. 原型式继承

- 非常适合不需要单独创建构造函数，但仍然需要在对象间共享信息的场合。但是，属性中包含的引用值始终会在相关对象间共享

```javascript
function object(o) {
  function F() {}
  F.prototype = o
  return new F()
}
let person = {
  name: 'Mike',
  friends: ['Jack', 'Rose', 'Jhon']
}
let antherPerson = object(person)
antherPerson.name = 'Gray'
antherPerson.friends.push('Bob')

let yetAnotherPerson = object(person)
yetAnotherPerson.name = 'Linda'
yetAnotherPerson.friends.push('Jerry')
console.log(yetAnotherPerson.friends)
```

### 5. 寄生式继承

- 关注对象，不在乎类型和构造函数

```javascript
function object(o) {
  function F() {}
  F.prototype = o
  return new F()
}
function createAnother(original) {
  let clone = object(original)
  // 增强对象
  clone.sayHi = function() {
    console.log('Hello World')
  }
  return clone
}
let person = {
  name: 'Mike',
  friends: ['Jack', 'Rose', 'Jhon']
}
let anotherPerson = createAnother(person)
anotherPerson.sayHi()
```

- 问题：给对象添加函数会导致函数难以重用

### 6. 寄生式组合继承

- 可以解决组合继承中两次调用父类构造函数导致的效率问题。基本思路是不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本。

```javascript
function object(o) {
  function F() {}
  F.prototype = o
  return new F()
}
function inheritPrototype(subType, superType) {
  // 创建父类原型副本作为子类原型
  let prototype = object(superType.prototype)
  // 将子类原型的构造器指向子类
  prototype.constructor = subType
  // 父类原型副本赋给子类原型
  subType.prototype = prototype
}

function SuperType(name) {
  this.name = name
  this.colors = ['red', 'green', 'blue']
}
SuperType.prototype.sayName = function() {
  console.log(this.name)
}
function SubType(name, age) {
  SuperType.call(this, name)
  this.age = age
}
inheritPrototype(SubType, SuperType)
SubType.prototype.sayAge = function() {
  console.log(this.age)
}
let instance = new SubType('Jackson', 25)
instance.sayName() // 父类原型上的方法
instance.sayAge() // 子类原型上的方法
```

### 总结

本文内容参考于《JavaScript高级程序设计》，由于该部分内容在面试中属于高频考点，所以整理出来，方便后面复习。
