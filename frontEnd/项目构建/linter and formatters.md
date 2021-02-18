# Linters and Formatter
* [ESLint](#ESlint)
    - [快速开始](#快速开始)
    - [基础](#基础)
        - [配置](#配置)
        - [命令行](#命令行)
        - [规则](#规则)
    - [进阶](#进阶)
    - [参考链接](#参考链接)
* [Prettier](#Prettier)
    - [介绍](#介绍)
    - [安装](#安装)
    - [使用](#使用)
    - [配置](#配置)
    - [参考链接](#参考链接)

## ESlint

### [快速开始](http://eslint.cn/docs/user-guide/getting-started)

1. 安装
```bash
npm install eslint --save-dev
```

2. 设置配置文件
```bash
./node_modules/.bin/eslint --init
```

3. 运行
```bash
./node_modules/.bin/eslint yourfile.js
```

### 基础

#### [配置](http://eslint.cn/docs/user-guide/configuring)

1. 有两种主要的方式来配置 ESLint：

- Configuration Comments - 使用 JavaScript 注释把配置信息直接嵌入到一个代码源文件中。

- Configuration Files - 使用 JavaScript、JSON 或者 YAML 文件为整个目录（处理你的主目录）和它的子目录指定配置信息。可以配置一个独立的 .eslintrc.* 文件，或者直接在 package.json 文件里的 eslintConfig 字段指定配置，ESLint 会查找和自动读取它们，再者，你可以在命令行运行时指定一个任意的配置文件。

2. 有很多信息可以配置：

- Environments: 指定脚本的运行环境。每种环境都有一组特定的预定义全局变量。
- Globals: 脚本在执行期间访问的额外的全局变量。
- Rules: 启用的规则及其各自的错误级别。

##### 配置Rules

规则有以下三种值：
- "off" or 0 - 关闭规则
- "warn" or 1 - 将规则视为一个警告（不会影响退出码）
- "error" or 2 - 将规则视为一个错误 (退出码为1) 

这三个错误级别可以允许你细粒度的控制 ESLint 是如何应用规则
```
// .eslintrc
{
    "rules": {
        "semi": ["error", "always"],
        "quotes": ["error", "double"]
    }
}
```

##### 配置plugins

```
{
    "plugins": [
        "plugin1",
        "eslint-plugin-plugin2"
    ]
}

// 在 YAML 中：
---
  plugins:
    - plugin1
    - eslint-plugin-plugin2
```

#### 指定解析器

ESLint 默认使用Espree作为其解析器，你可以在配置文件中指定一个不同的解析器，只要该解析器符合下列要求：

- 它必须是一个 Node 模块，可以从它出现的配置文件中加载。通常，这意味着应该使用 npm 单独安装解析器包。
- 它必须符合 parser interface。

```
{
    "parser": "esprima",
}
```

#### 指定解析器选项
- ecmaVersion：默认设置为 3，5（默认）， 你可以使用 6、7、8、9 或 10 来指定你想要使用的 ECMAScript 版本。你也可以用使用年份命名的版本号指定为 2015（同 6），2016（同 7），或 2017（同 8）或 2018（同 9）或 2019 (same as 10)
- sourceType：设置为 "script" (默认) 或 "module"（如果你的代码是 ECMAScript 模块)。
- ecmaFeatures：这是个对象，表示你想使用的额外的语言特性:
    - globalReturn：允许在全局作用域下使用 return 语句
    - impliedStrict：启用全局 strict mode (如果 ecmaVersion 是 5 或更高)
    - jsx：启用 JSX
    - experimentalObjectRestSpread：启用实验性的 object rest/spread properties 支持。(重要：这是一个实验性的功能,在未来可能会有明显改变。 建议你写的规则 不要 依赖该功能，除非当它发生改变时你愿意承担维护成本。)

##### 指定处理器

```
{
    "plugins": ["a-plugin"],
    "processor": "a-plugin/a-processor"
}
```

要为特定类型的文件指定处理器，请使用 overrides 键和 processor 键的组合。例如，下面对 *.md 文件使用处理器 a-plugin/markdown。

```
{
    "plugins": ["a-plugin"],
    "overrides": [
        {
            "files": ["*.md"],
            "processor": "a-plugin/markdown"
        }
    ]
}
```

##### 指定Global

一个环境定义了一组预定义的全局变量。这些环境并不是互斥的，所以你可以同时定义多个。

要在配置文件里指定环境，使用 env 关键字指定你想启用的环境，并设置它们为 true。例如，以下示例启用了 browser 和 Node.js 的环境：
```
{
    "env": {
        "browser": true,
        "node": true
    }
}
```

#### [命令行](http://eslint.cn/docs/user-guide/command-line-interface)

命令行工具有几个选项，你可以通过运行 eslint -h 查看所有选项。

这些选项可以通过重复该选项或使用逗号分隔的列表进行指定（除了 --ignore-pattern 不允许第二种风格）。

```
eslint --ext .jsx --ext .js lib/

eslint --ext .jsx,.js lib/
```

##### 基础配置

1. --no-eslintrc：禁用 .eslintrc.* 和 package.json 文件中的配置。
2. -c, --config：该选项允许你为 ESLint (查看 Configuring ESLint 了解更多)指定一个额外的配置文件。
3. --env：指定环境。关于每种环境中定义的全局变量的详细信息请查看 configuration 文档。该选项只能启用环境，不能禁用在其它配置文件中设置的环境。要指定多个环境的话，使用逗号分隔它们，或多次使用这个选项。
4. --global：定义了全局变量，这样它们就不会被 no-undef 规则标记为未定义了。任何指定的全局变量默认是只读的，在变量名字后加上 :true 后会使它变为可写。要指定多个变量，使用逗号分隔它们，或多次使用这个选项。
5. --parser： 指定一个解析器。默认情况下，使用 espree。
6. --parser-options: 你指定 ESLint 要使用的解析器选项。注意，可用的解析器选项取决于你所选用的解析器。
7. --resolve-plugins-relative-to: 更改插件解析所在的文件夹。

##### 指定Rules和Plugins

1. --rulesdir：指定另一个加载规则文件的目录。这允许你在运行时动态加载新规则。当你有自定义规则，而且这些规则不适合绑定到 ESLint 时，这会很有用。
2. --plugin：这个选项指定一个要加载的插件
3. --rule：指定要使用的规则。这些规则将会与配制文件中指定的规则合并

##### 修复

1. --fix：指示 ESLint 试图修复尽可能多的问题
2. --fix-dry-run：与 --fix 有相同的效果，唯一一点不同是，修复不会保存到文件系统中
3. --fix-type：允许你在使用 --fix 或 --fix-dry-run 时指定要应用的修复的类型。修复的三种类型是:
    - problem - 修复代码中的潜在错误
    - suggestion - 对改进它的代码应用修复
    - layout - 应用不改变程序结构 (AST) 的修复

##### 忽略

1. --ignore-path：指定一个文件作为 .eslintignore。默认情况下，ESLint 在当前工作目录下查找 .eslintignore。你可以通过提供另一个文件的路径改变这种行为。
2. --no-ignore：禁止排除 .eslintignore、--ignore-path 和 --ignore-pattern 文件中指定的文件。
3. --ignore-pattern：指定要忽略的文件模式(除了 .eslintignore 中的模式之外)。你可以重复该选项以提供多个模式

#### [规则](http://eslint.cn/docs/rules/)

### 进阶

#### [创建规则](http://eslint.cn/docs/developer-guide/working-with-rules)

#### [自定义格式化器](http://eslint.cn/docs/developer-guide/working-with-custom-formatters)

#### [自定义解析器](http://eslint.cn/docs/developer-guide/working-with-custom-parsers)

#### [开发插件](http://eslint.cn/docs/developer-guide/working-with-plugins)

#### [可共享的配置](http://eslint.cn/docs/developer-guide/shareable-configs)

### 参考链接
1. http://eslint.cn/
2. https://zhuanlan.zhihu.com/p/75531199
3. https://segmentfault.com/a/1190000014684778

## Prettier

### 介绍

Prettier是一个代码格式化工具，它能够解析代码，使用你自己设定的规则来重新打印出格式规范的代码。

它具有以下几个有优点：
- 可配置化
- 支持多种语言
- 集成多数的编辑器
- 简洁的配置项

### 基础

#### [安装](https://prettier.bootcss.com/docs/install.html)

1. npm
```bash
npm install --save-dev --save-exact prettier
```

2. yarn
```bash
yarn add --dev --exact prettier
```

#### 使用

##### [命令行](https://prettier.bootcss.com/docs/cli.html)

1. 格式化所有文件
- npm
```bash
npx prettier --write .
```

- yarn
```bash
yarn prettier --write .
```

2. 格式化某个目录
```bash
yarn prettier --write app/
```

3. 格式化某个文件
```bash
yarn prettier --write app/components/Button.js
```

4. 格式化某个目录下的某个类型的所有文件
```bash
yarn prettier --write "app/**/*.js"
```

##### [Pre-commit Hooks](https://prettier.bootcss.com/docs/precommit.html)

1. lint-staged
```bash
npx mrm lint-staged
```

```json
// 并将此配置添加到您的package.json：
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "**/*": "prettier --write --ignore-unknown"
  }
}
```

2. pretty-quick
```bash
npm install --save-dev pretty-quick husky
```

```json
// 并将此配置添加到您的package.json：

{
  "husky": {
    "hooks": {
      "pre-commit": "pretty-quick --staged"
    }
  }
}
```

3. pre-commit
```yaml
// .pre-commit-config.yaml文件：

- repo: https://github.com/pre-commit/mirrors-prettier
  rev: "" # Use the sha or tag you want to point at
  hooks:
    - id: prettier
```

4. git-format-staged
```bash
npm install --save-dev git-format-staged husky
```

```json
// 并将此配置添加到您的package.json：

{
  "husky": {
    "hooks": {
      "pre-commit": "git-format-staged -f 'prettier --ignore-unknown --stdin --stdin-filepath \"{}\"' ."
    }
  }
}
```

5. Shell script

##### 工作方式

- Input
```
foo(reallyLongArg(), omgSoManyParameters(), IShouldRefactorThis(), isThereSeriouslyAnotherOne());
```

- Output
```
foo(
  reallyLongArg(),
  omgSoManyParameters(),
  IShouldRefactorThis(),
  isThereSeriouslyAnotherOne()
);
```

##### 与ESLint结合使用

很多项目都会使用ESLint来提高代码的质量，有两种方式能够集成Prettier和ESLint，你也可以单独或同时使用它们。

1. 使用ESLint运行Prettier

如果你已经在你的项目中使用ESLint并且想要只通过单独一条命令来执行你的所有的代码检查的话，你可以使用ESLint来为你运行Prettier。

只需要使用eslint-plugin-prettier来添加Prettier作为ESLint的规则配置。

```bash
yarn add --dev prettier eslint-plugin-prettier
```

```json
// .eslintrc.json
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

2. 关闭ESLint的格式规则

你是否通过ESLint来运行Prettier，又或者是单独运行两个工具，那你大概只想要每个格式问题只出现一次，而且你特别不想要ESLint仅仅是和Prettier有简单的不同和偏好而报出“问题”。

所以你大概想要禁用冲突的规则（当保留其他Prettier不关心的规则时）最简单的方式是使用eslint-config-prettier。它可以添加到任何ESLint配置上面。

```bash
yarn add --dev eslint-config-prettier
```

```json
// .eslintrc.json
{
  "extends": ["prettier"]
}
```

##### [API](https://prettier.bootcss.com/docs/api.html)

1. prettier.format(source [, options])
2. prettier.check(source [, options])
3. prettier.formatWithCursor(source [, options])
4. prettier.resolveConfig(filePath [, options])
5. prettier.resolveConfigFile([filePath])
6. prettier.clearConfigCache()
7. prettier.getFileInfo(filePath [, options])
8. prettier.getSupportInfo()

#### 配置

##### [选项](https://prettier.bootcss.com/docs/options.html)

- Print Width
- Tab Width
- Tabs
- Semicolons
- Quotes
- Quote Props
- JSX Quotes
- Trailing Commas
- Bracket Spacing
- JSX Brackets
- Arrow Function Parentheses
- Range
- Parser
- File Path
- Require Pragma
- Insert Pragma
- Prose Wrap
- HTML Whitespace Sensitivity
- Vue files script and style tags indentation
- End of Line
- Embedded Language Formatting

##### [配置方式](https://prettier.bootcss.com/docs/configuration.html)

一共有三种方式支持对Prettier进行配置：

1. 根目录创建 .prettierrc 文件，能够写入YML、JSON的配置格式，并且支持.yaml、.yml、.json、.js后缀；
2. 根目录创建 .prettier.config.js 文件，并对外export一个对象；
3. 在 package.json 中新建prettier属性。

此外，添加.prettierignore文件，让您的编辑器知道哪些文件不需触摸，以及能够运行prettier --write .以格式化整个项目（无需处理不需要的文件，也不会阻塞生成的文件）。

### 参考链接
1. https://prettier.bootcss.com/
