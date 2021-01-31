# 包管理
* [NPM](#npm)
    - [npm简介](#npm简介) 
    - [package.json](#package)
    - [npm5新版功能特性解析](#npm5新版功能特性解析)
    - [NPX](#npx)
* [YARN](#yarn)
    - [yarn简介](#yarn简介)
    - [配置](#配置)

## npm
### npm简介
- npm有两层含义。一层含义是Node的开放式模块登记和管理系统，网址为 npmjs.org。另一层含义是Node默认的模块管理器，是一个命令行下的软件，用来安装和管理Node模块。
- npm不需要单独安装。在安装Node的时候，会连带一起安装npm。但是，Node附带的npm可能不是最新版本，最好用`npm install npm@latest -g`，更新到最新版本。

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

### yarn简介
- Yarn 是 Facebook, Google, Exponent 和 Tilde 开发的一款新的 JavaScript 包管理工具。它的目的是解决使用 npm 面临的少数问题：

1. 安装的时候无法保证速度/一致性
2. 安全问题，因为 npm 安装时允许运行代码
3. Yarn 同样是一个从 npm 注册源获取模块的新的 CLI 客户端。注册的方式不会有任何变化 —— 你同样可以正常获取与发布包。

### 两种安装方式
1. 官网推荐brew install yarn
2. npm全局安装 npm install -g yarn

### 常用命令
- [使用方法](https://yarn.bootcss.com/docs/usage/)
1. 开始一个新工程
    - yarn init 与 npm init 一样通过交互式会话创建一个 package.json
2. 添加一个依赖
    - 通过 yarn add 添加依赖会更新 package.json 以及 yarn.lock 文件
    - 开发环境
        - yarn add <packageName> 依赖会记录在 package.json 的 dependencies 下 开发环境
    - 生产环境
        - yarn add <packageName> --dev 依赖会记录在 package.json 的 devDependencies 下 生产环境  
    - 全局
        - yarn global add <packageName> 全局安装依赖
3. 更新一个依赖
    - yarn upgrade 用于更新包到基于规范范围的最新版本
4. 移除一个依赖
    - yarn remove <packageName>
5. 安装 package.json 中的所有文件
    - yarn 或者 yarn install
6. 运行脚本   
    - yarn run dev # yarn 执行 dev 对应的脚本 node app.js
7. 显示某个包信息
    - yarn info <packageName> 可以用来查看某个模块的最新版本信息
8. 列出项目的所有依赖
    - yarn list
9. 管理 yarn 配置文件
    - yarn coinfig
    - yarn config set key value # 设置
    - yarn config get key # 读取值
10. 缓存
    - yarn cache
    - sudo yarn cache list # 列出已缓存的每个包
    - sudo yarn cache dir # 返回 全局缓存位置
    - sudo yarn cache clean # 清除缓存

### 配置
#### package.json
- package.json最重要的两个领域是 name和version，没有他们，你的包将无法安装。name和version字段用于共同创造一个独特的ID
```javascript
{
  "name": "my-awesome-package"
}
// 软件包的名称。它在URL中使用，在命令行中作为参数使用，并作为内部的目录名称使用node_modules

1. yarn add [name]
2. node_modules/[name]
3. https://registry.npmjs.org/[name]/-/[name]-[version].tgz
```
- 规则
    - 必须小于或等于214个字符（包括@scope/范围包）。
    - 不得以点（.）或下划线（_）开头。
    - 名称中不能有大写字母。
    - 纠错
    - 必须只使用网址安全的字符。
    
- 注意
    - 不要使用与核心Node.js模块相同的名称
    - 不要把js或node在名称中。
    - 保持名称简短和描述性。你希望人们从名字上了解它是什么，但它也会用在require()通话中。
    - 确保在注册表中没有具有相同名称的东西。

#### envvars
- 定义的环境变量`process.env`允许您配置额外的Yarn特征
    - process.env.CHILD_CONCURRENCY=#number#
- 控制并行运行的子进程的数量以构建节点模块
- 将此数字设置为1将导致节点模块按顺序构建，这可以避免使用node-gyp的窗口上的链接器错误。

#### .yarnrc
- 项目根目录下添加文件.yarnrc

#### yarn.lock
- 为了获得跨机器的一致安装，Yarn需要比您在配置中的依赖更多的信息package.json。Yarn需要存储每个依赖项的安装版本。为此，Yarn 在项目的根目录中使用一个文件yarn.lock
- 由yarn管理
    - 您的yarn.lock文件是自动生成的，应完全由Yarn处理。当您使用Yarn CLI添加/升级​​/删除依赖项时，它将自动更新您的yarn.lock文件。不要直接编辑这个文件，因为很容易破坏某些东西
- 仅限当前包
    - 在安装期间，Yarn只会使用顶层yarn.lock文件，并会忽略yarn.lock依赖项中存在的任何文件。顶层yarn.lock文件包含Yarn需要锁定整个依赖关系树中软件包版本的所有内容。
- 检查到源代码管理
    - 所有yarn.lock文件都应该检入源代码控制（例如git或mercurial）。这允许Yarn在所有机器上安装相同的精确依赖关系树，无论它是您的同事的笔记本电脑还是CI服务器。
    - 框架和库作者也应该检查yarn.lock源代码管理。不要担心发布yarn.lock文件，因为它不会影响库的用户


###

## NPM参考资料
- [Javascript 标准参考教程 - package](https://javascript.ruanyifeng.com/nodejs/packagejson.html#toc1)
- [Javascript 标准参考教程 - npm](https://javascript.ruanyifeng.com/nodejs/npm.html)

## YARN参考资料
- [yarn出现的背景](https://cloud.tencent.com/developer/article/1694777)
- [yarn-中文](https://cloud.tencent.com/developer/doc/1253)
- [yarnpkg-英文](https://yarnpkg.com/cli/install)

## 常见问题
- [Yarn vs npm](https://zhuanlan.zhihu.com/p/23493436)
- [命令对比](https://yarn.bootcss.com/docs/migrating-from-npm/#toc-cli-commands-comparison)