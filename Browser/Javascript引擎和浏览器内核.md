## 认识JavaScript引擎

#### 为什么需要JavaScript引擎？

- 高级的编程语言都是需要转成最终的机器指令来执行的；
- 我们编写的`JavaScript`无论是交给浏览器还是`Node`执行，最后都是需要被`CPU`执行的；
- 而`CPU`只认识自己的指令集，实际上是机器语言，才能被`CPU`执行；
- 所以我们需要`JavaScript`引擎帮助我们将`JavaScript`代码翻译成`CPU`指令来执行。

#### 比较常见的JavaScript引擎有哪些？

- `SpiderMonkey`：第一款`JavaScript`引擎，由`JavaScript`作者开发；
- `Chakra`：微软开发，用于IE浏览器；
- `JavaScriptCore`：`WebKit`中的`JavaScript`引擎，`Apple`公司开发；
- `V8`：`Google`开发的强大`JavaScript`引擎，也帮助`Chrome`从众多浏览器中脱颖而出；
- `......`

## 认识浏览器内核

- 事实上，我们经常说的浏览器内核指的是浏览器的**排版引擎**`(layout engine)`，排版引擎也称为**浏览器引擎**`(browser engine)`，**页面渲染引擎**`(rendering engine)`或者**样版引擎**。
- 不同的浏览器有不同的内核：
  - `Gecko`：早期被`NetScape`和`Mozilla Firefox`浏览器使用；
  - `Trident`：微软开发，被IE浏览器使用，但是`Edge`浏览器已转向了`Blink`；
  - `Webkit`：苹果基于`KHTML`开发，用于`Safari`，`Google Chrome`之前也在使用；
  - `Blink`：是`Webkit`的一个分支，`Google`开发，目前应用于`Google Chrome`，`Edge`，`Opera`等

## 浏览器内核和JS引擎的关系

- 这里以`Webkit`为例，`Webkit`事实上由两部分组成：
  - `WebCore`：负责`HTML`解析、布局、渲染等等相关的工作；
  - `JavaScriptCore`：解析、执行`JavaScript`代码

