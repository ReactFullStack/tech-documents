# Npm Scripts
* [简介](#简介)
* [基础](#基础)
    - [原理](#原理)
    - [通配符](通配符)
    - [内置的scripts](#内置的scripts)
    - [传参](#传参)
    - [环境变量](#环境变量)
* [进阶](#进阶)
    - [生命周期](#生命周期)
    - [钩子](#钩子)
* [常见问题](#常见问题)
* [参考资料](#参考资料)  

## 简介

npm scripts 是指Npm 对于package.json文件中"scripts"属性的处理，通过该属性，npm 可以执行命令。

## 基础

### 原理
每当执行npm run，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面。

比较特别的是，npm run新建的这个 Shell，会将当前目录的node_modules/.bin子目录加入PATH变量，执行结束后，再将PATH变量恢复原样。

这意味着，当前目录的node_modules/.bin子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径。

由于 npm 脚本的唯一要求就是可以在 Shell 执行，因此它不一定是 Node 脚本，任何可执行文件都可以写在里面。

npm 脚本的退出码，也遵守 Shell 脚本规则。如果退出码不是0，npm 就认为这个脚本执行失败。

### 通配符 
由于 npm 脚本就是 Shell 脚本，因为可以使用 Shell 通配符。
```ts
"lint": "jshint *.js"
"lint": "jshint **/*.js"
```
上面代码中，*表示任意文件名，**表示任意一层子目录。

如果要将通配符传入原始命令，防止被 Shell 转义，要将星号转义。
```ts
"test": "tap test/\*.js"
```

### 内置的script
npm 为几个最常使用的scripts内置了简写命令：
- test：通过npm test执行；
- start：通过npm start执行；
    - 默认值："node server.js"
- stop：通过npm stop执行；
- restart：通过npm restart执行；npm restart是一个复合命令，实际上会依次执行三个脚本命令：stop、restart、start。

### 传参
向 npm 脚本传入参数，要使用--标明。
```ts
"lint": "jshint **.js"
```
向上面的npm run lint命令传入参数，必须写成下面这样。
```shell
$ npm run lint --  --reporter checkstyle > checkstyle.xml
```
也可以在package.json里面再封装一个命令。

```ts
"lint": "jshint **.js",
"lint:checkstyle": "npm run lint -- --reporter checkstyle > checkstyle.xml"
```

### 环境变量
通过`npm_package_`前缀，npm 脚本可以拿到package.json里面的字段

若package.json中有{"version":xxx,"name":xx},则npm 在scripts中添加环境变量： npm_package_name 和 npm_package_version。
在*.js文件中可以使用process.env.npm_package_xxx引入

```ts
// package.json
{
    version: '1.1.0',
    name: 'example'
}

// *.js
console.log(process.env.npm_package_name); // eacmple
console.log(process.env.npm_package_version); // 1.2.0

```
npm 脚本还可以通过`npm_config_`前缀，拿到 npm 的配置变量，即npm config get xxx命令返回的值。比如，当前模块的发行标签，可以通过npm_config_tag取到。

```ts
{
    "config": {
        "port": 1111
    }
    //则scripts中可以引入
    "scripts":{//bash脚本
        "xxx":"xxx ${npm_package_config_port}/$npm_package_config_port"
    }
}
```
> 注意: package.json里面的config对象，可以被环境变量覆盖

此外，npm还提供一个npm_lifecycle_event变量，返回当前正在运行的脚本名称，比如pretest、test、posttest等等。所以，可以利用这个变量，在同一个脚本文件里面，为不同的npm scripts命令编写代码。

## 进阶

### 生命周期
有一些特殊的生命周期脚本仅在某些情况下发生。这些脚本是除`pre`和`post`脚本之外发生的。

1. prepare
    - 包装前运行
    - 在发布包之前运行
    - 在本地运行，npm install不带任何参数
    - 运行之后prepublish，但之前prepublishOnly
注意：如果通过git安装的软件包包含prepare脚本，则在打包和安装软件包之前dependencies，devDependencies将安装和，并运行prepare脚本。
2. prepublish（弃用）
3. prepublishOnly：在包装准备好并包装之前运行，仅在上进行npm publish
4. prepack
- 在压缩包打包之前运行（在`npm pack`，`npm publish`以及安装git依赖项之前）。
- 注意：`npm run pack`与`npm pack`不同。`npm run pack`是用户定义的任意脚本名称，其中`npm pack`是CLI定义的命令。
5. postpack：在生成tarball之后将其运行并移至其最终目的地。

#### 生命周期操作顺序
- npm publish
    + prepublishOnly
    + prepare
    + prepublish
    + publish
    + postpublish
- npm pack
    + prepack
    + postpack
- npm install
    + preinstall
    + install
    + postinstall
    + prepublish （在本地时）
    + prepare （在本地时）
- npm start
    + prestart
    + start
    + poststart

### 钩子
npm 脚本有pre和post两个钩子。举例来说，build脚本命令的钩子就是prebuild和postbuild。
    
```ts
{
    "scripts":{
        'prexxx':,
        'xxx':,
        'postxxx':,
    }
}
```
    
用户执行npm run build的时候，会自动按照下面的顺序执行。

```shell
npm run prebuild && npm run build && npm run postbuild
```
因此，可以在这两个钩子里面，完成一些准备工作和清理工作

==自定义的脚本命令也可以加上pre和post钩子。但是双重的pre和post无效，比如prepretest和postposttest是无效的==

### 默认提供的钩子
- prepublish
- postpublish
- preinstall
- postinstall
- preuninstal
- postuninstall
- preversion
- postversion
- pretest
- posttest
- prestop
- poststop
- prestart
- poststart
- prerestart
- postrestart

## 常见问题

1. [为什么使用 npm Scripts 构建项目?](https://www.open-open.com/lib/view/open1487819024678.html)
    - npm scripts 并不需要熟悉命令行技能，当然熟悉的话更好；
    - npm scripts 可以完成绝大多数 Grunt 与 Gulp完成的任务，实在不行还可以写 NodeJS 代码来完成；
    - 因为 npm scripts 在一个 Shell 环境中执行的，而 Shell 天生支持流；
    - 几个常见的命令操作符&&/</>/|是跨平台的，对于一些 Linux/Mac 中的 Shell 命令可以使用 shelljs 来实现跨平台；
    
    然而 npm scripts 的不足是由于package.json文件不可以写注释，对于复杂的构建任务，代码可读性很差。这个可以尽量通过贴切的命名，任务细分来减轻。另外就是需要熟悉各构建工具的命令行使用方式。

## 参考资料
1. http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html
2. https://docs.npmjs.com/cli/v6/using-npm/scripts