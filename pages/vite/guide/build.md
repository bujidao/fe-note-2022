# 构建生产版本

当需要将应用部署到生产环境时，只需运行 vite build 命令。默认情况下，它使用 <root>/index.html 作为其构建入口点，并生成能够静态部署的应用程序包。请查阅 部署静态站点 获取常见服务的部署指引。

## 浏览器兼容性

用于生产环境的构建包会假设目标浏览器支持现代 JavaScript 语法。默认情况下，Vite 的目标浏览器是指能够 支持原生 ESM script 标签 和 支持原生 ESM 动态导入 的。作为参考，Vite 使用这个 browserslist 作为查询标准：

```
defaults and supports es6-module and supports es6-module-dynamic-import, not opera > 0, not samsung > 0, not and_qq > 0
```

你也可以通过 build.target 配置项 指定构建目标，最低支持 es2015。

请注意，默认情况下 Vite 只处理语法转译，且 默认不包含任何 polyfill。你可以前往 Polyfill.io 查看，这是一个基于用户浏览器 User-Agent 字符串自动生成 polyfill 包的服务。

传统浏览器可以通过插件 @vitejs/plugin-legacy 来支持，它将自动生成传统版本的 chunk 及与其相对应 ES 语言特性方面的 polyfill。兼容版的 chunk 只会在不支持原生 ESM 的浏览器中进行按需加载。

## 公共基础路径

如果你需要在嵌套的公共路径下部署项目，只需指定 base 配置项，然后所有资源的路径都将据此配置重写。这个选项也可以通过命令行参数指定，例如 vite build --base=/my/public/path/。

由 JS 引入的资源 URL，CSS 中的 url() 引用以及 .html 文件中引用的资源在构建过程中都会自动调整，以适配此选项。

当然，情况也有例外，当访问过程中需要使用动态连接的 url 时，可以使用全局注入的 import.meta.env.BASE_URL 变量，它的值为公共基础路径。注意，这个变量在构建时会被静态替换，因此，它必须按 import.meta.env.BASE_URL 的原样出现（例如 import.meta.env['BASE_URL'] 是无效的）

## 多页面应用模式

假设你有下面这样的项目文件结构

``` bash
├── package.json
├── vite.config.js
├── index.html
├── main.js
└── nested
    ├── index.html
    └── nested.js
```

在开发过程中，简单地导航或链接到 /nested/ - 将会按预期工作，与正常的静态文件服务器表现一致。

在构建过程中，你只需指定多个 .html 文件作为入口点即可：

``` js
// vite.config.js
const { resolve } = require('path')
const { defineConfig } = require('vite')

module.exports = defineConfig({
  build: {
    rollupOptions: {
      input: {
        main: resolve(__dirname, 'index.html'),
        nested: resolve(__dirname, 'nested/index.html')
      }
    }
  }
})
```

如果你指定了另一个根目录，请记住，在解析输入路径时，__dirname 的值将仍然是 vite.config.js 文件所在的目录。因此，你需要把对应入口文件的 root 的路径添加到 resolve 的参数中。