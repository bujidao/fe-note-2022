# 功能

对非常基础的使用来说，使用 Vite 开发和使用一个静态文件服务器并没有太大区别。然而，Vite 还通过原生 ESM 导入提供了许多主要用于打包场景的增强功能。

## NPM 依赖解析和预构建

原生 ES 导入不支持下面这样的裸模块导入：

``` javascript
import { someMethod } from 'my-dep'
```
上面的代码在浏览器中运行会抛错。Vite 将会将检测到所有被加载的源文件中此类裸模的引入，并执行一下操作：

1. 预构建：它们可以提高页面加载速度，将 Commonjs / UMD 转为 ESM 格式。预构建这一步由 Esbuild 执行，这使得 Vite 的冷启动时间比任何基于 JavaScript 的打包器都快得多。
1. 重写导入为合法的 URL， 例如 `/node_modules/.vite/my-dep.js?v=fsafsd` 以便浏览器能够正常的导入。

**依赖是强缓存的**

Vite 通过 HTTP 头缓存请求得到依赖

## 模块热重载

Vite 提供了一套原生 ESM 的 HMR API。 具有 HMR 功能的框架可以利用该 API 提供即时、准确的更新，而无需重新加载页面或清除应用程序状态。Vite 内置了 HMR 到 Vue 单文件组件（SFC） 和 React Fast Refresh 中。也通过 @prefresh/vite 对 Preact 实现了官方集成。

注意，你不需要手动设置这些 —— 当你通过 create-vite 创建应用程序时，所选模板已经为你预先配置了这些。

## Typescript

Vite 天然支持引入 `.ts` 文件

Vite 仅执行 `.ts` 文件的转译，并**不**执行任何类型检查。

Vite 使用 esbuild 执行 ts 文件的转译工作，速度比 tsc 快约20-30倍，同时 HMR 更新反映到浏览器的时间小于 50ms。

使用 `仅含类型的导入和导出` 形式的语法可以避免潜在的 *仅含类型的导入不被正确打包* 的问题，写法示例如下：

``` javascript
import type { T } from 'only/types'
export type { T }
```

## CSS

导入的 `.css` 文件将会把内容插入到 `style` 标签内，同时也带有 HMR 支持。也能够以字符串的形式检索处理后、作为其模板默认导出的CSS。

### `@import` 内联和变基

Vite 通过 `postcss-import` 预配置支持了 CSS `@import` 内联，Vite 路径别名也准从 CSS `@import` 。换句话说，所有`css url()` 引用，即使导入的文件在不同的目录中，也总是自动变量，以确保正确性。

Sass 和 Less 文件也支持 `@import `别名和 URL 变基

## CSS Modules

任何以 `.module.css` 结尾的css文件，都会被认为是一个`css modules 文件`。导入这样一个文件以后，会返回一个响应的模块对象。

``` css
/* example.module.css */
.red {
  color: red;
}
```

``` js
import classes from './example.module.css'
document.getElementById('foo').style.color = classes.red
```

css modules 的行为可以进行配置。

如果 `css.modules.localsConvention` 设置开启了 `camelCase` 格式变量名转换（例如 `localsConvention: 'camelCaseOnly'`），你还可以使用按名导入。

``` js
// .apply-color -> applyColor
import { applyColor } from './example.module.css'
document.getElementById('foo').className = applyColor
```

## 静态资源处理

导入一个静态资源会返回解析后的url

``` js
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
```