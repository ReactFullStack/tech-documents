# 包管理
* [NPM](#npm)
    - [npm简介](#npm简介) 
    - [package.json](#package)
    - [npm5新版功能特性解析](#npm5新版功能特性解析)
    - 
* [NPX](#npx)
* [YARN](#yarn)

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

### yarn简介
Yarn 是 Facebook, Google, Exponent 和 Tilde 开发的一款新的 JavaScript 包管理工具。它的目的是解决使用 npm 面临的少数问题：

1. 安装的时候无法保证速度/一致性
2. 安全问题，因为 npm 安装时允许运行代码
3. Yarn 同样是一个从 npm 注册源获取模块的新的 CLI 客户端。注册的方式不会有任何变化 —— 你同样可以正常获取与发布包。

### 两种安装方式
1. 官网推荐brew install yarn
2. npm全局安装 npm install -g yarn

### 常用命令
1. 开始一个新工程

    - yarn init 与 npm init 一样通过交互式会话创建一个 package.json
    - yarn init # yarn
    - npm init # npm

2. 添加一个依赖

    - 通过 yarn add 添加依赖会更新 package.json 以及 yarn.lock 文件
    
    - 开发环境
    
        - yarn add <packageName> 依赖会记录在 package.json 的 dependencies 下 开发环境
        - yarn add webpack@2.3.3 # yarn --save 是 yarn 默认的，默认记录在 package.json 中
        - npm install webpack@2.3.3 --save # npm
    
    - 生产环境
        
        - yarn add <packageName> --dev 依赖会记录在 package.json 的 devDependencies 下 生产环境
        - yarn add webpack --dev # yarn 简写 -D
        - npm install webpack --save-dev # npm
    
    - 全局
    
        - yarn global add <packageName> 全局安装依赖
        - yarn global add webpack # yarn
        - npm install webpack -g # npm

3. 更新一个依赖

    - yarn upgrade 用于更新包到基于规范范围的最新版本
    - yarn upgrade # 升级所有依赖项，不记录在 package.json 中
    - npm update # npm 可以通过 ‘--save|--save-dev’ 指定升级哪类依赖
    - yarn upgrade webpack # 升级指定包
    - npm update webpack --save-dev # npm
    - yarn upgrade --latest # 忽略版本规则，升级到最新版本，并且更新 package.json

4. 移除一个依赖

    - yarn remove <packageName>
    - yarn remove webpack # yarn
    - npm uninstall webpack --save # npm 可以指定 --save | --save-dev

5. 安装 package.json 中的所有文件

    - yarn 或者 yarn install
    - yarn install # 或者 yarn 在 node_modules 目录安装 package.json 中列出的所有依赖
    - npm install # npm
    - yarn install 安装时，如果 node_modules 中有相应的包则不会重新下载 --force 可以强制重新下载安装
    - yarn install --force # 强制下载安装
    - npm install --force # npm

6. 运行脚本
    
    - yarn run dev # yarn 执行 dev 对应的脚本 node app.js
    - npm run # npm
    - yarn start # yarn
    - npm start # npm
    - 与 npm 一样 可以有 yarn start 和 yarn test 两个简写的运行脚本方式

7. 显示某个包信息

    - yarn info <packageName> 可以用来查看某个模块的最新版本信息
    - yarn info webpack # yarn
    - npm info webpack # npm
    - yarn info webpack --json # 输出 json 格式
    - npm info webpack --json # npm
    - yarn info webpack readme # 输出 README 部分
    - npm info webpack readme

8. 列出项目的所有依赖

    - yarn list
    - yarn list # 列出当前项目的依赖
    - npm list # npm
    - yarn list --depth=0 # 限制依赖的深度
    - sudo yarn global list # 列出全局安装的模块
 
9. 管理 yarn 配置文件

    - yarn coinfig
    - yarn config set key value # 设置
    - npm config set key value
    - yarn config get key # 读取值
    - npm config get key
    - yarn config delete key # 删除
    - npm config delete key
    - yarn config list # 显示当前配置
    - npm config list
    - yarn config set registry https://registry.npm.taobao.org # 设置淘宝镜像
    - npm config set registry https://registry.npm.taobao.org # npm

10. 缓存

    - yarn cache
    - sudo yarn cache list # 列出已缓存的每个包
    - sudo yarn cache dir # 返回 全局缓存位置
    - sudo yarn cache clean # 清除缓存

### 混淆知识点
1. yarn global
    - 不像 npm 添加 -g 或 --global 可以进行全局安装，Yarn 使用的是 global 前缀。不过与 npm 类似，项目依赖不推荐全局安装。
    - global 前缀只能用于 yarn add, yarn bin, yarn ls 和 yarn remove，除yarn add外，这些命令都和 npm 等效。

2. yarn install
    - npm install 命令会根据 package.json 安装依赖以及允许你添加新的模块；
    - yarn install 仅会按 yarn.lock 或 package.json 里面的依赖顺序来安装模块。

3. yarn add [–dev]
    - 与 npm install 类似，yarn add 允许你添加与安装模块，就像命令的名称一样，添加依赖意味着也会算定将依赖写入 package.json，类似 npm 的 --save 参数；
    - Yarn 的 --dev参数则是添加开发依赖，类似 npm 的 --save-dev 参数。

4. yarn licenses [ls|generate-disclaimer]
    - npm 没有类似命令来方便编写自己的包。yarn licenses ls 列出所有已安装包的许可协议。
    - yarn licenses generate-disclaimer 生成包含已安装包许可协议的免责声明。某些协议要求使用者必须在项目中包含该协议，这时候该命令将变得非常好用

5. yarn upgrade
    - 该命令会根据符合 package.json 设定的规则而不是 yarn.lock 定义的确切版本来将包更新到最新版本

## NPM参考资料
- [Javascript 标准参考教程 - package](https://javascript.ruanyifeng.com/nodejs/packagejson.html#toc1)
- [Javascript 标准参考教程 - npm](https://javascript.ruanyifeng.com/nodejs/npm.html)

## YARN参考资料
- [yarn出现的背景](https://cloud.tencent.com/developer/article/1694777)
- [命令](https://classic.yarnpkg.com/en/docs/cli/global)
- [yarnpkg](https://yarnpkg.com/cli/install)
## 常见问题
- [Yarn vs npm](https://zhuanlan.zhihu.com/p/23493436)
- [命令对比](https://www.jianshu.com/p/e7209ecc120d)