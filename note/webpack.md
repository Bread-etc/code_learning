# WebPack

> https://yk2012.[github.io](http://search.bilibili.com/all?from_source=webcommentline_search&keyword=github.io)/sgg_webpack5/ 在线课件

## 1.基本介绍

    Webpack是一个静态资源打包工具,将我们项目所有文件编译组合成一个或多个文件输出出去,输出的文件称为`bundle` (捆/束)

### 功能介绍

- 开发模式(--mode=development) : 仅能编译JS中的`ES Module`语法

- 生产模式(--mode=production) : 能编译JS中`ES Module`语法,还能压缩JS代码

### 开始使用

- 资源目录
  
  ```directory
  webpack_code            # 项目根目录（所有指令必须在这个目录运行）
      └── src             # 项目源码目录
          ├── js          # js文件目录
          │   ├── count.js
          │   └── sum.js
          └── main.js     # 项目主文件
  ```

- 下载依赖
  
  ```powershell
  npm i webpack webpack-cli -D
  ```

- 启用Webpack
  
  ```powershell
  #开发模式
  npx webpack ./src/main.js --mode=development
  #生产模式
  npx webpack ./src/main.js --mode=production
  ```

>  最终文件输出在 `dist` 目录

## 2.基本配置

### 5大核心概念

1. entry (入口)

2. output (输出)

3. loader (加载器)

4. plugins (插件)

5. mode (模式)

### 修改配置文件

```javascript
// Node.js的核心模块，专门用来处理文件路径
const path = require("path");

module.exports = {
  // 入口
  // 相对路径和绝对路径都行
  entry: "./src/main.js",
  // 输出
  output: {
    // path: 文件输出目录，必须是绝对路径
    // path.resolve()方法返回一个绝对路径
    // __dirname 当前文件的文件夹绝对路径
    path: path.resolve(__dirname, "dist"),
    // filename: 输出文件名
    filename: "main.js",
  },
  // 加载器
  module: {
    rules: [],
  },
  // 插件
  plugins: [],
  // 模式
  mode: "development", // 开发模式
};
```

>  配置完成后需要输入`npx webpack`重新运行打包指令

## 3.处理样式资源

### 处理CSS资源

    Webpack本身不能识别样式资源,需要借助Loader来帮助Webpack解析样式资源

- 下载包

```powershell
npm i css-loader style-loader -D
```

- 功能介绍
  
  - `css-loader` : 负责将CSS文件编译成Webpack能识别的模块
  
  - `style-loader` : 会动态创建一个Style标签,里面放置Webpack中CSS模块内容

- 配置

```javascript
module: {
  rules: [
    {
      // 用来匹配 .css 结尾的文件
      test: /\.css$/,
      // use 数组里面 Loader 执行顺序是从右到左
      use: ["style-loader", "css-loader"],
    },
  ],
},
```

### 处理LESS资源

    less是css的一个预处理器

- 下载包

```powershell
npm install less less-loader --save-dev
```

- 配置文件

```javascript
{
    test:/\.less$/,
    use:[
        "style-loader",
        "css-loader",
        "less-loader"    
    ]
}
```

### 处理SASS资源

- 下载

```powershell
npm install sass-loader sass webpack --save-dev
```

- 配置文件

```javascript
{
  test: /\.s[ac]ss$/,
  use: [
    "style-loader",
    "css-loader", 
    "sass-loader"
  ],
},
```

### 处理STYLUS资源

- 下载

```powershell
npm install stylus stylus-loader --save-dev
```

- 配置文件

```javascript
{
  test: /\.styl$/,
  use: [
    "style-loader",
    "css-loader",
    "stylus-loader"
  ],
},
```

## 4.处理图片资源

    在Webpack4中,我们使用`file-loader`和`url-loader`进行处理,在Webpack5中已经内置了,无需另外再下载这两个包

- 配置文件

```javascript
{
  test: /\.(png|jpe?g|gif|webp)$/,
  type: "asset",
  parser: {
    dataUrlCondition: {
      // 小于10kb的图片会被base64处理
      // 优点 : 减少请求数量 缺点 : 体积会更大
      maxSize: 10 * 1024
    }
  }
},
```

>  其中使用maxSize对小图片进行网络请求优化

## 5.处理字体图标资源和其他资源

- 引入资源

```javascript
import "./css/iconfont.css";
```

- 使用字体图标

```html
    <!-- 使用字体图标 -->
    <i class="iconfont icon-arrow-down"></i>
    <i class="iconfont icon-ashbin"></i>
    <i class="iconfont icon-browse"></i>
```

- 配置文件

```javascript
{
  test: /\.(ttf|woff2?|mp3|mp4|avi)$/,
  type: "asset/resource",
  generator: {
    // 输出图片名称 hash:10代表哈希值只取前十位
    filename: 'static/media/[hash:10][ext][query]'
  }
},
```

>  其他资源如需处理,只需要在test中增加后缀名就可以了

## 6.部署webpack小技巧

- 修改输出资源的名称和路径

```javascript
filename: "static/js/main.js", // 将 js 文件输出到 static/js 目录中
generator: {
    // 将图片文件输出到 static/imgs 目录中
    // 将图片文件命名 [hash:8][ext][query]
    // [hash:8]: hash值取8位
    // [ext]: 使用之前的文件扩展名
    // [query]: 添加之前的query参数
    filename: "static/imgs/[hash:8][ext][query]",
},
```

- 自动清空上次打包目录 (./dist目录)

    在`output`中加入该配置项

```javascript
clean: true, // 自动将上次打包目录资源清空
```

- 使用`dev-Server`

```powershell
npm i webpack-dev-server --save-dev
```

- 配置文件

```javascript
  // 开发服务器
  devServer: {
    host: "localhost", // 启动服务器域名
    port: "3000", // 启动服务器端口号
    open: true, // 是否自动打开浏览器
  },
```

>  配置完毕后,每次开启服务输入`npx webpack serve` 来开启服务器

## 7.处理JS资源

    Webpack 对 js 处理是有限的，只能编译js中ES模块化语法，不能编译其他语法，导致 js 不能在IE等浏览器运行，所以我们希望做一些兼容性处理。

    其次开发中，团队对代码格式是有严格要求的，我们不能由肉眼去检测代码格式，需要使用专业的工具来检测。

- 针对 js 兼容性处理，我们使用 `Babel` 来完成
- 针对代码格式，我们使用 `Eslint` 来完成

我们先完成 Eslint，检测代码格式无误后，在由 Babel 做代码兼容性处理

### Eslint

    ESLint是用于检测js和jsx的代码检测工具，我们使用 Eslint，关键是写 Eslint 配置文件，里面写上各种 rules 规则，将来运行 Eslint 时就会以写的规则对代码进行检查

- 配置文件
  
  - `.eslintrc.*`
  
  - `.eslintrc`
  
  - `.eslintrc.js`
  
  - `.eslintrc.json`

- 具体配置

```javascript
module.exports = {
  // 解析选项
  parserOptions: {},
  // 具体检查规则
  rules: {},
  // 继承其他规则
  extends: [],
  // ...
  // 其他规则详见：https://eslint.bootcss.com/docs/user-guide/configuring
};
```

- `rules`具体规则
  
  - `"off"`或`0` -关闭规则
  
  - `"warn"`或`1` -开启规则,使用警告级别错误
  
  - `"error"`或`2` - 开启规则,使用错误级别的错误

```javascript
rules: {
  semi: "error", // 禁止使用分号
  'array-callback-return': 'warn', // 强制数组方法的回调函数中有 return 语句，否则警告
  'default-case': [
    'warn', // 要求 switch 语句中有 default 分支，否则警告
    { commentPattern: '^no default$' } // 允许在最后注释 no default, 就不会有警告了
  ],
  eqeqeq: [
    'warn', // 强制使用 === 和 !==，否则警告
    'smart' // https://eslint.bootcss.com/docs/rules/eqeqeq#smart 除了少数情况下不会有警告
  ],
}
```

- `extends`继承

```javascript
// 例如在React项目中，我们可以这样写配置
module.exports = {
  extends: ["react-app"],
  rules: {
    // 我们的规则会覆盖掉react-app的规则
    // 所以想要修改规则直接改就是了
    eqeqeq: ["warn", "smart"],
  },
};
```

>  在VS Code中的ESLint插件中,我们可以添加`.eslintignore`文件来忽略被检测文件

### Babel

    JavaScript 编译器。

    主要用于将 ES6 语法编写的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中

- 配置文件
  
  - `babel.config.*` : 新建文件,位于项目根目录
  
  - `babel.config.js`
  
  - `babel.config.json`
  
  - `babelrc.*` : 新建文件,位于项目根目录
  
  - `.babelrc`
  
  - `.babelrc.js`
  
  - `.babelrc.json`

- 具体配置

```javascript
module.exports = {
  // 预设 (一组Babel插件,用于扩展Babel功能)
  presets: [],
};
```

`@babel/preset-env`: 一个智能预设，允许您使用最新的 JavaScript。

`@babel/preset-react`：一个用来编译 React jsx 语法的预设

`@babel/preset-typescript`：一个用来编译 TypeScript 语法的预设

- 具体规则

```javascript
{
  test: /\.js$/,
  exclude: /(node_modules)/,      // 排除node_modules文件夹里的文件不处理
  use: {
    loader: 'babel-loader'
  }
},
```

>  可以在src目录创建`babel.config.js`来分离配置文件

## 8.处理HTML资源

- 下载包

```powershell
npm i html-webpack-plugin -D
```

- 引入

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");
```

- 配置文件

```javascript
new HtmlWebpackPlugin({
  // 模板 : 以public/index.html文件创建新的html文件
  // 新的html文件特点: 1.结构和原来一致 2.自动引入打包输出的资源
  template: path.resolve(__dirname,"public/index.html")
})
```

这下,我们可以自动引入HTML文件了,并以public下的为模板生成在static目录内

## 9.生产模式

    生产模式是开发完成代码后，我们需要得到代码将来部署上线。这个模式下我们主要对代码进行优化，让其运行性能更好。

优化主要从两个角度出发:

1. 优化代码`运行性能`
2. 优化代码`打包速度`

### 文件目录

```powershell
├── webpack-test (项目根目录)
    ├── config (Webpack配置文件目录)
    │    ├── webpack.dev.js(开发模式配置文件)
    │    └── webpack.prod.js(生产模式配置文件)
    ├── node_modules (下载包存放目录)
    ├── src (项目源码目录，除了html其他都在src里面)
    │    └── 略
    ├── public (项目html文件)
    │    └── index.html
    ├── .eslintrc.js(Eslint配置文件)
    ├── babel.config.js(Babel配置文件)
    └── package.json (包的依赖管理配置文件)
```

> 因为文件目录变了，所以所有绝对路径需要回退一层目录才能找到对应的文件

### 配置运行指令

```json
// package.json
{
  // 其他省略
  "scripts": {
    "start": "npm run dev",
    "dev": "npx webpack serve --config ./config/webpack.dev.js",
    "build": "npx webpack --config ./config/webpack.prod.js"
  }
}
```

以后启动指令：

- 开发模式：`npm start` 或 `npm run dev`
- 生产模式：`npm run build`

## 10.CSS处理

### 提取 CSS 成单独文件

    CSS文件被打包到 js 文件中，当 js 文件加载时，会创建一个 `style`标签生成样式，会出现闪屏现象，用户体验不好.我们应该是单独的 Css 文件，通过 `link`标签加载性能才好

- 下载包

```powershell
npm i mini-css-extract-plugin -D
```

- 配置

```javascript
// 引入插件
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

//配置项
use: [MiniCssExtractPlugin.loader, "css-loader"],

// 提取css成单独文件
new MiniCssExtractPlugin({
  // 定义输出文件名和目录
  filename: "static/css/main.css",
}),
```

### CSS兼容性处理 (适配IE)

- 下载包

```powershell
npm i postcss-loader postcss postcss-preset-env -D
```

- 配置文件

```javascript
{
  loader: "postcss-loader",
  options: {
    postcssOptions: {
      plugins: [
        "postcss-preset-env", // 能解决大多数样式兼容性问题
      ],
    },
  },
},
```

### 控制兼容性

    我们可以在 `package.json` 文件中添加 `browserslist` 来控制样式的兼容性做到什么程度。

```json
{
  // 其他省略
  "browserslist": ["ie >= 8"]
}
```

    不过开发中一般使用这个

```json
{
  // 其他省略
  "browserslist": ["last 2 version", "> 1%", "not dead"]
}
```

### 合并配置 (自定义loader)

```javascript
// 获取处理样式的Loaders
const getStyleLoaders = (preProcessor) => {
  return [
    MiniCssExtractPlugin.loader,
    "css-loader",
    {
      loader: "postcss-loader",
      options: {
        postcssOptions: {
          plugins: [
            "postcss-preset-env", // 能解决大多数样式兼容性问题
          ],
        },
      },
    },
    preProcessor,
  ].filter(Boolean);
};
```

    分别在rules中进行传参改动

```javascript
// css-loader
use: getStyleLoaders(),
// less-loader
use: getStyleLoaders("less-loader"),
// sass-loader
use: getStyleLoaders("sass-loader"),
// stylus-loader
use: getStyleLoaders("stylus-loader"),
```

### CSS压缩

    在Webpack中,HTML和Javascript已经内置了压缩,无需再进行压缩

- 下载包

```powershell
npm i css-minimizer-webpack-plugin -D
```

- 使用压缩

```javascript
// 引入
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");

// 使用插件
// css压缩
new CssMinimizerPlugin(),
```

>  在进行以上在`prod.js`中修改以后,要使用`npm run build`重新打包

## 11.Webpack高级配置

优化角度:

1. 提升开发体验
2. 提升打包构建速度
3. 减少代码体积
4. 优化代码运行性能

### 提升开发体验

#### SourceMap

    `SourceMap`（源代码映射）是一个用来生成源代码与构建后代码一一映射的文件的方案。

它会生成一个 `xxx.map` 文件，里面包含源代码和构建后代码每一行、每一列的映射关系。当构建后代码出错了，会通过 xxx.map 文件，从构建后代码出错位置找到映射后源代码出错位置，从而让浏览器提示源代码文件出错位置，帮助我们更快的找到错误根源。

    实际开发中主要关注两种模式 : 

- 开发模式 : `cheap-module-source-map`
  
  - 优点 : 打包编译速度快,只包含行映射
  
  - 缺点 : 没有列映射

```javascript
module.exports = {
  // 其他省略
  mode: "development",
  devtool: "cheap-module-source-map",
};
```

- 生产模式 : `source-map`
  
  - 优点 : 包含行/列映射
  
  - 缺点 : 打包编译速度更慢

```javascript
module.exports = {
  // 其他省略
  mode: "production",
  devtool: "source-map",
};
```

>  但实际开发中在生产模式下一般使用`no-source-map`或者`none`

---

> 关于其他的sourcemap配置,可以在此处查看[https://webpack.docschina.org/configuration/devtool/]()

#### HotModuleReplacement

    `HotModuleReplacement`（HMR/热模块替换）：在程序运行中，替换、添加或删除模块，而无需重新加载整个页面。该模块可以帮助我们只打包编译部分代码,加快打包速度

- 使用方式

```javascript
module.exports = {
  // 其他省略
  devServer: {
    host: "localhost", // 启动服务器域名
    port: "3000", // 启动服务器端口号
    open: true, // 是否自动打开浏览器
    hot: true, // 开启HMR功能（只能用于开发环境，生产环境不需要了）
  },
};
```

此时 css 样式经过 style-loader 处理，已经具备 HMR 功能了。 但是 js 还不行。

```javascript
// main.js
import count from "./js/count";
import sum from "./js/sum";
// 引入资源，Webpack才会对其打包
import "./css/iconfont.css";
import "./css/index.css";
import "./less/index.less";
import "./sass/index.sass";
import "./sass/index.scss";
import "./styl/index.styl";

const result1 = count(2, 1);
console.log(result1);
const result2 = sum(1, 2, 3, 4);
console.log(result2);

// 判断是否支持HMR功能
if (module.hot) {
  module.hot.accept("./js/count.js", function (count) {
    const result1 = count(2, 1);
    console.log(result1);
  });

  module.hot.accept("./js/sum.js", function (sum) {
    const result2 = sum(1, 2, 3, 4);
    console.log(result2);
  });
}
```

>  但在实际开发中,我们很少自己写HMR,一般会使用其他loader,比如`vue-loader`,`react-hot-loader`

#### OneOf

    使一个文件只能被一个loader识别,增加打包速度

- 使用方式

```javascript
  module: {
    rules: [
      {
        oneOf: [
          {
            // 用来匹配 .css 结尾的文件
            test: /\.css$/,
            // use 数组里面 Loader 执行顺序是从右到左
            use: ["style-loader", "css-loader"],
          },
          {
            test: /\.less$/,
            use: ["style-loader", "css-loader", "less-loader"],
          },
          {
            test: /\.s[ac]ss$/,
            use: ["style-loader", "css-loader", "sass-loader"],
          },
          {
            test: /\.styl$/,
            use: ["style-loader", "css-loader", "stylus-loader"],
          },
          {
            test: /\.(png|jpe?g|gif|webp)$/,
            type: "asset",
            parser: {
              dataUrlCondition: {
                maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
              },
            },
            generator: {
              // 将图片文件输出到 static/imgs 目录中
              // 将图片文件命名 [hash:8][ext][query]
              // [hash:8]: hash值取8位
              // [ext]: 使用之前的文件扩展名
              // [query]: 添加之前的query参数
              filename: "static/imgs/[hash:8][ext][query]",
            },
          },
          {
            test: /\.(ttf|woff2?)$/,
            type: "asset/resource",
            generator: {
              filename: "static/media/[hash:8][ext][query]",
            },
          },
          {
            test: /\.js$/,
            exclude: /node_modules/, // 排除node_modules代码不编译
            loader: "babel-loader",
          },
        ],
      },
    ]
```

    其实就是在`rules`下,加入一个大括号,内部加入`oneOf`并用`中括号`括起内容loader

#### Include/Exclude

    开发的时候经常使用第三方库或插件，所有文件都下载到 `node_modules `中了。而这些文件是不需要编译可以直接使用的。所以我们在对 js 文件处理时，要排除 node_modules 下面的文件。

- 使用方式
  
  - include 只包含文件
  
  - exclude 排除文件

```javascript
{
  test: /\.js$/,
  // exclude: /node_modules/, // 排除node_modules代码不编译
  include: path.resolve(__dirname, "../src"), // 也可以用包含
  loader: "babel-loader",
},
```

```javascript
new ESLintWebpackPlugin({
  // 指定检查文件的根目录
  context: path.resolve(__dirname, "../src"),
  exclude: "node_modules", // 默认值
}),
```

#### Cache (缓存)

    每次打包时 js 文件都要经过 Eslint 检查 和 Babel 编译，速度比较慢。我们可以缓存之前的 Eslint 检查 和 Babel 编译结果，这样第二次打包时速度就会更快了。

- 使用方式

```javascript
{
  test: /\.js$/,
  // exclude: /node_modules/, // 排除node_modules代码不编译
  include: path.resolve(__dirname, "../src"), // 也可以用包含
  loader: "babel-loader",
  options: {
    cacheDirectory: true, // 开启babel编译缓存
    cacheCompression: false, // 缓存文件不要压缩
  },
},
```

- 给ESLint添加

```javascript
new ESLintWebpackPlugin({
  // 指定检查文件的根目录
  context: path.resolve(__dirname, "../src"),
  exclude: "node_modules", // 默认值
  cache: true, // 开启缓存
  // 缓存目录
  cacheLocation: path.resolve(__dirname,
    "../node_modules/.cache/.eslintcache"),
}),
```

#### Thead

    多进程打包：开启电脑的多个进程同时干一件事，速度更快。

    **需要注意：请仅在特别耗时的操作中使用，因为每个进程启动就有大约为 600ms 左右开销。**

- 获取CPU核数

```javascript
// nodejs核心模块，直接使用
const os = require("os");
// cpu核数
const threads = os.cpus().length;
```

>  一般情况下,都使用 CPU - 1

- 使用方式

```javascript
const os = require("os");
const TerserPlugin = require("terser-webpack-plugin");
// cpu核数
const threads = os.cpus().length;
...
use: [
  {
    loader: "thread-loader", // 开启多进程
    options: {
      workers: threads, // 数量
    },
  },
  {
    loader: "babel-loader",
    options: {
      cacheDirectory: true, // 开启babel编译缓存
    },
  },
]

...
optimization: {
  minimize: true,
  minimizer: [
    // css压缩也可以写到optimization.minimizer里面，效果一样的
    new CssMinimizerPlugin(),
    // 当生产模式会默认开启TerserPlugin，但是我们需要进行其他配置，就要重新写了
    new TerserPlugin({
      parallel: threads // 开启多进程
    })
  ],
},
```

>  注意 : 需要在ESLint中加入`threads`来使用多进程,且上述实例是在prod生产模式下

### 提升打包构建速度

#### Tree Shaking

    开发时我们定义了一些工具函数库，或者引用第三方工具函数库或组件库。如果没有特殊处理的话我们打包时会引入整个库，但是实际上可能我们可能只用上极小部分的功能。这样将整个库都打包进来，体积就太大了。

    `Tree Shaking` 是一个术语，通常用于描述移除 JavaScript 中的没有使用上的代码。

**注意：它依赖 `ES Module`。**

> webpack中默认开启了`Tree Shaking`无需自行配置

#### Babel

    `Babel`为编译的每个文件都插入了辅助代码，使代码体积过大！`Babel`对一些公共方法使用了非常小的辅助代码，比如 `_extend`。默认情况下会被添加到每一个需要它的文件中。你可以将这些辅助代码作为一个独立模块，来避免重复引入。

>     `@babel/plugin-transform-runtime`: 禁用了 Babel 自动对每个文件的 runtime 注入，而是引入 `@babel/plugin-transform-runtime` 并且使所有辅助代码从这里引用。

- 下载包

```powershell
npm i @babel/plugin-transform-runtime -D
```

- 配置文件

```javascript
{
  loader: "babel-loader",
  options: {
    // presets: [@babel/preset-env]
    // 开启babel缓存
    cacheDirectory: true,
    // 关闭缓存文件压缩
    cacheCompression: false,
    plugins: ["@babel/plugin-transform-runtime"], // 减少代码体积
  },
}
```

> 在生产模式和开发模式下皆可以使用

#### Image Minimizer

    用于压缩`本地项目`的静态资源图片体积,在线链接无需压缩

    `image-minimizer-webpack-plugin`: 用来压缩图片的插件

- 下载包

```powershell
npm i image-minimizer-webpack-plugin imagemin -D
# 无损压缩
npm install imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo -D
# 有损压缩
npm install imagemin-gifsicle imagemin-mozjpeg imagemin-pngquant imagemin-svgo -D
```

- 配置文件

```javascript
const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
...
// 压缩图片
new ImageMinimizerPlugin({
  minimizer: {
    implementation: ImageMinimizerPlugin.imageminGenerate,
    options: {
      plugins: [
        ["gifsicle", { interlaced: true }],
        ["jpegtran", { progressive: true }],
        ["optipng", { optimizationLevel: 5 }],
        [
          "svgo",
          {
            plugins: [
              "preset-default",
              "prefixIds",
              {
                name: "sortAttrs",
                params: {
                  xmlnsOrder: "alphabetical",
                },
              },
            ],
          },
        ],
      ],
    },
  },
}),
```

>     打包过程中会出现报错,需要安装`jpegtran.exe`和`optipng.exe`到node_modules中才能解决问题

**不过一般我们不推荐使用这种压缩方式,使用外部网络链接更好一些**

### 优化代码运行性能

#### Code Split

    打包代码时会将所有 js 文件打包到一个文件中，体积太大了。我们如果只要渲染首页，就应该只加载首页的 js 文件，其他文件不应该加载。所以我们需要将打包生成的文件进行代码分割，生成多个 js 文件，渲染哪个页面就只加载某个 js 文件，这样加载的资源就少，速度就更快。

    代码分割（`Code Split`）主要做了两件事：

`分割`文件：将打包生成的文件进行`分割`，生成多个 js 文件。

`按需加载`：需要哪个文件就加载哪个文件。

##### 1.多入口

- 文件目录

```powershell
├── public
├── src
|   ├── app.js
|   └── main.js
├── package.json
└── webpack.config.js
```

- 下载包

```powershell
npm i webpack webpack-cli html-webpack-plugin -D
```

- 配置文件

```javascript
// 单入口
// entry: './src/main.js',
// 多入口
entry: {
  main: "./src/main.js",
  app: "./src/app.js",
},
```

##### 2.提取重复代码

- 配置文件

```javascript
  optimization: {
    // 代码分割配置
    splitChunks: {
      chunks: "all", // 对所有模块都进行分割
      // 以下是默认值
      // minSize: 20000, // 分割代码最小的大小
      // minRemainingSize: 0, // 类似于minSize，最后确保提取的文件大小不能为0
      // minChunks: 1, // 至少被引用的次数，满足条件才会代码分割
      // maxAsyncRequests: 30, // 按需加载时并行加载的文件的最大数量
      // maxInitialRequests: 30, // 入口js文件最大并行请求数量
      // enforceSizeThreshold: 50000, // 超过50kb一定会单独打包（此时会忽略minRemainingSize、maxAsyncRequests、maxInitialRequests）
      // cacheGroups: { // 组，哪些模块要打包到一个组
      //   defaultVendors: { // 组名
      //     test: /[\\/]node_modules[\\/]/, // 需要打包到一起的模块
      //     priority: -10, // 权重（越大越高）
      //     reuseExistingChunk: true, // 如果当前 chunk 包含已从主 bundle 中拆分出的模块，则它将被重用，而不是生成新的模块
      //   },
      //   default: { // 其他没有写的配置会使用上面的默认值
      //     minChunks: 2, // 这里的minChunks权重更大
      //     priority: -20,
      //     reuseExistingChunk: true,
      //   },
      // },
      // 修改配置
      cacheGroups: {
        // 组，哪些模块要打包到一个组
        // defaultVendors: { // 组名
        //   test: /[\\/]node_modules[\\/]/, // 需要打包到一起的模块
        //   priority: -10, // 权重（越大越高）
        //   reuseExistingChunk: true, // 如果当前 chunk 包含已从主 bundle 中拆分出的模块，则它将被重用，而不是生成新的模块
        // },
        default: {
          // 其他没有写的配置会使用上面的默认值
          minSize: 0, // 我们定义的文件体积太小了，所以要改打包的最小文件体积
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
```

##### 3.按需加载,动态导入

    实现按需加载,可以快速渲染页面,提升用户体验

- 修改文件

```javascript
console.log("hello main");

document.getElementById("btn").onclick = function () {
  // 动态导入 --> 实现按需加载
  // 即使只被引用了一次，也会代码分割
  import("./math.js").then(({ sum }) => {
    alert(sum(1, 2, 3, 4, 5));
  });
};
```

>  一旦通过 import 动态导入语法导入模块，模块就被代码分割，同时也能按需加载了。

##### 4.单入口

    对于单页面应用仅仅需要一个入口文件

```javascript
entry: "./src/main.js",
...
optimization: {
  // 代码分割配置
  splitChunks: {
    chunks: "all", // 对所有模块都进行分割
  }
}
```

##### 5.更新配置

    最终我们会使用单入口+代码分割+动态导入方式来进行配置。更新之前的配置文件。

```javascript
// 代码分割配置
splitChunks: {
  chunks: "all", // 对所有模块都进行分割
  // 其他内容用默认配置即可
},
```

##### 6.给动态导入文件命名

- 修改文件

```javascript
document.getElementById("btn").onClick = function () {
  // eslint会对动态导入语法报错，需要修改eslint配置文件
  // webpackChunkName: "math"：这是webpack动态导入模块命名的方式
  // "math"将来就会作为[name]的值显示。
  import(/* webpackChunkName: "math" */ "./js/math.js").then(({ count }) => {
    console.log(count(2, 1));
  });
};
```

**注意: 此处命名比较神奇,在类似注释中实现命名`/**/`,比较魔幻**

- 在ESLint中加入配置,防止报错

```javascript
  plugins: ["import"], // 解决动态导入import语法报错问题 --> 实际使用eslint-plugin-import的规则解决的
```

- 统一命名配置

```javascript
filename: "static/js/[name].js", // 入口文件打包输出资源命名方式
chunkFilename: "static/js/[name].chunk.js", // 动态导入输出资源命名方式
assetModuleFilename: "static/media/[name].[hash][ext]", // 图片、字体等资源命名方式（注意用hash）

// css文件改名
// 定义输出文件名和目录
filename: "static/css/[name].css",
chunkFilename: "static/css/[name].chunk.css",
```

>  rules中的`generator`选项需要注释掉,否则会覆盖命名规则

#### Preload / Prefetch

    我们使用 `import`动态导入语法来进行代码按需加载（我们也叫`懒加载`，比如路由懒加载就是这样实现的）。但是加载速度还不够好，比如：是用户点击按钮时才加载这个资源的，如果资源体积很大，那么用户会感觉到明显卡顿效果。我们想在浏览器空闲时间，加载后续需要使用的资源。我们就需要用上 `Preload` 或 `Prefetch` 技术。

##### 主要区别

- `Preload`：告诉浏览器`立即`加载资源。

- `Prefetch`：告诉浏览器在`空闲`时才开始加载资源。 (常用)

它们共同点：

- 都只会加载资源，并不执行。
- 都有缓存。

它们区别：

- `Preload`加载优先级高，`Prefetch`加载优先级低。
- `Preload`只能加载当前页面需要使用的资源，`Prefetch`可以加载当前页面资源，也可以加载下一个页面需要使用的资源。

总结：

- 当前页面优先级高的资源用 `Preload` 加载。
- 下一个页面需要使用的资源用 `Prefetch` 加载。

它们的问题：兼容性较差。

- 我们可以去 [Can I Useopen in new window](https://caniuse.com/) 网站查询 API 的兼容性问题。
- `Preload` 相对于 `Prefetch` 兼容性好一点。

##### 使用方式

- 下载包

```powershell
npm i @vue/preload-webpack-plugin -D
```

- 配置文件

```javascript
const PreloadWebpackPlugin = require("@vue/preload-webpack-plugin");
...
new PreloadWebpackPlugin({
  rel: "preload", // preload兼容性更好
  as: "script",
  // rel: 'prefetch' // prefetch兼容性更差
}),
```

#### Network Cache

    前后输出的文件名是一样的，都叫main.js，一旦将来发布新版本，因为文件名没有变化导致浏览器会直接读取缓存，不会加载新资源，项目也就没法更新了。所以我们从文件名入手，确保更新前后文件名不一样，这样就可以做缓存了。

- 具体作用
  
  - 它们都会生成一个唯一的 `hash` 值。
    
    - `fullhash`（webpack4 是 hash）
    
        每次修改任何一个文件，所有文件名的 hash 至都将改变。所以一旦修改了任何一个文件，整个项目的文件缓存都将失效。
    
    - `chunkhash`
    
        根据不同的入口文件(Entry)进行依赖文件解析、构建对应的 chunk，生成对应的哈希值。我们 js 和 css 是同一个引入，会共享一个 hash 值。
    
    - `contenthash`
    
        根据文件内容生成 hash 值，只有文件内容变化了，hash 值才会变化。所有文件 hash 值是独享且不同的。 

- 使用方式

```javascript
// [contenthash:8]使用contenthash，取8位长度
filename: "static/js/[name].[contenthash:8].js", // 入口文件打包输出资源命名方式
chunkFilename: "static/js/[name].[contenthash:8].chunk.js", // 动态导入输出资源命名方式
...
filename: "static/css/[name].[contenthash:8].css",
chunkFilename: "static/css/[name].[contenthash:8].chunk.css",
```

>      项目更新导致`main.js`不更新解决思路 : 
> 
>                                 将 hash 值单独保管在一个 runtime 文件中。
> 
>     我们最终输出三个文件：`main`、`math`、`runtime`。当 math 文件发送变化，变化的是 math 和 runtime 文件，main 不变。`runtime`文件只保存文件的 `hash` 值和它们与文件关系，整个文件体积就比较小，所以变化重新请求的代价也小。

```javascript
//plugin 下
...
// 提取runtime文件
runtimeChunk: {
  name: (entrypoint) => `runtime~${entrypoint.name}`, // runtime文件命名规则
},
```

#### Core-js (重要)

    过去我们使用 babel 对 js 代码进行了兼容性处理，其中使用`@babel/preset-env` 智能预设来处理兼容性问题。

    它能将 ES6 的一些语法进行编译转换，比如箭头函数、点点点运算符等。但是如果是 async 函数、promise 对象、数组的一些方法（includes）等，它没办法处理。所以此时我们 js 代码仍然存在兼容性问题，一旦遇到低版本浏览器会直接报错。所以我们想要将 `js 兼容性问题`彻底解决

- 先修改ESLint
  
  - 下载包
  
  ```powershell
  npm i @babel/eslint-parser -D
  ```
  
  - 在.eslintrc.js中添加
  
  ```javascript
  module.exports = {
    // 继承 Eslint 规则
    extends: ["eslint:recommended"],
    parser: "@babel/eslint-parser", // 支持最新的最终 ECMAScript 标准
    env: {
      node: true, // 启用node中全局变量
      browser: true, // 启用浏览器中全局变量
    },
    plugins: ["import"], // 解决动态导入import语法报错问题 --> 实际使用eslint-plugin-import的规则解决的
    parserOptions: {
      ecmaVersion: 6, // es6
      sourceType: "module", // es module
    },
    rules: {
      "no-var": 2, // 不能使用 var 定义变量
    },
  }
  ```

- 使用`core-js`

```powershell
npm i core-js
```

- 引入包
  
  - 手动全部引入 (代码体积过大)
  
  ```javascript
  import "core-js";
  ```
  
  - 手动按需引入 (容易错漏,麻烦)
  
  ```javascript
  import "core-js/es/promise";
  ```
  
  - 自动按需引入 (重要)
  
  ```javascript
  module.exports = {
    // 智能预设：能够编译ES6语法
    presets: [
      [
        "@babel/preset-env",
        // 按需加载core-js的polyfill
        { useBuiltIns: "usage", corejs: { version: "3", proposals: true } },
      ],
    ],
  };
  ```

>  自动按需引入是在`babel.config.js`中写入

#### PWA技术

    渐进式网络应用程序(progressive web application - `PWA`)：是一种可以提供类似于 native app(原生应用程序) 体验的 Web App 的技术。其中最重要的是，在 **离线(offline)** 时应用程序能够继续运行功能。内部通过 `Service Workers` 技术实现的。

- 下载包

```javascript
npm i workbox-webpack-plugin -D
```

- 修改配置文件

```javascript
const WorkboxPlugin = require("workbox-webpack-plugin");
...
// plugin下
new WorkboxPlugin.GenerateSW({
  // 这些选项帮助快速启用 ServiceWorkers
  // 不允许遗留任何“旧的” ServiceWorkers
  clientsClaim: true,
  skipWaiting: true,
}),
```

- 在`main.js`中加入

```javascript
if ("serviceWorker" in navigator) {
  window.addEventListener("load", () => {
    navigator.serviceWorker
      .register("/service-worker.js")
      .then((registration) => {
        console.log("SW registered: ", registration);
      })
      .catch((registrationError) => {
        console.log("SW registration failed: ", registrationError);
      });
  });
}
```

>  在开发中会出现PWA不能注册的原因 : 本地缺少dist目录服务器
> 
>  可以这样做 : 
> 
> -    下载包
> 
> ```powershell
> npm i serve -g
> # 开启服务器
> serve dist
> ```
> 
> 此时通过 serve 启动的服务器我们 service-worker 就能注册成功了。

## 12.Webpack原理

### loader

#### 概念

    帮助 webpack 将不同类型的文件转换为 webpack 可识别的模块。

#### loader执行顺序

1. 分类
- `pre`： 前置 loader
- `normal`： 普通 loader
- `inline`： 内联 loader
- `post`： 后置 loader
2. 执行顺序
- 4 类 loader 的执行优级为：`pre > normal > inline > post` 。
- 相同优先级的 loader 执行顺序为：`从右到左，从下到上`。
3. 使用 loader 的方式
- 配置方式：在 `webpack.config.js` 文件中指定 loader。（pre、normal、post loader）

- 内联方式：在每个 `import` 语句中显式指定 loader。（inline loader）
4. inline loader

用法：`import Styles from 'style-loader!css-loader?modules!./styles.css';`

含义：

- 使用 `css-loader` 和 `style-loader` 处理 `styles.css` 文件
- 通过 `!` 将资源中的 loader 分开

`inline loader` 可以通过添加不同前缀，跳过其他类型 loader。

- `!` 跳过 normal loader。

`import Styles from '!style-loader!css-loader?modules!./styles.css';`

- `-!` 跳过 pre 和 normal loader。

`import Styles from '-!style-loader!css-loader?modules!./styles.css';`

- `!!` 跳过 pre、 normal 和 post loader。

`import Styles from '!!style-loader!css-loader?modules!./styles.css';`

#### 开发loader

```javascript
// loaders/loader1.js
module.exports = function loader1(content,map,meta) {
  console.log("hello loader");
  return content;
};
```

- `content` 源文件的内容
- `map` SourceMap 数据
- `meta` 数据，可以是任何内容,一般是来源于其他loader的数据

#### loader分类

##### 1.同步loader

```javascript
module.exports = function (content, map, meta) {
  return content;
};
```

- `this.callback` 方法则更灵活，因为它允许传递多个参数，而不仅仅是 `content`。

```javascript
module.exports = function (content, map, meta) {
  // 传递map，让source-map不中断
  // 传递meta，让下一个loader接收到其他参数
  this.callback(null, content, map, meta);
  return; // 当调用 callback() 函数时，总是返回 undefined
};
```

##### 2.异步loader

```javascript
module.exports = function (content, map, meta) {
  const callback = this.async();
  // 进行异步操作
  setTimeout(() => {
    callback(null, result, map, meta);
  }, 1000);
};
```

>     由于同步计算过于耗时，在 Node.js 这样的单线程环境下进行此操作并不是好的方案，我们建议`尽可能地使你的 loader 异步化`。但如果计算量很小，同步 loader 也是可以的。

##### 3.raw loader

    默认情况下，资源文件会被转化为 UTF-8 字符串，然后传给 loader。通过设置 raw 为 true，loader 可以接收原始的 Buffer。(缓存文件)

```javascript
module.exports = function (content) {
  // content是一个Buffer数据
  return content;
};
module.exports.raw = true; // 开启 Raw Loader
```

##### 4.pitch loader

- pitch loader 有些类似于eventListener的冒泡和捕获过程

```javascript
module.exports = function (content) {
  return content;
};
module.exports.pitch = function (remainingRequest, precedingRequest, data) {
  console.log("do somethings");
};
```

<img src="file:///C:/Users/Lenovo%20PC/Desktop/code/Bread-etc/note/asset/2023-07-27-01-29-23-image.png" title="" alt="" data-align="center">

-     在这个过程中如果任何 pitch 有返回值，则 loader 链被阻断。webpack 会跳过后面所有的的 pitch 和 loader，直接进入上一个 loader 。

<img title="" src="file:///C:/Users/Lenovo%20PC/Desktop/code/Bread-etc/note/asset/2023-07-27-01-30-48-image.png" alt="" data-align="center">

>     这种机制一般称为`(熔断)`机制

#### loader API

| 方法名                     | 含义                           | 用法                                             |
| ----------------------- | ---------------------------- | ---------------------------------------------- |
| this.async              | 异步回调 loader。返回 this.callback | const callback = this.async()                  |
| this.callback           | 可以同步或者异步调用的并返回多个结果的函数        | this.callback(err, content, sourceMap?, meta?) |
| this.getOptions(schema) | 获取 loader 的 options          | this.getOptions(schema)                        |
| this.emitFile           | 产生一个文件                       | this.emitFile(name, content, sourceMap)        |
| this.utils.contextify   | 返回一个相对路径                     | this.utils.contextify(context, request)        |
| this.utils.absolutify   | 返回一个绝对路径                     | this.utils.absolutify(context, request)        |

#### 手写定制loader

    此处省略,详情阅读自定义babel-loader,clean-log-loader,banner-loader

### Plugin

#### Plugin的作用

    通过插件我们可以扩展 webpack，加入自定义的构建行为，使 webpack 可以执行更广泛的任务，拥有更强的构建能力。(类似于vue中的`hooks`)

#### Plugin的工作原理

>     webpack 就像一条生产线，要经过一系列处理流程后才能将源文件转换成输出结果。 这条生产线上的每个处理流程的职责都是单一的，多个流程之间有存在依赖关系，只有完成当前处理后才能交给下一个流程去处理。 插件就像是一个插入到生产线中的一个功能，在特定的时机对生产线上的资源做处理。webpack 通过 Tapable 来组织这条复杂的生产线。 webpack 在运行过程中会广播事件，插件只需要监听它所关心的事件，就能加入到这条生产线中，去改变生产线的运作。 webpack 的事件流机制保证了插件的有序性，使得整个系统扩展性很好。 ——「深入浅出 Webpack」

    站在代码逻辑的角度就是：webpack 在编译代码过程中，会触发一系列 `Tapable` 钩子事件，插件所做的，就是找到相应的钩子，往上面挂上自己的任务，也就是注册事件，这样，当 webpack 构建的时候，插件注册的事件就会随着钩子的触发而执行了。

#### Webpack内部钩子

##### 什么是钩子

    钩子的本质就是：`事件`。为了方便我们直接介入和控制编译过程，webpack 把编译过程中触发的各类关键事件封装成事件接口暴露了出来。这些接口被很形象地称做：`hooks`（钩子）。开发插件，离不开这些钩子。

##### Tapable

    `Tapable` 为 webpack 提供了统一的插件接口（钩子）类型定义，它是 webpack 的核心功能库。webpack 中目前有十种 `hooks`，在 `Tapable` 源码中可以看到，他们是：

```javascript
// https://github.com/webpack/tapable/blob/master/lib/index.js
exports.SyncHook = require("./SyncHook");
exports.SyncBailHook = require("./SyncBailHook");
exports.SyncWaterfallHook = require("./SyncWaterfallHook");
exports.SyncLoopHook = require("./SyncLoopHook");
exports.AsyncParallelHook = require("./AsyncParallelHook");
exports.AsyncParallelBailHook = require("./AsyncParallelBailHook");
exports.AsyncSeriesHook = require("./AsyncSeriesHook");
exports.AsyncSeriesBailHook = require("./AsyncSeriesBailHook");
exports.AsyncSeriesLoopHook = require("./AsyncSeriesLoopHook");
exports.AsyncSeriesWaterfallHook = require("./AsyncSeriesWaterfallHook");
exports.HookMap = require("./HookMap");
exports.MultiHook = require("./MultiHook");
```

`Tapable` 还统一暴露了三个方法给插件，用于注入不同类型的自定义构建行为：

- `tap`：可以注册`同步`钩子和`异步`钩子。
- `tapAsync`：`回调`方式注册异步钩子。
- `tapPromise`：`Promise` 方式注册异步钩子。

#### Plugin构建对象

##### Compiler

    `compiler` 对象中保存着完整的 Webpack 环境配置，每次启动 webpack 构建时它都是一个独一无二，仅仅会创建一次的对象。

    这个对象会在首次启动 Webpack 时创建，我们可以通过 compiler 对象上访问到 Webapck 的主环境配置，比如 loader 、 plugin 等等配置信息。

它有以下主要属性：

- `compiler.options` 可以访问本次启动 webpack 时候所有的配置文件，包括但不限于 loaders 、 entry 、 output 、 plugin 等等完整配置信息。
- `compiler.inputFileSystem` 和 `compiler.outputFileSystem` 可以进行文件操作，相当于 Nodejs 中 fs。
- `compiler.hooks` 可以注册 tapable 的不同种类 Hook，从而可以在 compiler 生命周期中植入不同的逻辑。

> [compiler hooks](https://webpack.docschina.org/api/compiler-hooks/)

##### Compilation

    `compilation` 对象代表一次资源的构建，compilation 实例能够访问所有的模块和它们的依赖。

    一个 compilation 对象会对构建依赖图中所有模块，进行编译。 在编译阶段，模块会被加载(load)、封存(seal)、优化(optimize)、 分块(chunk)、哈希(hash)和重新创建(restore)。

它有以下主要属性：

- `compilation.modules` 可以访问所有模块，打包的每一个文件都是一个模块。
- `compilation.chunks` chunk 即是多个 modules 组成而来的一个代码块。入口文件引入的资源组成一个 chunk，通过代码分割的模块又是另外的 chunk。
- `compilation.assets` 可以访问本次打包生成所有文件的结果。
- `compilation.hooks` 可以注册 tapable 的不同种类 Hook，用于在 compilation 编译模块阶段进行逻辑添加以及修改。

> [compilation hooks](https://webpack.docschina.org/api/compilation-hooks/)

##### 生命周期简图

<img src="file:///C:/Users/Lenovo%20PC/Desktop/code/Bread-etc/note/asset/2023-07-27-21-38-18-image.png" title="" alt="" data-align="center">

>  类似于`vue`中的生命周期

##### 注册生命周期钩子(hooks)

```javascript
class TestPlugin {
  constructor() {
    console.log("TestPlugin constructor()");
  }
  // 1. webpack读取配置时，new TestPlugin() ，会执行插件 constructor 方法
  // 2. webpack创建 compiler 对象
  // 3. 遍历所有插件，调用插件的 apply 方法
  apply(compiler) {
    console.log("TestPlugin apply()");

    // 从文档可知, compile hook 是 SyncHook, 也就是同步钩子, 只能用tap注册
    compiler.hooks.compile.tap("TestPlugin", (compilationParams) => {
      console.log("compiler.compile()");
    });

    // 从文档可知, make 是 AsyncParallelHook, 也就是异步并行钩子, 特点就是异步任务同时执行
    // 可以使用 tap、tapAsync、tapPromise 注册。
    // 如果使用tap注册的话，进行异步操作是不会等待异步操作执行完成的。
    compiler.hooks.make.tap("TestPlugin", (compilation) => {
      setTimeout(() => {
        console.log("compiler.make() 111");
      }, 2000);
    });

    // 使用tapAsync、tapPromise注册，进行异步操作会等异步操作做完再继续往下执行
    compiler.hooks.make.tapAsync("TestPlugin", (compilation, callback) => {
      setTimeout(() => {
        console.log("compiler.make() 222");
        // 必须调用
        callback();
      }, 1000);
    });

    compiler.hooks.make.tapPromise("TestPlugin", (compilation) => {
      console.log("compiler.make() 333");
      // 必须返回promise
      return new Promise((resolve) => {
        resolve();
      });
    });

    // 从文档可知, emit 是 AsyncSeriesHook, 也就是异步串行钩子，特点就是异步任务顺序执行
    compiler.hooks.emit.tapAsync("TestPlugin", (compilation, callback) => {
      setTimeout(() => {
        console.log("compiler.emit() 111");
        callback();
      }, 3000);
    });

    compiler.hooks.emit.tapAsync("TestPlugin", (compilation, callback) => {
      setTimeout(() => {
        console.log("compiler.emit() 222");
        callback();
      }, 2000);
    });

    compiler.hooks.emit.tapAsync("TestPlugin", (compilation, callback) => {
      setTimeout(() => {
        console.log("compiler.emit() 333");
        callback();
      }, 1000);
    });
  }
}

module.exports = TestPlugin;
```

    其中`make`表示并行运行,`emit`表示串行运行,上述示例输出的顺序是

```powershell
compiler.compile()
compiler.make() 333
compiler.make() 222
compiler.make() 111
compiler.emit() 111
compiler.emit() 222
compiler.emit() 333
```
