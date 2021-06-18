## webpack学习笔记：开发环境

### 1、了解webpack

- 什么是webpack
  - webpack是一个模块打包器。
  - 在webpack看来，前端的所有资源文件(js/json/css/img/less...)都会作为模块处理
  - 它将根据模块的依赖关系进行静态分析，生成对应的静态资源
- 五个核心概念
  - Entry：入口文件，指示webpack应该使用哪个模块来作为构建其内部依赖图的开始
  - Output：告诉webpack在哪里输出它所创建的bundles，以及如何命名这些文件，默认值为`./dist/`
  - Loader：让webpack能处理那些非`js`和`json`文件
  - Plugins：可以用于执行范围更广的任务，插件的范围包括从打包优化到压缩，一直到重新定义环境中的变量等
  - Mode：模式，有生产模式`production`和开发模式`development`
- 理解Loader
  - webpack本身只能加载js和json模块，如果要加载其他类型的文件，就需要使用对应的loader进行转换
  - Loader本身也是运行在node.js环境中的Javascript模块
  - 它本身是一个函数，接受源文件作为参数，返回转换后的结果
  - loader一般是以xxx-loader的方式命名，xxx代表了这个loader的转换功能，比如`css-loader`
- 理解Plugins
  - 插件可以完成一些loader不能完成的功能
  - 插件的使用一般是在webpack的配置信息plugins选项中指定
- 配置文件
  - webpack.config.js：是一个node模块，采用CommonJs模块化规范，返回一个json格式的配置信息对象

### 2、开启项目

- 初始化项目

  - 生成package.json文件

    ```shell
    npm init 
    ```

- 安装webpack
  - npm install webpack webpack-cli -g   全局安装，作为指令使用
  - npm install webpack webpack-cli -D  本地安装，作为本地依赖使用

### 3、编译打包应用

- 创建js文件

  - src/js/index.js

    ```javascript
    /* 
      该index.js不同于模块化时汇总js的文件
      模块化技术的index.js只用于汇总各个js模块
      该index.js是webpack的入口文件，可以用于汇总：js、json、css、图片、音视频...
    */
    import { sum } from './module1'
    import { sub } from './module2'
    import m3 from './module3'
    import a from '../json/test.json'
    import '../css/index.less'
    
    console.log(sum(1, 2))
    console.log(sub(3, 4))
    console.log(m3.mul(2, 5))
    console.log(m3.div(2, 5))
    console.log(a)
    
    // webpack只管翻译es6的模块语法变为浏览器认识的，但是不会处理其他新语法，打包后箭头函数语法依然存在
    setTimeout(() => {
      console.log('定时器执行啦')
    },1000)
    ```

  - src/js/module1.js

    ```javascript
    // 分别暴露--求和函数
    export function sum(a, b) {
      return a + b
    } 
    ```

  - src/js/module2.js

    ```javascript
    // 统一暴露--减法函数
    function sub(a, b) {
      return a - b
    }
    export {sub}
    ```

  - src/js/module3.js

    ```javascript
    // 默认暴露--乘法和除法函数
    export default {
      mul(a, b) {
        return a * b
      },
      div(a, b) {
        return a / b
      }
    }
    ```

- 创建json文件

  - src/json/test/json

- 创建主页面

  - src/index.html

    ```html
    <html lang="en">
    <head>
      <title>Document</title>
      <script src="../dist/js/main.js"></script>
    </head>
    <body>
    </body>
    </html>
    ```

- 运行指令

  - 开发环境配置：webpack ./src/js/index.js -o ./dist/js --mode development
  - 生产环境配置：webpack ./src/js/index.js -o ./dist/js --mode production  
    - 在开发环境配置上多了压缩代码

- 结论

  - webpack能够编译打包js和json文件
  - 能将es6的模块化语法转换成浏览器能识别的语法
  - 能压缩代码

- 缺点

  - 不能编译打包css、img等文件
  - 不能将js的es6基本语法转为es5及以下的语法

### 4、打包css或者less资源

- 创建css或者less文件

  - src/css/index.less

- 入口文件引入less文件

  - ```shell
    import '../css/index.less'
    ```

- 安装loader

  - ```shell
    install i less-loader css-loader style-loader -D
    ```

- 配置loader

  ```json
  module.exports = {
    entry: './src/js/index.js',
    output: {
      path: path.resolve(__dirname, 'dist/js'),
      filename: 'build.js'
    },
    module: {
      rules: [
        {
          // 匹配哪些文件
          test: /\.css$/,
          // 使用哪些loader进行处理，use中的执行顺序是从下到上，从右到左
          use: [
            // 常见style标签，将js中的样式资源插入进行，添加到head标签中
            'style-loader',
            // 将css文件变成commonjs模块加载进js中，里面内容是样式字符串
            'css-loader'
          ]
        },
        {
          test: /\.less/,
          use: [
              'style-loaser', 
              'css-loader',
              'less-loader' // 将less文件编译为css，但不生成单独的css文件，在内存中
          ]
        }
      ]
    },
    // 工作模式
    mode: 'production'
  }
  ```

### 5、js语法检查

- 安装loader

  - npm install eslint-loader eslint -D

- 配置loader

  ```json
  module: {
      rules: [
        /* 
          语法检查：eslint-loader 
            注意：只检查自己写的源代码，不检查第三方库地代码
            设置检查规则：
              在package.json的eslintConfig设置
              airbnb风格 eslint-config-airbnb-base eslint-plugin-import eslint
        */
        {
          test: /\.js$/,
          exclude: /node_modules/,
          loader: 'eslint-loader',
          options: {
            // 检查规则不在optios中配置，vue底层就是这么做的，效率较高
          }
        }
      ]
    }
  ```

- 在package.json中配置检查规则

  ```json
  "eslintConfig": {
      "parserOptions": {
          "ecmaVersion": 6,  // 支持es6语法
          "sourceType": "module"  // 使用es6模块化
      },
      "env": {  // 设置环境
          "browser": true, // 支持浏览器环境，能够使用window上的全局变量
          "node": true  // 支持服务器环境，能够使用node上的全局变量
      },
      "globals": {
          "$": "readonly", // 把$设置成只读，因为$不在es6语法中出现
          "Promise": "readonly"
      },
      "rules": {
          "no-console": "off", // 不检查console
          "eqeqeq": "warn", // 用==会警告，必须用===
          "no-alert": "error"  // 使用alert会报错
      },
      "extends": "eslint:recommended" // 使用eslint推荐的规则
  }
  ```


### 6、js语法转换

- 概述：将浏览器不能识别的新语法转换为能识别的旧语法，做兼容性处理

- 安装loader

  - npm install babel-loader @babel/core @babel/preset-env -D

- 配置loader

  ```json
  module: {
      rules: [
        // js语法转换
        {
          test: /\.js$/,
          exclude: /node_modules/,
          use: {
            loader: 'babel-loader',
            options: {
              presets: ['@babel/preset-env']
            }
          }
        }
      ]
  }
  ```

### 7、js兼容性处理

#### 第一种方法：使用经典的polyfill

- 安装  npm install @babel/polyfill

- 使用

  ```javascript
  // 在index.js中加上
  import '@babel/polyfill'
  ```

- 优点：解决babel只能转换部分es6新语法的问题（如let/const/解构赋值......），但是不能转换高级语法（如Promise......)

- 缺点：将所有高级语法都进行了转换，但实际上只使用了一部分甚至都没有使用

- 解决：需要按需加载

#### 第二种方法：借助core-js按需引入

- 安装  npm install core-js

- 使用

  ```json
  // js语法转换
        {
          test: /\.js$/,
          exclude: /node_modules/,
          use: {
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env',
                  {
                    'useBuiltIns': 'usage', // 按需引入需要使用polyfill
                    'corejs': {version: 3}, // 解决不能找到core-js的问题
                    'targets': {  // 指定兼容性处理哪些浏览器
                       'chrome': '58',
                       'ie': 9
                    }
                  }
                ],
                cacheDirectory: true // 开启babel缓存
              ]
            }
          }
        }
  ```

### 8、打包样式中的图片资源

- 安装loader  

  - npm install file-loader url-loader -D
  - url-loader是file-loader的上层封装，使用时需配合file-loader使用
  - url-loader比file-loader多了自动识别图片大小的功能，能够将8kb(一般设置为8kb)转为base64编码

- 配置loader

  ```json
  // 处理图片
  {
      test: /\.(png|jpg|gif)$/,
      use: {
          // loader: 'file-loader',
          loader: 'url-loader',
          options: {
              limit: 8 * 1024, // 8kb以下的图片会base64处理，处理后会比原来图片大小还小
              outputPath: 'images', // 文件本地输出路径
              publicPath: '../dist/images', //决定图片的url路径，不设置的话会到src目录下找
              name: '[hash:8].[ext]' // 取文件名称hash值前8位
          }
      }
  }
  ```

### 9、打包html文件

- html文件webpack不能解析，需要借助插件编译解析

- 在html文件中不要引入任何css或js文件

- 安装插件Plugins  npm install html-webpack-plugin -D

- 在webpack.config.js中引入插件（插件都需要手动引入才能使用，而loader会自动加载）、

  - const HtmlWebpackPlugin = require('html-webpack-plugin')

- 配置插件

  ```json
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  
  module.exports = {
    // ......
    plugins: [
      new HtmlWebpackPlugin({
        // 用给定的html作为模板，原来的结构不会变，还会自动引入打包的资源
        template: './src/index.html'
      })
    ],
    // 工作模式
    mode: 'development'
  }
  ```

### 10、自动编译打包运行

- 安装

  - npm install webpack-dev-server -D

- 修改webpack.config.js文件

  ```javascript
  devServer: {
      open: true,  // 自动打开浏览器
      compress: true,  // 自动gzip压缩
      port: 3000  // 端口号
  }
  ```

- 修改package.json中的scripts指令
  - "start": "webpack-dev-server"
- 运行指令：npm run start
  - 注意webpack-dev-server指令才能启动devServer配置，然后配置到package.json才行
- 出现问题：Error: Cannot find module ‘webpack-cli/bin/config-yargs’
  - 因为webpack-cli和webpack-dev-server版本不兼容，webpack-cli是4.x版本，webpack-dev-server是3.x版本。解决办法是降低webpack-cli版本。执行命令：`npm i webpack-cli@3.3.12 -D`就ok了

### 11、模块热更新（HMR）

- HMR允许在运行时更新所有类型的模块，而无需完全更新，只更新变化的模块，不更新不变的模块

- 修改devServer配置

  ```javascript
  devServer: {
      open: true,  // 自动打开浏览器
      compress: true,  // 自动gzip压缩
      port: 3000,  // 端口号
      hot: true // 开启热模块替换
  }
  ```

- 问题：html文件修改后不能进行热更新，因为html文件不在入口文件中

- 解决：把html文件加入entry中

  ```javascript
  entry: ['./src/js/index.js', './src/index.html']
  ```

### 12、devtool

- 概述：一种将压缩/编译文件中的代码映射回源文件中的原始位置的技术，让我们调试代码不再困难
- 介绍
  - cheap 只保留行，编译速度快
  - eval webpack生成的代码（每个模块彼此分开，并使用模块名称进行注释），编译速度快
  - inline 以base64方式将source-map嵌入到代码中，缺点是造成编译后的代码体积很大
- 推荐使用
  - 开发环境：cheap-module-eval-source-map
  - 生产环境：cheap-module-source-map

> 以上就是webpack开发环境的配置，可以在内存中自动打包所有类型文件并有自动编译运行，热跟新等功能







