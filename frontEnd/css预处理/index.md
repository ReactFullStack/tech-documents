# CSS 预处理

* [基础](#基础)
* [CSS预处理器](#CSS预处理器)
* [预处理器的优缺点](#预处理器的优缺点)

## 基础
 - 概念
    ```
        CSS预处理器是一种专门的编程语言，用来为CSS增加一些编程特性（CSS本身不是编程语言）。
        不需要考虑浏览器兼容问题，因为CSS预处理器最终编译和输出的仍是标准的CSS样式。
        可以在CSS预处理器中：使用变量、简单逻辑判断、函数等基本编程技巧。
    ```
 - CSS预处理器主要目的
   - CSS语法不够强大（例如：CSS选择器无法进行嵌套，导致CSS中存在较多重复的选择器语句）；CSS中无法定义变量以及没有合理的样式复用机制，导致整体CSS样式难以维护。
   - 为了减少CSS代码冗余，为CSS提供样式复用机制，提高CSS代码的可维护性

- 主流CSS预处理器
  - Sass
  - Less
  - Stylus
  - PostCSS
- CSS预处理器的使用
    ```
        集成在前端开发工具中使用（插件），如 VS Code、WebStorm 等
        集成在项目构建工具中，在项目编译（打包）时进行自动转换，如 webpack、gulp等
    ```
## CSS预处理器
### 1. Sass
 - [Sass教程](https://ruanyifeng.com/blog/2012/06/sass.html)
 - [Sass文档](https://www.sass.hk/guide/)
### 2. Less
 - [Less教程](https://www.cnblogs.com/landeanfen/p/6047031.html)
 - [Less文档](https://less.bootcss.com/#%E6%A6%82%E8%A7%88)
### 3. Stylus
 - [Stylus教程](https://www.jianshu.com/p/5fb15984f22d) 
 - [Stylus文档](https://stylus.bootcss.com/)
### 4. PostCSS
 - PostCSS 一种对css编译的工具，类似babel对js的处理，常见的功能如：
    1. 使用下一代css语法
    2. 自动补全浏览器前缀
    3. 自动把px代为转换成rem
    4. css 代码压缩等等
 - 常用的postcss插件
    1. Autoprefixer
      ```javascript
        前缀补全，全自动的，无需多说
        安装：
        cnpm install Autoprefixer --save-dev
      ```
    2. postcss-cssnext
    ```javascript
      使用下个版本的css语法【关于语法另一篇文章会单独讲】
      安装：
      cnpm install postcss-cssnext --save-dev
    ```
    3. postcss-pxtorem
    ```javascript
      把px转换成rem
      安装：
      cnpm install postcss-pxtorem --save-dev
    ```
 - [PostCSS教程](https://www.jianshu.com/p/8001a2885f71)
 - [PostCSS文档](https://www.postcss.com.cn/)

 ## 预处理器的优缺点
  - Less 
  ```
  从语言特性的设计到功能的健壮程度和另外两者相比都有一些缺陷，但用 Less 可以满足大多数场景的需求。

  但相比另外两者，基于 Less 开发类库会复杂得多，实现的代码会比较脏，能实现的功能也会受到 DSL 的制约。

  比 Stylus 语义更清晰、比 Sass 更接近 CSS 语法，使得刚刚转用 CSS 预编译的开发者能够更平滑地进行切换。
  ```
 - Sass
 ```
  在三者之中历史最久，也吸收了其他两者的一些优点。

  从功能上来说 Sass 大而全，语义明晰但是代码很容易显得累赘。

  主项目基于 Ruby 可能也是一部分人不选择它的理由（Less 开始也是基于 Ruby 开发，后来逐渐转到 less.js 项目中）。

  Sass 有一个「事实标准」库——Compass，于是对于很多开发者而言省去了选择类库的烦恼，对于提升开发效率也有不小的帮助。
```
- Stylus 
```
  Stylus的语法非常灵活，很多语义都是根据上下文隐含的。

  基于 Stylus 可以写出非常简洁的代码，但对使用团队的开发素养要求也更高，更需要有良好的开发规范或约定。
```
- PostCSS
```
  postcss 既不是 预处理器也不是 后处理器，其功能比较广泛，而且重要的一点是，postcss可以和less/sass结合使用
```