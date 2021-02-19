# Antd

* [基础概念](#基础概念)
    - [官方文档](#官方文档)
    - [安装](#安装)
* [进阶](#进阶)
    - [主题配置](#主题配置)
    - [自定义主题](#自定义主题)
    - [国际化](#国际化)

## 基础概念

### 官方文档
1. [文档](#https://ant.design/docs/react/introduce-cn)
2. [组件](#https://ant.design/components/overview-cn/)

### 安装
1. npm
    - `npm install antd --save`
2. yarn
    - `yarn add antd`

### 使用antd组件
1. 引入组件
    ```ts
    // App.js
    import { Button } from 'antd';
    import './App.css';

    function App() {
        return (
            <div className="App">
                <Button type="primary">antd-btn</Button>
            </div>
        );
    }

    export default App;

    ```
2. 引入组件样式
    ```ts
    // App.css
    @import '~antd/dist/antd.css';
    ```

## 进阶

### 主题配置
> 我们需要对 create-react-app 的默认配置进行自定义，这里我们使用 craco （一个对 create-react-app 进行自定义配置的社区解决方案）

1. 安装
    - `yarn add @craco/craco`
2. 修改`package.json`中的`scripts`属性
    ```ts
    /* package.json */
    "scripts": {
    -   "start": "react-scripts start",
    -   "build": "react-scripts build",
    -   "test": "react-scripts test",
    +   "start": "craco start",
    +   "build": "craco build",
    +   "test": "craco test",
    }
    ```
3. 在项目根目录创建一个`craco.config.js`用于修改默认配置
    ```ts
    /* craco.config.js */
    module.exports = {
        // ...
    };
    ```

### 自定义主题

1. 把`src/App.css`文件修改为`src/App.less`，然后修改样式引用为less文件。
    ```ts
    /* src/App.js */
    import './App.css';
    import './App.less';
    ```
    ```ts
    /* src/App.less */
    @import '~antd/dist/antd.css';
    @import '~antd/dist/antd.less';
    ```
2. 安装`craco-less`
    ```ts
    yarn add craco-less
    ```
3. 修改`craco.config.js`文件
    ```ts
    const CracoLessPlugin = require('craco-less');

    module.exports = {
        plugins: [
            {
                plugin: CracoLessPlugin,
                options: {
                    lessLoaderOptions: {
                        lessOptions: {
                            modifyVars: { '@primary-color': '#1DA57A' },
                            javascriptEnabled: true,
                        },
                    },
                },
            },
        ],
    };
    ```
4. 重启项目
    - 可以观察到button的背景色已经发生了改变

### 国际化
1. 使用`ConfigProvider`组件
    > antd 提供了一个 React 组件 ConfigProvider 用于全局配置国际化文案
    ```ts
    import zhCN from 'antd/lib/locale/zh_CN';

    return (
        <ConfigProvider locale={zhCN}>
            <App />
        </ConfigProvider>
    );
    ```
2. [目前antd支持的语言](#https://ant.design/docs/react/i18n-cn)



