# 开始

## 总览

Vite 主要由两部分组成：

* 一个开发服务器，它基于 `原生ES模块`，提供了丰富的内建功能，如速度快到惊人的 `模块热更新HMR`

* 一套构建指令，它使用Rollup打包代码，并且它是预配置的，用于输出生产环境的高度优化过的静态资源。

## 搭建第一个Vite项目

使用 yarn:
``` bash
yarn create vite
```

还可以通过附件命令选项，直接指定项目名称和想要的模板。例如构建一个：Vite + Vue 的项目：

``` bash
yarn create vite my-vue-app --template vue
```

项目结构如图：

```
│  .gitignore
│  aa.txt
│  index.html
│  package.json
│  README.md
│  tsconfig.json
│  vite.config.ts
│  yarn.lock
│  
├─public
│      favicon.ico
│      
└─src
    │  App.vue
    │  env.d.ts
    │  main.ts
    │  
    ├─assets
    │      logo.png
    │      
    └─components
            HelloWorld.vue
```

## `index.html` 与项目根目录

你可能已经注意到，在一个 vite 项目中，index.html 在项目的最外层，而不是在 public 文件夹内。这是有意而为之的，在开发期间，vite是一个服务器，而 index.html 是这个 vite 项目的入口文件。

Vite 将 index.html 视为源码和模块图的一部分。Vite 解析 `<script type="module" src="...">`， 这个标签指向你的 JavaScript 源码。甚至内联引入 JavaScript `<script type="module">` 和引用 CSS 的 `<link href>` 也能利用 Vite 特有的功能被解析。另外，`index.html` 中的 URL 将被自动转换，因此不再需要 `%PUBLIC_URL%` 占位符了。

Vite 也支持多个 .html 作入口点的 多页面应用模式。

## 指定替代根目录

vite 以当前工作目录作为根目录启动开发服务器。你也可以通过 vite serve some/sub/dir 来指定一个替代的根目录。

## 使用未发布的功能

如果你迫不及待想要体验最新的功能，可以自行克隆 vite 仓库 到本地机器上然后自行将其链接（将需要 pnpm）：

``` javascript
git clone https://github.com/vitejs/vite.git
cd vite
pnpm install
cd packages/vite
pnpm run build
pnpm link --global # you can use your preferred package manager for this step
```

然后，回到你的 Vite 项目并运行 pnpm link --global vite（或者使用你的其他包管理工具来全局链接 vite）。重新启动开发服务器来体验新功能吧！
