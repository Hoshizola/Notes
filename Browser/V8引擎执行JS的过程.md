## V8引擎的原理

#### V8引擎的定义

- `V8`使用`C++`编写的Google开源高性能JavaScript和WebAssembly引擎，它用于Chrome和Node.js等
- `V8`可以独立运行，也可以嵌入到任何`C++`应用程序中

#### V8引擎架构图

![v8引擎原理架构图](C:\Users\nobod\Pictures\图片-学习资料\v8引擎原理架构图.png)

- `Parse`模块会将`JavaScript`代码转换成`AST`抽象语法树，这是因为解释器并不直接认识`JavaScript`代码（如果函数没有被调用，是不会生成`AST`的）

- `Ignition`模块是一个解释器，会将`AST`转换成字节码

  - 同时会收集`TurboFan`优化所需要的信息（比如函数参数的类型信息，有了类型才能进行真实的运算）
  - 如果函数只调用一次，`Ignition`会执行解释执行字节码

- `TurboFan`是一个编译器，可以将字节码编译为CPU可以直接执行的机器码

  - 如果一个函数被调用多次，那么就会被标记为**热点函数**，那么就会经过`TurboFan`**转换成优化的机器码**，提高代码的执行性能
  - 但是，机器码实际上也会被还原成字节码，这是因为如果后续执行函数的过程中，**类型发生了变化**（比如sum函数原来参数类型是number类，后来变成string类），之前优化的机器码并不能正确的处理运算，就会逆向地转换成字节码

  

#### V8引擎解析过程

![V8引擎解析图](C:\Users\nobod\Pictures\图片-学习资料\V8引擎解析图.png)

- 首先，`Blink`内核将`js`源码交给`V8`引擎，将`Stream`获取到的源码数据流进行编码转换
- `Scanner`会进行词法分析，将代码转换成`tokens`
- `tokens`会被转换成`AST`树，经过`Parser`和`PreParser`
  - `Parser`就是直接将`tokens`转换成`AST`
  - `PreParser`是预解析
    - 因为并不是所有的`JavaScript`代码，在一开始就会被执行，那么对所有的`JavaScript`代码进行解析，必然会影响网页的运行效率
    - 所以`V8`引擎实现了`Lazy Parsing`（延迟解析），作用是将不必要的函数进行预解析，也就是只解析暂时需要的内容，而对函数的全量解析是在函数被调用时才会进行
- 生成`AST`树后，会被`Ignition`转成字节码，之后的过程就是代码的执行过程

## V8执行JS

#### 初始化全局对象

- js引擎会在执行代码前，在堆内存中创建一个全局对象：`Global Object(go)`
  - 该对象所有作用域`(scope)`都可以访问
  - 里面会包含`Date`，`String`，`Array`，`Number`，`setTimeout`，`setInterval`等
  - 其中还有一个`window`属性指向自己`(this)`

#### 执行上下文栈（调用栈）

- js引擎内部有一个执行上下文栈(`Execution Context Stack`，简称`ECS`)，它是用于执行代码的调用栈，它要执行的是全局代码块
  - 全局代码块为了执行会构建一个`Global Execution Context(GEC)`
  - `GEC`会被放入到`ECS`中执行
- `GEC`会被放入到`ECS`中，里面包含两个部分内容
  - 第一部分，在代码执行前，在parse转成AST的过程中，会将全局定义的变量、函数等加入到`Global Object`中，但是不会赋值，因为此时代码还没有执行，这个过程也成为变量的提升
  - 第二部分，在代码执行中，对变量赋值，或者执行其他的函数