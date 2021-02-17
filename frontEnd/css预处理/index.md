# CSS 预处理

* [基础](#基础)
* [CSS预处理器](#语义化)

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
## 预处理器
### 1. Sass
 - [sass文档](https://www.sass.hk/guide/)
### 2. Less
 - [less文档](https://less.bootcss.com/#%E6%A6%82%E8%A7%88)
### 3. Stylus
 - [stylus]
 (https://stylus.bootcss.com/)
### 4. PostCSS
 - [postcss文档](https://www.postcss.com.cn/)