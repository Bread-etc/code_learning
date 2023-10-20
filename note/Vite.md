# Vite

## 指引

### 基本介绍

 Vite 是一种新型前端构建工具,能够显著提升前端开发体验.主要由两部分组成: 

- 一个开发服务器，它基于 [原生 ES 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 提供了 [丰富的内建功能](https://cn.vitejs.dev/guide/features.html)，如速度快到惊人的 [模块热更新（HMR）](https://cn.vitejs.dev/guide/features.html#hot-module-replacement)。

- 一套构建指令，它使用 [Rollup](https://rollupjs.org/) 打包你的代码，并且它是预配置的，可输出用于生产环境的高度优化过的静态资源。

市面上主流的构建工具:

- `webpack` (最常用)
- vite
- parcel
- esbuild
- rollup
- grunt
- gulp

#### vite相较于webpack的优势

> [vite官方文档](https://cn.vitejs.dev/guide/)

- const lodash = require('lodash') (common js 规范)
- import Vue from "vue" (es6 module)

webpack需要将全部文件打包后，再输出文件，使得加载时间大大增加。而vite的ESM语法可以实现按需加载，减少了打包时间，打包构建速度相比webpack要快上不少。

![Alt text](./assets/image.png)
![Alt text](./assets/image-1.png)

#### Vite初次构建

使用 NPM:

```bash
$ npm create vite@lateest
```

使用 Yarn:

```bash
$ yarn create vite
```

使用 PNPM:

```bash
$ pnpm create vite
```

> 当然，我们可以使用 npm add vite -D 在当前项目中添加 vite

需要注意的是： `index.html`在项目最外层而不是在`pubilc`文件夹内。这是有意而为之的：在开发期间 Vite 是一个服务器，而 index.html 是该 Vite 项目的入口文件。

### 功能

#### NPM依赖解析和构建

解决的问题：

1. `预构建`它们可以提高页面加载速度，并将 CommonJS / UMD 转换为 `ESM` 格式。预构建这一步由 `esbuild` 执行，这使得 Vite 的冷启动时间比任何基于 JavaScript 的打包器都要快得多。
2. 对路径处理上可以直接使用 .vite/deps，重写成合法的URL
3. 网络多包传输的性能问题，有多少额外的export和import，vite都会尽可能集成生成为一个或几个模块

#### CSS

Vite天生就支持对CSS文件的直接处理，vite在main.js中引用到了index.css，它会创建一个`<style>`标签，并且把index.css插入。

> 最外层元素类名一般为wrapper，最底层的元素命名为footer

module.css用于解决多个css文件命名重复的问题。

##### `@import 内联和变基`

Vite 通过`postcss-import`预配置支持了CSS`@import`内联，Vite 的路径别名也遵从CSS`@import`。换句话说，所有CSS`url()`引用，即使导入的文件在不同的目录中，也总是自动变基，以确保正确性。

Sass 和 Less 文件也支持`@import`别名和 URL 变基（具体请参阅 CSS Pre-processors）。

##### PostCSS

如果项目包含有效的`PostCSS`配置（任何受postcss-load-config支持的格式，例如`postcss.config.js`），它会自动应用于所有已导入的CSS。

**注意：**CSS最小化压缩将在PostCSS之后运行，并会使用`build.cssTarget`选项。

##### CSS Modules

任何以`.module.css`为后缀名的CSS文件都被认为是一个`CSS modules`文件。导入这样的文件会返回一个响应的模块对象：

```css
/* example.module.css */
.red {
  color: red;
}
```

```js
import classes from './example.module.css'
document.getElementById('foo').className = classes.red
```

当然，CSS modules行为可以通过`css.modules`选项进行配置。如果`css.modules.localsConvention`设置开启了camelCase格式变量名转换（例如：`localsConvention: 'camelCaseOnly'`），还可以使用按名导入。

```js
// .apply-color -> applyColor
import { applyColor } from './example.module.css'
doucument.getElementById('foo').className = applyColor
```

##### CSS预处理器

Vite提供对`.scss`,`.sass`,`.less`,`.stylus`文件的内置支持。没有必要为他们安装特定的Vite插件，但是必须安装相应的预处理器依赖:

```bash
# .scss and .sass
npm add -D sass

# .less
npm add -D less

# .styl and .stylus
npm add -D stylus
```

> 同时，这些预处理器也支持模块化，比如:`style.module.less`

##### 禁用CSS注入页面

自动注入CSS内容的行为可以通过`?inline`参数来关闭。在关闭时，被处理过的CSS字符串将会作为该模块的默认导出，但样式并没有被注入到页面中。

```js
import './foo.css'                          // 自动注入页面
import otherStyles from './bar.css?inline'  // 不会注入页面
```

> **注意：**
> 
> 自Vite4起，CSS文件的默认导入和按名导入（例如`import style from './foo.css'`）将弃用。请使用`?inline`参数代替。

### 静态资源处理

除了动态API外，几乎所有资源都视作静态资源。

#### 将资源引入为URL

服务时引入一个静态资源会返回解析后的公共路径：

```js
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
```

行为类似于Webpack的`file-loader`。区别在于导入既可以使用绝对公共路径（基于开发期间的项目根目录），也可以使用相对路径。

- `url()`在CSS中的引用也以同样的方式处理。
- 如果Vite使用了Vue插件，Vue SFC模板中的资源引用都将自动转换为导入。
- 常见的图像、媒体和字体文件类型被自动检测为资源。可以使用`assetsInclude`选项扩展内部列表。
- 引用的资源作为构建资源图的一部分包括在内，将生成散列文件名，并可以由插件进行处理以进行优化。
- 较小的资源体积小于`assetsInlineLimit`选项值则会被内联为base64 data URL。
- Git LFS 占位符会自动排除在内联之外，因为它们不包含它们所表示的文件的内容。要获得内联，请确保在构建之前通过 Git LFS 下载文件内容。
- 默认情况下，TypeScript不会将静态资源导入视为有效的模块。要解决这个问题，需要添加`vite/client`。

#### 显式URL引入

未被包含在内部列表或`assetsInclude`中的资源，可以使用`?url`后缀显式导入为一个URL。这十分有用，例如，要导入`Houdini Paint Worklets`时：

```js
import workletURL from 'extra-scalloped-border/worklet.js?url'
CSS.paintWorklet.addModule(workletURL)
```

#### 将资源引入为字符串

资源可以使用`?raw`后缀声明作为字符串引入。

```js
import shaderString from './shader.gls?raw'
```

#### 导入脚本作为Worker

脚本可以通过`?worker`或`?sharedWorker`后缀导入为web worker。

```js
// 在生产构建中将会分离出 chunk
import Worker from './shader.js?workder'
const worker = new worker()
```

```js
// sharedworker
import SharedWorker from './shader.js?sharedworder'
const sharedWorker = new SharedWorker()
```

```js
// 内联为 base64 字符串
import InlineWorker from './shader.js?worker&inline'
```

#### `public`目录

如果你有下列这些资源：

- 不会被源码引用（例如`robots.txt`）
- 必须保持原有文件名（没有经过bash）
- ...或者你压根不想引入该资源，只是想得到其URL

那么你可以将该资源放在指定的`public`目录中，它应位于你的项目根目录。该目录中的资源在开发时能直接通过`/`根路径访问到，并且打包时会被完整复制到目标目录的根目录下。

目录默认是`<root>/public`，但可以通过`publicDir`选项来配置。

**注意：**

- 引入`public`中的资源永远应该使用根绝对路径 —— 举个例子，`public/icon.png`应该在源码中被引用为`/icon.png`。
- `public`中的资源不应该被 JavaScript 文件引用。

#### new URL(url, import.meta.url)

`import.meta.url`是一个 ESM 的原生功能，会暴露当前模块的 URL。将它与原生的 URL 构造器组合使用，在一个JavaScript模块中，通过相对路径我们就能得到一个被完整解析的静态资源 URL：

```js
const imgUrl = new URL('./img.png', import.meta.url).href
document.getElementById('hero-img').src = imgUrl
```

这在现代浏览器中能够原生使用 - 实际上，Vite 并不需要在开发阶段处理这些代码！

这个模式同样还可以通过字符串模板支持动态 URL：

```js
fuction getImageUrl(name) {
  return new URL(`./dir/${name}.png`, import.meta.url).href
}
```

在生产构建时，Vite才会进行必要的转换保证URL在打包和资源哈希后仍指向正确的地址。然而，这个URL字符串必须是静态的，这样才好分析。否则代码将被原样保留、因而在`build.target`不支持`import.meta.url`时会导致运行时错误。

```js
// Vite 不会转换这个
const imgUrl = new URL(imagePath, import.meta.url).href
```

**注意：**无法在 SSR 中使用

如果你正在以服务端渲染模式使用 Vite 则此模式不支持，因为`import.meta.url`在浏览器和 Node.js 中有不同的语义。服务端的产物也无法预先确定客户端主机 URL。

### 环境变量与模式

> 环境变量： 根据当前代码环境产生值的变化的变量

代码环境：

1. 开发环境
2. 测试环境
3. 预发布环境
4. 灰度环境
5. 生产环境

Vite在一个特殊的`import.meta.env`对象上暴露环境变量。以下是所有情况下都可以使用的内建变量：

- `import.meta.env.MODE`：{string}应用运行的模式
- `import.meta.env.BASE_URL`：{string}部署应用时的基本URL，由配置项`base`决定
- `import.meta.env.PROD`：{boolean}应用是否运行在生产环境
- `import.meta.env.DEV`：{boolean}应用是否运行在开发环境（永远与生产环境相反）
- `import.meta.env.SSR`：{boolean}应用是否运行在server上

#### `.env`文件

Vite使用dotenv从环境目录中的下列文件加载额外的环境变量，dotenv是Vite的第三方库，会将环境变量注入到process中去，可以通过`process.env`来读取环境变量。

```bash
.env              # 所有情况下都会加载
.env.local        # 所有情况下都会加载，但会被git忽略
.env.[mode]       # 只在指定模式下加载
.env.[mode].local # 只在指定模式下加载，但会被git忽略
```

加载的环境变量会通过`import.meta.env`以字符串的形式暴露给`客户端`源码。为了防止意外地将一些源码泄漏到客户端，只有以`VITE_`为前缀的变量才会暴露给经过vite处理的代码：

```js
VITE_SOME_KEY = 123       // 被暴露给客户端
VITE_APP_KEY = Bread_etc  // 被暴露给客户端
DB_PASSWORD = foobar      // 不被暴露
```

> 如果想要在环境变量中使用 $ 符号，必须使用反斜杠进行转义

**如果你想自定义 env 变量的前缀，需要配置`envPrefix`选项**

## 配置

### 配置Vite

#### 配置智能提示

当以命令行方式运行`vite`的时候，Vite会自动解析`项目根目录`下的名为`vite.config.js`的配置文件。

> 支持显式地通过`--config`命令行选项指定一个配置文件（相对于`cwd`路径进行解析）其中 cwd 指 current working directory 是当前工作目录的缩写

```js
//方式一 （使用defineConfig工具函数）
import { defineConfig } from 'vite'
export default defineConfig({
    // ..
})

//方式二
/** @type import("vite").UserConfig */
const viteConfig = {
    // ...
}
```

上述方式用于兼容其他更多情况。

#### 情景配置

如果配置文件需要基于（ dev/serve 或 build ）命令或者不同的模式来决定项，亦或是一个 `SSR` 构建（ `ssrbuild` ），则可以选择出这样一个函数：

```js
export default defineConfig(({ command, mode, ssrBuild }) => {
  if (command === 'serve') {
    return {
      // dev 独有配置
    }
  } else {
    // command === 'build'
    return {
      // build 独有配置
    }
  }
})
```

#### 异步配置

配置一个异步函数，从而导出一个异步函数，可以通过`defineConfig`传递，以便获得更好的智能提示：

```js
export default defineConfig(async ({ command, mode }) => {
    const data = await asyncFunction()
    return {
        // vite 配置
    }
})
```

#### 在配置中使用环境变量

环境变量通常可以从`process.env`获得
注意： Vite默认是不加载`.env`文件的，因为这些文件需要在执行完Vite配置后才能确定加载哪一个。（与Vite生命周期有关）如果的确需要时，可以使用Vite导出的`loadEnv`函数来加载指定的`.env`文件。

```js
import { defineConfig, loadEnv } from 'vite'

export default defineConfig(({ command, mode }) =>{
  // 根据当前工作目录中的mode加载.env文件
  // 设置第三个参数为''来加载所有环境变量，而不管是否含有'VITE_'前缀
  const env = loadEnv(mode, process.cwd(), '')
  return {
    // vite配置
    define: {
      __APP_ENV__: JSON.stringify(env.APP_ENV),
    },
  }
})
```

或者可以采用**策略模式**

```js
import { defineConfig, loadEnv } from "vite";
import viteBaseConfig from "./vite.base.config";
import viteDevConfig from "./vite.dev.config";
import viteProdConfig from "./vite.prod.config";

// 策略模式
const envResolver = {
    "build": () => {
        console.log('生产环境');
        return ({ ...viteBaseConfig, ...viteProdConfig })
    },
    "serve": () => {
        console.log('开发环境');
        return ({ ...viteBaseConfig, ...viteDevConfig })
    }
}

export default defineConfig(({ command, mode }) => {
    // 是build还是serve取决于敲入的命令
    // console.log('process',process.env)
    // cwd 是当前文件目录
    const env = loadEnv(mode, process.cwd(), "")
    console.log('env///',env);
    return envResolver[command]()
})
```

### 共享选项

#### css.modules

```js
interface CSSModulesOptions {
  scopeBehaviour?: 'global' | 'local'
  globalModulePaths?: RegExp[]
  generateScopedName?:
    | string
    | ((name: string, filename: string, css: string) => string)
  hashPrefix?: string
  /**
   * 默认：null
   */
  localsConvention?:
    | 'camelCase'
    | 'camelCaseOnly'
    | 'dashes'
    | 'dashesOnly'
    | null
}
```

- localsConvention: 修改生成的配置对象的key的展示形式（驼峰or中划线）
- scopeBehaviour: 配置当前的模块化行为是模块化还是全局化（开启hash保证相同的类名不被覆盖）
- generateScopedName: 生成的类名的规则，可以被配置成函数
- hashPrefix: 生成文件的hash的前缀

#### preprocessorOptions 预处理器

指定传递给 CSS 预处理器的选项。文件扩展名用作选项的键。每个预处理器支持的选项可以在它们各自的文档中找到：

- sass/scss
- less
- styl/stylus 仅支持define，可以作为对象传递。

所有预处理器选项还支持`additionalData`选项，可以用于为每个样式内容注入额外代码。请注意，如果注入的是实际的样式而不仅仅是变量时，那么这些样式将会在最终的打包产物中重复出现。

示例：(key + value 形式)

```js
export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `$injectedColor: orange;`,
      },
      less: {
        math: 'parens-division',
      },
      styl: {
        define: {
          $specialColor: new stylus.nodes.RGBA(51, 197, 255, 1),
        },
      },
    },
  },
})
```

#### css.devSourcemap

- 类型：boolean
- 默认：false

#### css.postcss

`PostCSS`是一个用于转换CSS的工具，它允许开发者使用JavaScript插件来处理和转换CSS。它可以看作是一个通用的CSS处理器，可以帮助开发者实现各种CSS处理和转换的需求。

使用postcss：

1. 安装依赖
   
   ```bash
   npm add postcss-cli postcss -D
   ```

2. 书写描述文件

需要先安装预设环境（插件包）

```js
// postcss.config.js
const postcssPresetEnv = require("postcss-preset-env")

module.exports = {
  plugin: [postcssPresetEnv(/* pluginOptions */)]
}
```

内联的`PostCSS`配置（格式同`postcss.config.js`），或者一个（默认基于项目根目录的）自定义的PostCSS配置路径。对内联的POSTCSS配置，它期望接收与`postcss.config.js`一致的格式。但对于`plugins`属性有些特别，只接收使用数组格式。搜索是使用`postcss-load-config`完成的，只有被支持的文件名才会被加载。

**注意：**如果提供了该内联配置，Vite将不会搜索其他PostCSS配置源。

#### resolve.alias

当使用文件系统路径的别名时，请始终使用绝对路径。相对路径的别名值会原封不动地被使用，因此无法被正常解析。(也就是说，可以使用commonjs中的path.resolve来获得绝对路径)

```js
export default defineConfig({
  resolve: {
    alias: {
      // 配置别名
      '@': path.resolve(__dirname, './src'),
      'assets': path.resolve(__dirname, './src/assets')
    }
  }
})
```

### 构建选项

#### build.rollupOptions

- 类型：`RollupOptions`

指定机构以底层的Rollup打包配置。这与从Rollup配置文件导出的选项相同，并将与Vite内部Rollup选项合并。

> **注意：**需要查看 Rollup 选项文档来获取更多细节。（比如`assetFileName`等配置）

#### build.outDir

- 类型：string
- 默认：dist

指定输出路径（相对于项目根目录）

#### build.assetsDir

- 类型：string
- 默认：assets

指定生成静态资源的存放路径（相对于`build.outDir`）。在库模式下不能使用

## API

### 插件API

Vite 插件扩展了设计出色的 Rollup 接口，带有一些 Vite 独有的配置项。因此，你只需要编写一个 Vite 插件，就可以同时为开发环境和生产环境工作。

> 推荐优先阅读 `Rollup` 文档

### Vite独有钩子（Hooks）

Vite 插件也可以提供钩子来服务于特定的 Vite 目标。这些钩子会被 Rollup 忽略。

#### `config`

- 类型：`(config: UserConfig, env: { mode: string，command:string }) => UserConfig | null | void`
- 种类：`async, sequential`

在解析 Vite 配置前调用。钩子接收原始用户配置（命令行选项指定的会与配置文件合并）和一个描述配置环境的变量，包含正在使用的`mode`和`command`。它可以返回一个将被深度合并到现有配置中的部分配置对象，或者直接改变配置（如果默认的合并不能达到预期的结果）。

示例：

```js
// 返回部分配置（推荐）
const partialConfigPlugin = () => ({
  name: 'return-partial',
  config: () => ({
    resolve: {
      alias: {
        foo: 'bar',
      },
    },
  }),
})

// 直接改变配置（应仅在合并不起作用时使用）
const mutateConfigPlugin = () => ({
  name: 'mutate-config',
  config(config, { command }) {
    if (command === 'build') {
      config.root = 'foo'
    }
  },
})
```

> **注意:**
> 
> 用户插件在运行这个钩子之前会被解析，因此在`config`钩子中注入其他插件不会有任何效果。

#### `configResolved`

- 类型：`(config: ResolvedConfig) => void | Promise<void>`
- 种类：`async, parallel`

在解析 Vite 配置后调用。使用这个钩子读取和存储最终解析的配置。当插件需要根据运行的命令做一些不同的事情时，它也很有用。

示例：

```js
const examplePlugin = () => {
  let config

  return {
    name: 'read-config',

    configResolved(resolvedConfig) {
      // 存储最终解析的配置
      config = resolvedConfig
    },

    // 在其他钩子中使用存储的配置
    transform(code, id) {
      if (config.command === 'serve') {
        // dev: 由开发服务器调用的插件
      } else {
        // build: 由 Rollup 调用的插件
      }
    },
  }
}
```

> **注意**：
> 
> 在开发环境下，`command`的值为`serve`（在 CLI 中，vite 和 vite dev 是 vite serve 的别名）。

#### `configureServer`

- 类型：`(server: ViteDevServer) => (() => void) | void | Promise<(() => void) | void>`
- 种类：`async, sequential`

此外请看 ViteDevServer

是用于配置开发服务器的钩子。最常见的用例是在内部`connect`应用程序中添加自定义中间件(middlewares):

```js
const myPlugin = () => ({
  name: 'configure-server',
  configureServer(server) {
    server.middlewares.use((req, res, next) => {
      // 自定义请求处理...
    })
  },
})
```

##### 注入后置中间件

`configureServer`钩子将在内部中间件`被安装前`调用，所以自定义的中间件将会默认会比内部中间件早运行。如果你想注入一个在内部中间件之后运行的中间件，你可以从`configureServer`返回一个函数，将会在内部中间件安装后被调用：

```js
const myPlugin = () => ({
  name: 'configure-server',
  configureServer(server) {
    // 返回一个在内部中间件安装后
    // 被调用的后置钩子
    return () => {
      server.middlewares.use((req, res, next) => {
        // 自定义请求处理...
      })
    }
  },
})
```

##### 存储服务器访问

在某些情况下，其他插件钩子可能需要访问开发服务器实例（例如访问`websocket`服务器、文件系统监视程序或模块图）。这个钩子也可以用来存储服务器实例以供其他钩子访问:

```js
const myPlugin = () => {
  let server
  return {
    name: 'configure-server',
    configureServer(_server) {
      server = _server
    },
    transform(code, id) {
      if (server) {
        // 使用 server...
      }
    },
  }
}
```

> **注意:** `configureServer`在运行生产版本时不会被调用，所以其他钩子需要防范它缺失。

#### `configurePreviewServer`

- 类型：`(server: PreviewServerForHook) => (() => void) | void | Promise<(() => void) | void>`
- 种类：`async, sequential`

参见： PreviewServerForHook

与`configureServer`相同，但用于预览服务器。`configurePreviewServer`这个钩子与`configureServer`类似，也是在其他中间件安装前被调用。如果你想要在其他中间件`之后`安装一个插件，你可以从`configurePreviewServer`返回一个函数，它将会在内部中间件被安装之后再调用：

```js
const myPlugin = () => ({
  name: 'configure-preview-server',
  configurePreviewServer(server) {
    // 返回一个钩子，会在其他中间件安装完成后调用
    return () => {
      server.middlewares.use((req, res, next) => {
        // 自定义处理请求 ...
      })
    }
  },
})
```

#### `transformIndexHtml`

- 类型：`IndexHtmlTransformHook | { order?: 'pre' | 'post', handler: IndexHtmlTransformHook }`
- 种类：`async, sequential`

转换`index.html`的专用钩子。钩子接收当前的HTML字符串和转换上下文。上下文在开发期间暴露ViteDevServer实例，在构建期间暴露`Rollup`输出的包。

这个钩子可以是异步的，并且可以返回以下其中之一:

- 经过转换的 HTML 字符串
- 注入到现有 HTML 中的标签描述符对象数组（{ tag, attrs, children }）。每个标签也可以指定它应该被注入到哪里（默认是在 <head> 之前）
- 一个包含 { html, tags } 的对象

默认情况下`order`是`undefined`，这个钩子会在HTML被转换后应用。为了注入一个应该通过 Vite 插件管道的脚本，`order: 'pre'`指将在处理HTML之前应用。`order: 'post'`是在所有未定义的`order`的钩子函数被应用后才应用。

基础示例：

```js
const htmlPlugin = () => {
  return {
    name: 'html-transform',
    transformIndexHtml(html) {
      return html.replace(
        /<title>(.*?)<\/title>/,
        `<title>Title replaced!</title>`,
      )
    },
  }
}
```

完整钩子签名：

```ts
type IndexHtmlTransformHook = (
  html: string,
  ctx: {
    path: string
    filename: string
    server?: ViteDevServer
    bundle?: import('rollup').OutputBundle
    chunk?: import('rollup').OutputChunk
  },
) =>
  | IndexHtmlTransformResult
  | void
  | Promise<IndexHtmlTransformResult | void>

type IndexHtmlTransformResult =
  | string
  | HtmlTagDescriptor[]
  | {
      html: string
      tags: HtmlTagDescriptor[]
    }

interface HtmlTagDescriptor {
  tag: string
  attrs?: Record<string, string>
  children?: string | HtmlTagDescriptor[]
  /**
   * 默认： 'head-prepend'
   */
  injectTo?: 'head' | 'body' | 'head-prepend' | 'body-prepend'
}
```

#### `handleHotUpdate`

- 类型：`(ctx: HmrContext) => Array<ModuleNode> | void | Promise<Array<ModuleNode> | void>`

执行自定义`HMR`更新处理。钩子接收一个带有以下签名的上下文对象：

```ts
interface HmrContext {
  file: string
  timestamp: number
  modules: Array<ModuleNode>
  read: () => string | Promise<string>
  server: ViteDevServer
}
```

`modules`是受更改文件影响的模块数组。它是一个数组，因为单个文件可能映射到多个服务模块（例如 Vue 单文件组件）。

`read`这是一个异步读函数，它返回文件的内容。之所以这样做，是因为在某些系统上，文件更改的回调函数可能会在编辑器完成文件更新之前过快地触发，并`fs.readFile`直接会返回空内容。传入的`read`函数规范了这种行为。

钩子可以选择:

过滤和缩小受影响的模块列表，使 HMR 更准确。

返回一个空数组，并通过向客户端发送自定义事件来执行完整的自定义 HMR 处理:

```js
handleHotUpdate({ server }) {
  server.ws.send({
    type: 'custom',
    event: 'special-update',
    data: {}
  })
  return []
}
```

客户端代码应该使用`HMR API`注册相应的处理器（这应该被相同插件的 transform 钩子注入）：

```js
if (import.meta.hot) {
  import.meta.hot.on('special-update', (data) => {
    // 执行自定义更新
  })
}
```

### 插件顺序

一个 Vite 插件可以额外指定一个`enforce`属性（类似于`webpack加载器`）来调整它的应用顺序。enforce 的值可以是`pre`或`post`。解析后的插件将按照以下顺序排列：

- Alias
- 带有 enforce: 'pre' 的用户插件
- Vite 核心插件
- 没有 enforce 值的用户插件
- Vite 构建用的插件
- 带有 enforce: 'post' 的用户插件
- Vite 后置构建插件（最小化，manifest，报告）

### Javascript API

#### `createServer`

类型签名：

```ts
async function createServer(inlineConfig?: InlineConfig): Promise<ViteDevServer>
```

使用实例：

```js
import { fileURLToPath } from 'url'
import { createServer } from 'vite'

const __dirname = fileURLToPath(new URL('.', import.meta.url))

;(async () => {
  const server = await createServer({
    // 任何合法的用户配置选项，加上 `mode` 和 `configFile`
    configFile: false,
    root: __dirname,
    server: {
      port: 1337,
    },
  })
  await server.listen()

  server.printUrls()
})()
```

> **注意**
> 
> 当在同一个 Node.js 进程中使用 `createServer` 和 `build` 时，两个函数都依赖于 `process.env.NODE_ENV` 才可正常工作，而这个环境变量又依赖于 mode 配置项。为了避免行为冲突，请在使用这两个 API 时为 process.env.NODE_ENV 或者 mode 配置项、字段设置参数值 development，或者你也可以生成另一个子进程，分别运行这两个 API。

#### `InlineConfig`

内联配置

`InlineConfig`接口扩展了`UserConfig`并添加了以下属性：

- `configFile`：指明要使用的配置文件。如果没有设置，Vite尝试从项目根目录自动解析。设置为false可以禁用自动解析功能。
- `envFile`：设置为false时，则禁用`.env`文件。

#### `ResolvedConfig`

该接口和`UserConfig`有完全相同的属性，也包括如下工具方法：

- `config.assetsInclude`： 一个函数，用于检查一个`id`是否被考虑为是一个资源。
- `config.logger`： Vite内部的日志对象。

## 插件

### vite-aliases

vite-aliases可以帮助我们自动生成别名：检测你当前目录下的包括src在内的所有文件夹，并帮助我们去生成别名。

### vite-plugin-html

vite-plugin-html可以帮助我们注入html标签，更改主页名字，支持自动更改。

```html
<title>
    <%= title %>
</title>
```

### vite-plugin-mock

mock数据： 数据模拟

1. 简单方式：直接写死数据，方便调试
   - 缺陷：
     - 没法获得海量数据测试
     - 没法获得一些标准数据
     - 没法感知http异常
2. mockjs: 用于模拟海量数据，vite-plugin-mock的依赖项是mockjs

> 详细参考`mockjs`官网文档

## vite优化策略

到底是优化什么东西？

- 开发时态的构建速度优化： npm yarn pnpm dev/start 的一瞬间到呈现所需时长。
- 页面性能指标：与写下的代码有关
  - 首屏渲染时：fcp(first content paint)
    - 懒加载
    - http优化：协商缓存和强缓存
      - 强缓存：服务端给响应头追加一些字段(expires)，客户端会记住这些字段，在expires截止失效时间没有到达之前，无论如何刷新页面，都不会重新请求页面。
      - 协商缓存：当服务端给我们打上协商缓存的标记以后，客户端在下次刷新页面需要重新请求资源时会发送一个协商请求给服务端，服务端如果说需要变化，则会响应具体的内容，如果服务端觉得没变化则会响应304。
  - 页面中最大元素的一个时长： lcp(largest content paint)
  - ...
- JS逻辑：
  - 需要注意副作用的清除（定时器）
  - 浏览器16.6ms更新一次，使用`requestAnimationFrame`、`requestIdleCallback`用于卡浏览器帧率
    - requestIdleCallback: 传一个函数进去
    - concurrent mode --> concurrency 可中断渲染
  - 防抖节流，尽量使用`lodash`工具 Array.prototype.forEach
- CSS
  - 关注继承属性：能继承的就不要重复写
  - 尽量避免太过于深的css嵌套
- 构建的优化：vite(rollup) webpack
  - 优化体积：压缩、treeshaking、图片资源压缩、cdn加载、分包

## 分包策略

分包策略就是把一些不会常规更新的文件，进行单独打包处理。(比如`node_modules`)

```js
import { forEach } from "lodash"
// tsconfig.js
{
  // 从node_modules去找包
  "moduleResolution": "node",

}
...

// vite.config.js
export default defineConfig({
  "build": {
    "miniify": false,
    "rollupOptions": {
      "input": {
        main: path.resolve(__dirname, "./index.html"),
        product: path.resolve(__dirname, "./src/product.html")
      },
      "output": {
        "manualChunks": (id: string) => {
          if(id.includes("node_modules"){
            return "vendor";
          })
        }
      }
    }
  }
)}
```

## gzip压缩

将所有的静态文件进行压缩，以达到减少体积的目的。

chunk -> 块 
从入口文件到它的一系列依赖最终打包成的js文件叫做块，块最终会映射成js文件，但是块不是js文件。

使用方法：

```js
// 先安装npm包
// $ pnpm add vite-plugin-compression

// 在vite.config.js后使用
import viteCompression from 'vite-plugin-compression';

export default () => {
  return {
    plugins: [viteCompression()],
  };
};
```

服务端要读取gzip文件(.gz后缀) 设置一个响应头 content-encoding
-> gzip（代表告诉浏览器改文件是使用gzip压缩过的）
但是浏览器需要承担一定的解压时间，如果体积不是很大的话，不要使用gzip压缩。

## 动态导入

动态导入一般用于路由(router)

```js
const routes = {
  {
    path: "/home",
    // import始终返回Promise
    component: import("./Home")
  }
}
```

## cdn加速

cdn: content delivery network 内容分发网络

将我们依赖度的第三方模块全部写成cdn的形式，然后保证我们自己代码体积比较小（体积小、服务器和客户端的传输压力小）

cdn -> 内容分发 dns

使用方法：

```js
// vite.config.js
import viteCDNPlugin from "vite-plugin-cdn-import";

export default defineConfig({
    plugin: [
      viteCDNPlugin({
        modules: [
          name: "lodash",
          // 配置全局导出符号
          var: "_",
          // 填写cdn地址
          path: "https://xxx.xx"
        ]
      })
    ]
})
```

## vite处理跨域

仅有在浏览器上有同源策略

- 开发时解决方案（构建工具或脚手架或第三方库的proxy代理配置）

```js
// vite.config.js
import { defineConfig } from "vite";

export default defineConfig({
  server: { // 开发服务器的配置，使用自己的服务器就不会有同源策略了
    proxy: {
      "/api": { // key + value
        target: "https://www.baidu.com",
        changeOrigin: true,
        // 是否要重写api
        rewreite: (path) => path.replace(/^\/api/,'')
      }
    }
  }
})
```

- 生产时态一般是直接交给后端处理跨域的：
  - Nginx：代理服务
  - 配置身份标记(Access-Control-Allow-Origin)