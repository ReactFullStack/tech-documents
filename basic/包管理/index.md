# 包管理
* [NPM](#npm)
    - [npm简介](#npm简介) 
    - [package.json](#package)
    - [npm5新版功能特性解析](#npm5新版功能特性解析)
* [NPX](#npx)
* [YARN](#yarn)

Feature | npm | yarn 
--- | --- | ---
初始化 | npm init | yarn init
安装包 | npm install xxx --save | yarn add xxx
移除包 | npm unintall xxx | yarn remove xxx
更新包 | npm update xxx | yarn upgrade
安装开发以来的包 | npm install xxx --save-dev | yarn add
全局安装 | npm install -g xxx | yarn global add xxx
设置下载镜像的地址 | npm config set registry url | yarn config set registry url
安装所有依赖 | npm install | yarn install
执行包 | npm run | yarn run

## npm
- [学习文档](https://javascript.ruanyifeng.com/nodejs/npm.html)

### npm简介
- npm 是 Node.js 标准的软件包管理器。

- 在 2017 年 1 月时，npm 仓库中就已有超过 350000 个软件包，这使其成为世界上最大的单一语言代码仓库，并且可以确定几乎有可用于一切的软件包。

- 它起初是作为下载和管理 Node.js 包依赖的方式，但其现在也已成为前端 JavaScript 中使用的工具。

### 下载
- 安装所有依赖 `npm install`
- 安装单个软件包 `npm install <package-name>`
    --save 安装并添加条目到 package.json 文件的 dependencies
    --save-dev 安装并添加条目到 package.json 文件的 devDependencies
- 更新软件包 `npm update`

### 安装
- 本地安装 `npm install lodash`
    - 软件包会被安装到当前文件树中的 node_modules 子文件夹下
- 全局安装 `npm install -g lodash`
    - 软件包不会再安装到本地文件夹下，而是使用全局的位置，确切的位置取决于设置
    - npm root -g 命令会告知其在计算机上的确切位置
- 当使用 npm install <package-name> 安装 npm 软件包时，是将其安装为依赖项。
    - dependencies：该软件包会被自动地列出在 package.json 文件中的 dependencies 列表下（在 npm 5 之前：必须手动指定 --save）。
    - devDependencies：当添加了 -D 或 --save-dev 标志时，则会将其安装为开发依赖项（会被添加到 devDependencies 列表）。
- --production 标志`npm install --production`，以避免安装这些开发依赖项

### 安装旧版本
- 使用 @ 语法来安装 npm 软件包的旧版本 `npm install <package>@<version>`
- 列出软件包所有的以前的版本 `npm view <package> versions`
- 选择要将软件包更新到的版本（当运行 npm update 时）
    - ^: 只会执行不更改最左边非零数字的更新
        - 如果写入的是 ^0.13.0，则当运行 npm update 时，可以更新到 0.13.1、0.13.2 等，但不能更新到 0.14.0 或更高版本
        - 如果写入的是 ^1.13.0，则当运行 npm update 时，可以更新到 1.13.1、1.14.0 等，但不能更新到 2.0.0 或更高版本。
    - ~: 如果写入的是 〜0.13.0，则当运行 npm update 时，会更新到补丁版本：即 0.13.1 可以，但 0.14.0 不可以。
    - >: 接受高于指定版本的任何版本。
    - >=: 接受等于或高于指定版本的任何版本。
    - <=: 接受等于或低于指定版本的任何版本。
    - <: 接受低于指定版本的任何版本。
    - =: 接受确切的版本。
    - -: 接受一定范围的版本。例如：2.1.0 - 2.6.2。
    - ||: 组合集合。例如 < 2.1 || > 2.6

### 使用
- 若要在代码中使用它，则只需导入到程序中
    - const _ = require('lodash')
    - import _ from 'lodash'
- 如果软件包是可执行文件
    - 可以输入 `./node_modules/.bin/cowsay` 来运行
    - 输入 `npx cowsay` 来运行

### 查看 npm 包安装的版本
- 查看所有已安装的 npm 软件包（包括它们的依赖包）的最新版本 `npm list`
- 适用于全局安装的软件包 `npm list -g`
- 若要仅获取顶层的软件包 `npm list --depth=0`
- 也可以通过指定名称来获取特定软件包的版本　｜｜　安装的软件包的依赖 `npm list minimist`
- 查看软件包在 npm 仓库上最新的可用版本 `npm view [package_name] version`

### 卸载 npm 软件包
- 卸载之前在本地安装的软件包 `npm uninstall <package-name>`
    - -S 或 --save 标志，则此操作还会移除 package.json 文件中的引用
    - -D 或 --save-dev 标志从devDependencies 文件中移除
- 如果该软件包是全局安装的 `npm uninstall -g <package-name>`

### nrm
- 原来包刚一开始是只存在于国外的NPM服务器，但是由于网络原因，经常访问不到，这时候，我们可以在国内，创建一个和官网完全一样的NPM服务器，只不过，数据都是从人家那里拿过来的，除此之外，使用方式完全一样；
- 淘宝镜像
- cnpm 镜像

    #### 作用
    - 提供了一些最常用的NPM包镜像地址
    - 快速的切换安装包时候的服务器地址
    
    #### 常用命令
    - `npm install -g nrm` 安装
    - `nrm ls` 查看当前所有可用的镜像源地址以及当前所使用的镜像源地址
    - `nrm use npm`|`nrm use taobao` 切换不同的镜像源

### package

- [学习地址](https://javascript.ruanyifeng.com/nodejs/packagejson.html)
- name - 包名。
- version - 包的版本号。
- description - 包的描述。
- homepage - 包的官网 url 。
- author - 包的作者姓名。
- contributors - 包的其他贡献者姓名。
- dependencies - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下。
- repository - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上。
- main - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。
- keywords - 关键字

### 版本号
- 语义版本号分为X.Y.Z三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。
    - 如果只是修复bug，需要更新Z位。
    - 如果是新增了功能，但是向下兼容，需要更新Y位。
    - 如果有大变动，向下不兼容，需要更新X位。

### npm5新版功能特性解析
- [学习文档](https://cloud.tencent.com/developer/article/1020507)
- 默认新增了类似yarn.lock的 package-lock.json
- git 依赖支持优化：这个特性在需要安装大量内部项目（例如在没有自建源的内网开发），或需要使用某些依赖的未发布版本时很有用。在这之前可能需要使用指定 commit_id 的方式来控制版本。
- 文件依赖优化：在之前的版本，如果将本地目录作为依赖来安装，将会把文件目录作为副本拷贝到 node_modules 中。而在 npm5 中，将改为使用创建 symlinks 的方式来实现（使用本地 tarball 包除外），而不再执行文件拷贝。这将会提升安装速度。目前yarn还不支持。

    #### package-lock.json
    - [学习文档](https://blog.cuiyongjian.com/engineering/lock-in-npm/)
    
    #### npx
    
    - npx 是一个非常强大的命令，从 npm 的 5.2 版本（发布于 2017 年 7 月）开始可用。
    - 如果不想安装 npm，则可以安装 npx 为独立的软件包。
    - npx 可以运行使用 Node.js 构建并通过 npm 仓库发布的代码。
    
        ##### 运行原理
        - 去node_modules/.bin路径检查npx后的命令是否存在，找到之后执行；
        - 找不到，就去环境变量$PATH里面，检查npx后的命令是否存在，找到之后执行;
        - 还是找不到，自动下载一个临时的依赖包最新版本在一个临时目录，然后再运行命令，运行完之后删除，不污染全局环境。
        
        ##### npx commandname
        - 会自动地在项目的 node_modules 文件夹中找到命令的正确引用，而无需知道确切的路径，也不需要在全局和用户路径中安装软件包
        
        ##### 无需安装的命令执行
        - 不需要安装任何东西。
        - 可以使用 @version 语法运行同一命令的不同版本
        ```
        <!--npm情况下执行-->
        npm install cowsay
        cowsay "你好"
        
        <!--npx情况下执行-->
        npx cowsay "你好"
        ```
        
        ##### 使用不同的 Node.js 版本运行代码
        - npx node@10 -v #v10.18.1
        - npx node@12 -v #v12.14.1
        
        ##### 直接从 URL 运行任意代码片段
        - npx 并不限制使用 npm 仓库上发布的软件包。
        - 可以运行位于 GitHub gist 中的代码 `npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32`

## yarn


## NPM参考资料
- [Javascript 标准参考教程 - package](https://javascript.ruanyifeng.com/nodejs/packagejson.html#toc1)
- [Javascript 标准参考教程 - npm](https://javascript.ruanyifeng.com/nodejs/npm.html)

## YARN参考资料

## 常见问题
- [Yarn vs npm](https://zhuanlan.zhihu.com/p/23493436)