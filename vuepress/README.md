# Vue Press

用于将 `md` 文档生成网站，尤其适合写 `vue` 技术文档，因为它支持在文档中写 `vue` 代码，可以在线运行。

## 简介

在 `docs` 文件夹中写文档，在 `docs/.vuepress` 中配置项目。其中包括项目配置，组件，主题，样式，增强等。

## 初始化

* 创建目录
* npm init
* git init
* npm i vuepress -D
* 创建docs文件夹，创建README.md
* 添加启动命令，package.json 添加

    ```json
    {
      "scripts": {
        "docs:dev": "vuepress dev docs",
        "docs:build": "vuepress build docs"
      }
    }
    ```

* 页面配置，`docs` 下添加 `.vuepress` 文件夹，并添加 `config.js`

    ```js
    module.exports = {
      title: 'Hello VuePress',
      description: 'Just playing around'
    }
    ```

## 基础配置

### 导入组件

`docs/.vuepress` 下添加 `components` 文件夹，在该文件夹下添加自定义组件。

### 相关依赖

手动安装需要使用的第三方依赖，并配置。

配置 `Element UI`：npm安装依赖后，`docs/.vuepress` 下添加 `enhanceApp.js`。

```js
import Element from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

export default ({ Vue, options, router }) => {
  Vue.use(Element);
};
```

安装 `sass-loader`，注意版本问题。
安装 `node-sass` 或 `sass` （推荐）。

### DemoContainer 插件

为了实现与 `Element UI` 文档交互效果，即包含可运行案例，又包含源代码。
又想只写一遍代码，借助 `vuepress-plugin-demo-container` 插件。

安装步骤：

* 安装依赖，`npm i vuepress-plugin-demo-container -D`
* 配置，修改 `docs/.vuepress/config.js`，添加

    ```js
    {
      ...
      plugins: ['demo-container'], // 引入插件
      locales: { // 设置默认语言为中文
        '/': {
          lang: 'zh-CN',
        },
      },
      ...
    }
    ```

* 使用, **代码前要添加 :::demo**

  ```md
  ::: demo 这是一些说明，不能换行
  ```html
  这里是你的代码
  \``` <== 因为md文档语法问题，此处添加 `\`，注意删除
  :::
  ```

* 引入自定义组件.虽然在 `docs/.vuepress/components` 中导入的组件可以直接在 `md` 文档中使用，但是不能在`DemoContainer` 中使用。修改 `docs/.vuepress/enhanceApp.js`，全局注册自定义组件。可以参考下列代码。

    ```js
    const requireComponents = require.context('./components', true, /\.vue$/);
    requireComponents.keys().forEach((key) => {
      const component = requireComponents(key).default;
      Vue.component(component.name, component);
    });
    ```

### 样式

案例的样式会被 `md` 文档自带样式影响，比如 `<table>` 自带上下边距，`<th>, <td>` 自带边框。

目前没有什么好方法，只能重置 `.demo-content` 下的所有默认样式

在 `docs/.vuepress` 创建 `styles` 文件夹，并在内部创建 `index.styl` 文件，重置样式。内部使用 `stylus` 语法。

```stylus
.demo-content 
  table 
    margin: 0;
  th, td
    border: none;
```

### 代码规范

自定义组件与案例代码都需要遵循代码规范。

* 添加 `.eslint.js`，配置相关代码规范
* 安装相关依赖。注意版本问题，不知不觉eslint已经到 `8.0` 了，我还在用 `6.7`
* `.`开头的文件和文件夹会被 `eslint` 忽略，添加 `.eslintignore` 配置 `!.vuepress`。
* md中代码无法应用格式化工具
  * 创建 `examples` 文件夹，创建案例文件，格式化完再拷贝出来。比较繁琐。
  * 放弃 `DEMO Container` 插件，创建 `examples` 文件夹，创建案例文件，在 `enhanceApp.js` 中全局引入组件。直接调用组件可以产生运行结果。在 `md` 中通过 `<<< @/examples/filepath` 引用代码。
  * 开发新插件。

## 导航和功能配置

<!-- TODO -->
