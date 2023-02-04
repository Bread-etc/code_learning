# Node.Js

## Chapter 1

### 1. 初识Node.js

#### 1.1 Node.js是什么

- Node.js is a JavaScript runtime built on [Chrome's V8 JavaScript engine](https://developers.google.com/v8/).
  
  - Node.js不是一门语言
  - Node.js不是库(library)、不是框架(frame)
  - Node.js是一个JavaScript运行环境
  - <u>简单点来讲就是Node.js可以解析和执行JavaScript代码</u>
  - 以前只有浏览器可以解析和执行JavaScript代码
  - 也就是说现在的JavaScript可以完全脱落浏览器来运行，一切都归功于：Node.js
  - 构建于chrome的v8引擎之上

#### 1.2 node的API参考文档的使用：

- 黑点后面代表参数，例：
  
  - request <http.IncomingMessage> 如果是方法它会告诉你方法的参数是什么，是否可选的。
  
      上述`request`代表绑定函数的参数，因为它是一个事件因此肯定需要绑定一个函数，后面的连接为这个参数的类型，功能上能够调用什么变量和方法。

### 2.使用Node.Js

#### 2.1. 安装Node

- 查看当前Node环境的版本号
- 官网下载，LTS(长期支持版)稳定版，Current体验版
- 安装，一直下一步
- 查看，打开命令行 cmd，输入`node --version` 或者`node -v` 查看版本
- 环境变量

#### 2.2 node练习

##### fs 读写模块

- 使用`fs`模块
  
  ```javascript
  const fs = require('fs');
  ```

- 读取文件
  
  ```javascript
  fs.readFile('路径','可选编码',function(error, data){});//具体查看代码
  ```

- readFile方法的第二个参数为可选参数，规定解析文件的编码，可选择`utf-8`编码，让他以网页原文件的形式读取，不是二进制数据

- 写入文件
  
  ```javascript
  fs.writeFile('file',data[,options],callback);
  ```

> Node.js中常常使用回调函数作为参数，因为是异步的.

##### path 路径模块

- 使用`path`路径模块

```javascript
const path = require('path')
```

- `path.join( )`语法格式

```javascript
// ../会抵消前面的路径
const pathStr = path.join('/a','/b/c','../','./d','e')
console.log(pathStr);   //输出\a\b\d\e

fs.readFile(path.join(__dirname,'/file/1.txt'),'utf-8',function(err,dataStr){
    if (err) {
        return console.log(err.message);
    }
    console.log(dataStr);
})
```

path <string> 路径片段序列

- `path.basename( ) `语法格式

```javascript
path.basename(path[,ext])
```

参数解读: 

    1.path<string> 必选参数,表示一个路径的字符串

    2.ext<string> 可选参数,表示文件扩展名

    3.返回: <string> 表示路径中的最后一部分

- `path.extname( )`语法格式

```javascript
path.extname(path)    
```

参数解读: path表示文件字符串,返回文件扩展名

##### HTTP模块

- 使用`http`模块

```javascript
const http = require('http')
```

- 创建 web 服务器实例

```javascript
const server = http.createServer()    //调用http.createServer()快速创建
```

- 为服务器实例绑定 `request` 事件

       一般用于监听客户端发送过来的网络请求

```javascript
server.on('request',(req,res)=>{
    console.log('Someone visit our web server')
})
```

- 启动服务器

```javascript
server.listen(80,()=>{    //(端口号,callback回调函数)
    console.log('http server running at http://127.0.0.1')
})
```

    服务器和web页面一般使用字符串进行通信，因此json的传递需要转化为字符串

> 响应内容只能是字符串或二进制数据（Buffer）。对象、数字、数组、布尔值都不行，**因此`response.end()`方法能够响应字符串和二进制数据**

### 3. NodeJs中的JavaScript

#### 3.1 Node中使用模块

- ECMAScript
  - 没有DOM、BOM
- 核心模块
- 第三方模块
- 用户自定义模块

#### 3.2 核心模块

- Node为JavaScript提供了很多服务器级别的API，这些API绝大多数都被包装到了一个具名的核心模块中了。
  
  - 例如文件操作的`fs`核心模块，http服务构建`http`模块，`path`路劲操作模块、`os`操作系统信息模块。
    
    ```javascript
    const fs = require('fs');
    const http = require('http');
    ```

- 模块的好处：可以完全避免变量命名冲突污染的问题

- 模块之间的通信需要使用模块中的`exports`对象

- 所有文件操作的API都是异步的，即核心模块fs的方法。执行顺序可能要放到其他执行过程的后边执行

#### 3.3 用户自定义模块

​ Node.js不能同时执行多个js文件，需要使用模块化编程

- require
  
  - require是一个方法，作用是用来加载模块的
  - 在Node中，模块有三种
    - 具名的核心模块，例如fs、http
    - 用户自己编写的模块（就是一个js文件），可以使用require方法加载。相对路径必须加`./`，否则会报错，因为会将模块认定为一个核心模块
  
  require方法有两个作用：
  
  1. `加载文件模块并执行里面的代码`
  2. 拿到被加载文件模块导出的接口对象

- exports
  
  - exports用来实现不同模块之间的通信
  - 每个模块中都提供了一个exports对象，默认是一个`空对象`
  - 该对象类似于原型链中的prototype对象，能够向对象中添加数据，另一个模块可以通过`require`方法返回的exports对象使用这些添加的数据

>  module.exports和exports指向的是`同一空对象`,但以前者为准(module.exports)
> 
> 要么只使用module.exports或者只使用exports

### 4. web服务器开发

#### 4.1端口的概念

计算机中只有一个物理网卡，而且同一个局域网中，网卡的地址必须是唯一的。

- 端口号用来定位具体的应用程序（所有需要联网通信的软件都必须具有端口号），通信的软件占用

- 端口号的范围从0~65536之间

- 在计算机中有一些默认的端口号，最好不要去占用
  
  - 例如http服务的80

- 可以同时开启多个服务，但一定要确保不同服务占用的端口号不一致。同一个端口号只能被一个程序占用

- 目前发现，node的网络编程主要是通过request和response两个对象来实现的。一般就是监听request事件来获取客户端和服务端的数据。

- request代表着客户端的信息，response代表着服务器的信息
  
  开启服务器流程

```javascript
const http = require('http')
//1.  创建server
const server = http.createServer()
//2.  监听Server的request请求事件，设置请求处理函数
server.on('request',function(req,res){
    console.log(req.url)
})
// 3.绑定端口号，启动服务
server.listen(8080, function(){
    console.log('server running at http://127.0.0.1:8080');
})
```

#### 4.2 Content-Type

- 在服务端默认发送的数据，其实都是utf-8编码的内容  
  浏览器在不知道服务器响应内容的编码情况下会按照`当前操作系统的默认编码`去解析
- 在http协议中，Content-Type就是用来告知对方我给你发送的数据内容是什么类型，响应和请求都可以设置
  - Content-Type的值：
    - `text/plain`为普通文本，浏览器不会解析
    - `text/html`为html文本，浏览器会当做html文件解析该文本
    - 图片不需要指定编码，编码一般指定的是

#### 5. 注意事项

- node不管什么模块，涉及到路径的都是从app所在的目录为起点的

## Chapter 2

### 1. 目录斜杠问题

- 字符串目录一般采用斜杠`/`，也可以使用`\\`，`\`反斜杠的作用是用来转义
- Windows的目录采用了`\`来显示目录，这点与字符串和网址的url相反

### 2. Node 安装第三方库

- 在需要安装的目录下 输入npm install +包名，安装到执行目录中新建的node_modules目录下
- 一般安装在与服务器代码同级目录下
- node_modules目录不能够修改

### 3. 服务器端渲染

- 浏览器端的渲染在script标签中进行，服务端的渲染在服务器端进行，服务器渲染是可以减少请求，提升页面性能的，在实际操作时视情况选用
- 客户端渲染不利于SEO（爬虫查找）搜索引擎优化，例如ajax等异步渲染（想想全部刷新的页面一般都是服务器端渲染）
- 服务端渲染是可以被爬虫抓取到的，客户端异步渲染是很难被爬虫抓取到的
- 真正的网站是异步和服务端渲染相结合的
- 服务器渲染一般会刷新整个页面，页面源码会看到呈现的文字（方便SEO）；客户端渲染一般不会刷新页面。
- 服务端和浏览器端使用模板引擎有本质区别，浏览器端是将要解析的模板放在script标签找中的，服务器是将整个web页面当做要解析的字符串，因此服务器端使用模板引擎会在没有解析时暴露源码内容，浏览器端使用模板引擎则不会出现这种情况

### 4. 浏览器解析静态资源

- js的执行会阻塞DOM树的解析和渲染

- css的加载不会阻塞DOM树的解析，会阻塞DOM树的渲染

- 浏览器收到HTML响应内容之后，会开始从上到下一次解析
  
  当在解析的过程中，如果发现：
  
  ​ link  script  img​  iframe​  video​  audio
  
  等带有src或者是href（a标签除外）属性的标签（具有外链的资源标签）的时候，浏览器会自动对这些资源发起新的请求

### 5. 开发文件规范问题（处理网站中的静态资源）

- 为了让目录结构保持统一清晰，约定把所有的HTML文件都放在views目录下
- 为了方便的统一处理静态资源（css,img,js,第三方资源），我们约定把所有的静态资源都存放在public目录下
  - css目录中存放css文件
  - img目录下存放图片文件
  - js目录中存放js文件
  - lib目录下存放第三方资源
- 使用代码控制，可以控制用户访问哪些资源。即可以访问index.html和public目录中的资源
- 客户端的请求路径不再使用文件路径了（相对路径），web页面的请求url一般使用`/`（根目录）的url地址来引用资源。服务端代码的路径还是相对路径
- 浏览器在真正发送请求的时候会自动把 http://127.0.0.1:3000 拼上，所以在浏览器显示的请求路径一律写成根路径形式，例如`/public`，这种形式叫做url路径

### 6. url模块

- url模块能够用于 URL 处理与解析
  - `url.parse()`方法能够解析请求网址，返回一个包含各个请求数据的对象。记住第二个参数设置为`true`
  - 该方法返回的路径也为端口号后面的路径

### 7. 服务器让客户端重定向

- 如何通过服务器让客户端重定向，重定向就是为了减少代码的书写
  
  1. 状态码设置为302临时重定向（301为永久重定向）
     
     ​ 通过`res.statusCode`
  
  2. 在响应头中通过Location告诉客户端往哪重定向
     
     ​ 通过`res.setHeader`
  
  3. 设置完重定向之后一定要关闭请求，res.end()

```javascript
res.statusCode = 302
res.setHeader('Location', '/')
res.end()​
```

  如果客户端发现收到服务器的响应的状态码是302，就会自动去响应头中找Location，然后对该地址发起新的请求。所有就可以看到客户端自动跳转

- 301永久重定向 浏览器会记住
  
  - 比如访问a.com 会跳转至b.com 下次浏览器不在向a.com发请求会直接跳转至b.com

- 302 临时重定向 浏览器不会记忆
  
  - a.com临时，下次浏览器还会请求a.com 然后告诉浏览器访问b.com。每次请求都尝试一下

## Chapter 3

### 知识点

- 模块系统
  - 核心模块
  - 第三方模块
  - 自定义模块
  - 加载规则以及加载机制
  - 循环加载的问题
- npm
- package.json
- Express
  - 第三方web开发框架
  - 高度封装了http模块
  - 增删改查
  - 使用文件来保存数据（锻炼异步编码），遇到业务能够自己封装
- MongoDB（数据库）

### 软件版本

- 涉及到软件工程知识
- x.x.x
  - 第一个数字代表`大版本`，新增功能比较多，甚至可能去除了某些功能
  - 第二个数字：`功能版本`
  - 第三个数字：修复bug，提升了性能

### node和PHP

- php是文件型的
- node定制性更强
- node比较底层，很多功能需要自己实现。
- 在Node中开启的服务器，默认是黑盒子，所有资源都不允许用户来访问，用户可以访问哪些资源具体有开发人员编写设计的代码为准。可以处理url，php则会显示index.html

### 1.Node中的模块系统

使用node编写应用程序主要就是在使用：

- ECMAScript语言
- 核心模块
  - 文件操作的fs
  - http服务的http
  - url路径操作系统
  - path路径处理模块
  - os操作系统信息
- 第三方模块
  - art-template
  - 必须通过npm来下载可以使用
- 自定义模块

#### 1.2 CommonJs模块规范

在Node中的js还有一个重要的概念：模块系统

- 模块作用域
- 使用require方法用来加载模块
- 使用exports接口对象用来导出模块中的成员

如果一个模块需要直接导出某个成员，而非挂载的方式，需要使用下面的方式

```javascript
module.exports = 'hello'//这样另一个模块加载这个模块就会返回'hello'，而不是exports对象
```

##### 1.2.1 加载 `require`

```javascript
const 自定义变量名 = require('模块')
```

- 执行被加载模块中的代码
- 得到被加载模块中的`exports`导出接口对象

##### 1.2.2 导出`exports`

- Node中是模块作用域，默认文件中所有的成员只在当前文件模块中有效
- 对于希望可以被其他模块访问的成员，我们就需要把这些公开的成员都挂载到`exports`接口对象中

导出多个成员（必须在对象中）：

```javascript
exports.a = 123
exports.b = {
    foo : 123
}
```

导出单个成员(拿到的就是：函数、字符串)：

```javascript
module.exports = 'hello'
module.exports = function(){//后者会覆盖前者
    //内容
}
```

因此，也可以如下方式导出多个成员：

```javascript
module.exports = {
    //加成员
}
```

##### 1.2.3 原理解析

- 每一个模块都有一个自己的`module`对象
- module对象中，有一个成员叫：`exports`成员也是一个对象
- 也就是说需要对外导出成员，只需要把导出的成员挂载到`module.exports`对象中

```javascript
const module = {
    exports : {

    }
}
//require这个模块，就会得到 module.exports
//为了简化操作，在模块中还有一句代码
const exports = module.exports  //exports和module.exports挂载是一样的。这行代码应该是在第一行
console.log(exports === module.exports)//结果为true
return module.exports
```

##### 1.2.4 require方法加载规则

- 优先从缓存加载

- 不会重复加载相同的模块

- 可以拿到其中的接口对象，不会重复执行模块中的代码

- 这样做是为了避免重复加载模块，提高加载效率

- require的参数为模块标识

- 加载时要判断模块标识
  
  1. 核心模块    (优先级最高)
  2. 第三方模块
     - 凡是第三方模块都可以通过`npm`来下载
     - 使用的时候可以同require('包名')的方式进行加载才可以使用（非路径形式）
     - 查找规则（以art-template模块为例）
       1. 先找到当前文件所处目录中的node_modules目录（这个目录就是用来存放第三方模块的）
       2. 找node_modules/art-template
       3. 找node_modules/art-template/package.json 文件
       4. 找node_modules/art-template/package.json文件中的“main”属性
       5. mian属性中就记录了art-template的入口模块
       6. 然后加载使用这个第三方包，实际上最后加载的还是文件
       7. 如果package.json文件不存在或者main指定的入口模块也没有，则node会自动找该目录下的index.js作为默认备选项
       8. 若果以上所有任何一个条件都不成立，则会进入上一级目录中的node_modules目录查找，规则如上
       9. 如果上一级还没有，则继续往上上一级查找，知道磁盘根目录还找不到，最后报错：`can not find module xxx`
  3. 自定义模块
  
  注意：
  
  - 路径形式的模块采用`./`，当前目录不可省略，`../``上一节目录不可省略
  - 核心模块的加载按名字来加载，核心模块的本质也是文件。核心模块的文件已经被编译到二进制文件中了，我们只需要按照名字来加载就可以了
  - 一个项目有且只有一个node_modules，不会出现多个node_modules。该目录放在项目同目录（项目根目录）中，这样的话项目中的代码都可以加载到第三方包。

#### 1.3 npm

- node package manager，是一种命令行工具
- npm install art-template jquery bootstrap（可以一次性装几个包）
- install 也可以用 `i`代替

##### 1.3.1 npm网站

> npmjs.com npm官方网站，可以查询包和发包

##### 1.3.2 npm命令行工具

- npm查看版本输入：

```javascript
npm --version 
npm -v
```

- npm升级

```javascript
npm install --global npm//自己升级自己
```

- npm初始化

```javascript
npm init
npm init -y//可以跳过向导，快速生成
```

- npm安装指定的版本
  - npm i bootstrap`@版本号` （中间没有空格）
- npm install --save 包名
  - 下载并保存依赖项（package.json文件中的dependencies选项）
  - npm i -S 包名
- npm uninstall 包名
  - 只删除，如果有依赖项会依然保存
  - npm un 包名
- npm uninstall --save 包名
  - 删除的同时也会把依赖信息也删除
  - npm un -S 包名
- npm help
  - 查看使用帮助
- npm 命令 --help
  - 查看指定命令的帮助
- --global 为全局安装，安装到node的安装目录下，一般用于影响操作的软件更新或替换
- npm install 包名 --save-dev 这样安装的包和版本号就会存在 package.json 的 devDependencies 这个里面，--save 会会存在 devDependencies 里面。简写为 -D.`devDependencies`中一般存放上线后用不到的包名(开发时候用得到)

> 注意 : 可以通过package.json中的包信息,使用 `npm install` 一次性下载所有包

#### 1.4 package.json

- package.json，包描述文件，包的说明书，说明包引用了什么依赖
- npm install art-template --save（放到包后面）或者npm install --save art-template （放到包前面，前后都可以），package.json文件中多了一个属性`"dependencies"`，该属性的值的对象中保存着项目的依赖（依赖什么插件、插件的版本）
- 建议每一个项目都有一个package.json文件，就像`说明书`
- package.json文件可以通过`npm init`的方式自动初始化出来，`npm init`会以向导的方式（问一句答一句），提问项如下（不填的直接回车）：
  - name：
  - version
  - description:（项目的描述）
  - entry point:(index.js) （项目的入口一般使用`main.js`作为入口，与json文件同目录）
  - test command:（测试命令）
  - git repository:（github地址）
  - keywords:
  - author:作者
  - license: (ISC)（许可证）
  - --save 第三方包会出现"dependencies"属性
- 目前最有用的是"dependencies"选项，包含第三方包的依赖信息
- 建议执行`npm install +第三方包名`都加上`--save`这个选项，用来保存（项目依赖信息）
- 如果node_modules删除了，只要"dependencies"选项中保存有依赖包，下次直接npm install来加载包即可

### 2. Express

- experss : 表达，快速的

- [http://expressjs.com/](http://expressjs.com/)

#### 2.1 Express 基本使用语法

##### 监听GET请求

通过`app.get()`方法,可以监听客户端的GET请求,具体的语法格式如下:

```javascript
//参数1:客户端请求的URL地址
//参数2:请求对应的处理函数
//    req:请求对象(包含了与请求相关的属性与方法)
//    res:响应对象(包含了与响应相关的属性与方法)
app.get('请求URL',function(req,res){
    //处理函数
)
```

##### 监听POST请求

通过`app.post()`方法,可以监听客户端的POST请求,具体的语法格式如下:

```javascript
//参数1:客户端请求的URL地址
//参数2:请求对应的处理函数
//    req:请求对象(包含了与请求相关的属性与方法)
//    res:响应对象(包含了与响应相关的属性与方法)
app.post('请求URL',function(req,res){
    //处理函数
)
```

##### 响应给客户端

```javascript
app.get('/user',(req,res)=>{
    //向客户端发送JSON对象
    res.send({name:'zs',age:20,gender:'male'})
})


app.post('/user',(req,res)=>{
    //向客户端发送文本内容
    res.send('请求成功')
})
```

##### 完整web服务器搭建流程

```javascript
//1.导入 express
const express = require('express')
//2.创建web服务器
const app = express()

//4.监听客户端的GET和POST请求,并向客户端响应具体的内容
app.get('/user',(req,res)=>{
    //调用express提供的res.send(),向客户端响应一个JSON对象
    res.send({name:'zs',age:20,gender:'male'})
})
app.post('/user',(req,res)=>{
    //调用express提供的res.send(),向客户端响应一个文本字符串
    res.send('请求成功')
})

//3.启动web服务器
app.listen(80,()=>{
    console.log('express server running at http://127.0.0.1');
}) 
```

- express找不到的路径，会返回Cannot GET /a (比如说‘a’) 页面，同时返回状态码404
- express的get方法判断的路径为纯路径，不带传递的参数和锚等内容
- app.get()方法会返回app对象，可以链式编程，继续执行get方法

#### 2.2 Express 中request的两个参数

`query`参数

```javascript
app.get('/',(req,res)=>{
    //通过req.query可以获取到客户端发送过来的查询参数
    //默认是一个空对象
    res.send(req.query)
})
```

- request对象的其他属性和方法可以查看文档 [Node.js Express 框架 | 菜鸟教程](http://www.runoob.com/nodejs/nodejs-express-framework.html)

`params`参数

```JavaScript
//此处:id和:username为动态参数,可以有多个动态参数
app.get('/user:id/:username',(req,res)=>{
    res.send(req.params)
})
```

#### 2.3 Express公开指定的目录（处理静态资源）

`express.static()`创建一个静态资源服务器

```javascript
//托管单个静态资源目录
app.use(express.statiic('./public'))
//挂载路径前缀
app.use('/public/', express.static('./public/'))
```

- 不公开目录默认找不到路径处理。
- 上述代码公开的是public文件夹下的文件，因此次用`‘/public/’`的形式，也可以使用`/public`，它只是在检测url是否以你定义的开头
- 浏览器的访问路径只有在端口号后没有'/'的时候补一个‘/’，浏览器在文件路径后面补一个‘/’，其他的路径都是在浏览器地址栏看到的路径，没有区别。

总结：

- get方法是用来判断访问路径，作出处理的
- use方法是用来开发静态资源的。
- 浏览器路径确认是文件，会在后面自动拼接一个`/`

## Chapter 4

### 知识点

- express
- 基于文件做一套 CRUD
  - C - Creation 增加  
    R - Retrieve 查询  
    U - Update 修改  
    D - DELETE 删除

### 文件路径和模块标识

- 文件路径`/`为磁盘根目录
- 模块标识的`./`中的`.`不能省去

### 1. 修改完代码自动重启

​ 使用一个第三方命令行工具：`nodemon`来帮助我们解决频繁修改代码重启服务器问题

​ `nodemon`是一个基于Node.js开发的一个第三方命令行工具

```shell
npm install --global nodemon
```

安装完毕之后，使用：

```shell
node app.js
# 使用 nodemon启动app
npx nodemon app.js
```

>     只要通过`nodemon app.js`启动服务，则他会自动监视你的文件变化。当文件发生变化的时候，自动帮你重启服务器。

### 2. express 基本路由（router）

路由（routing）是指分组从源到目的地时，决定端到端路径的网络范围的进程。

- 路由其实就是一张表。我们已经了解了 HTTP 请求的基本应用，而路由决定了由谁(指定脚本)去响应客户端请求。
- 这个表里面有具体的映射关系
  - 当请求'/'，执行什么语句，分配什么
- express 中的get方法就相当于一个路由，以什么路径执行的时候，执行对应的处理函数，就是一个路由处理

路由器（映射关系）:

- 请求方法 METHOD(PATH,HANDLER)
- 请求路径 PATH
- 请求处理函数 HANDLER
- 同一路径使用不同的请求方式，就可以让同一个路径被多次使用

#### 路由的使用

`创建`路由模块

```javascript
//这是路由模块
//1.导入express
const express = require('express')
//2.创建路由对象
const router = express.Router()
//3.挂载具体的路由
router.get('/user/list',(req,res)=>{
    res.send('Get user list.')
})
router.post('/user/add',(req,res)=>{
    res.send('Add new user')
})
//4.向外导出路由对象
module.exports = router
```

`注册`路由模块

```javascript
//1.导入路由模块
const userRouter = require('./router/user.js')
//2.使用app.use()注册路由模块
app.use(userRouter)
//挂载静态资源统一添加前缀
app.use('./api',userRouter)
```

>     其中app.use()函数的作用是来注册全局中间件

### 3. express静态服务

1. 当以`/public/`开头的时候，添加`.`去`./public/`目录中寻找对应的资源

```javascript
app.use('/public/', express.static('./public/'))
```

2. 当省略第一个参数的时候，则可以通过省略 /public 的方式来访问。直接写该目录下的资源路径即可，例如public目录下的index.html，url为 127.0.0.1:3000/inde.html

```javascript
app.use(express.static('./public/'))
```

3. 当指定第一个参数的时候，必须是/a/为开头取代 /public/ 访问public中的资源，是为了方便指定定制访问路径。例：1207.0.0.1:3000/a/index.html 才能访问如下的开放目录。[统一添加访问前缀]

```javascript
app.use('/a/', express.static('./public/'))
```

### 4. Express 中间件

- 本质上是一个函数

- `next()`是把流转关系转交给下一个中间件和路由的函数
1. 定义中间件函数 `(应用程序级别)`

```javascript
//定义最简中间件函数  不关心请求路径
const mw = function(req,res,next){
    console.log('最简中间件函数');
    next()
}
```

2. 全局生效的中间件

客户端发起的任何请求,到达服务器之后,都会触发的中间件

```javascript
//全局中间件
app.use(mw)
//最简形式
app.use(function(req,res,next){
    console.log('中间件函数最简方式')
    next()
})
```

3. 局部生效的中间件 `路由级别`

```javascript
//单个局部中间件
app.get('/',mw1,(req,res)=>{
    res.send('Home page')
})
//多个局部中间件
app.get('/',[mw1,mw2],(req,res)=>{
    res.send('Home page')
})
```

4. 中间件的注意事项
- 在路由之前注册中间件

- 客户端的请求可以连续调用多个中间件处理

- 不要忘记next()函数

- 调用完next()后不要再写其他代码

- 多个中间件之间,共享req和res对象
5. 错误级别中间件

必须注册在`所有路由之后`,可以防止报错导致的崩溃,没有next()

```javascript
app.use(function(err,req,res,next){
    console.log(err.message)
    res.send('Error!',err.message)
})
```

6. express内置中间件

```javascript
app.use(express.static())    //快速托管
app.use(express.json())    //解析JSON
app.use(express.urlencoded({ extended: false }))    //解析URLencoded请求
```

> 可以使用req.body来接收客户端发送过来的请求体数据

7. 自定义中间件

```javascript
const express = require('express')
const qs = express('querystring')
const app = express()
//这是解析表单数据的中间件
app.use((req,res,next)=>{
    //定义中间件具体的业务逻辑
    //1.定义一个str字符串,专门用于存储客户端发送过来的数据
    let str = ''
    //2.监听req的data事件
    req.on('data',(chunk)=>{
        str += chunk
    })
    //3.监听req的end事件
    req.on('end',()=>{
        //在str中存放的是完整的请求体数据
        // console.log(str);
        //TODO:把字符串格式的请求体数据,解析成对象格式
        const body = qs.parse(str)
        req.body = body
        next()
    })
})
app.post('/user',(req,res)=>{
    res.send('ok')
})
app.listen(80,()=>{
    console.log('http://127.0.0.1');
})
```

### 5. 使用Express 写接口

1. 填写`GET`接口

```javascript
//在这挂载对应的路由
router.get('/get',(req,res)=>{
    //通过req.query 获取客户单通过查询字符串,发送到服务器的数据
    const query = req.query
    res.send({
        status:0,   //0表示成功 1表示失败
        msg:'GET请求成功',
        data:query  //响应给客户端的数据
    })
})
```

2. 填写`POST`接口

```javascript
router.post('/post',(req,res)=>{
    //通过 req.body 获取请求体中包含的 url-encoded 格式的数据
    const body = req.body
    res.send({
        status:0,   //0表示成功 1表示失败
        msg:'POST请求成功',
        data:body  //响应给客户端的数据
    })
})
```

3. `JSONP`接口

为了防止冲突,必须在配置CORS中间件之前声明JSONP的接口,否则JSONP接口会被处理成开启了CORS的接口

```javascript
app.get('/api/jsonp',(req,res)=>{
    //定义JSONP接口具体的实现过程
    ...
    //响应给客户端的<script>标签进行解析
    res.send(scriptStr)
})
```

### 6. CORS跨域问题

安装与配置cors中间件

```shell
npm install cors
```

CORS 响应头部 -`Access-Control-Allow-Origin`

```javascript
res.setHeader('Access-Control-Allow-Origin','http://xxx.com')
// * 表示通配符,表示所有网页都能访问该服务器
res.setHeader('Access-Control-Allow-Origin','*')
```

CORS 响应头部 - `Access-Control-Allow-Headers`

```javascript
//允许客户端额外想服务器发送 Content-Type 请求头等
res.setHeader('Access-Control-Allow-Headers','Content-Type','x-Custom-Header')
```

CORS 响应头部 - `Access-Control-Allow-Methods`

```javascript
//允许所有的HTTP请求方法
res,setHeader('Access-Control-Allow-Methods','*')
//只允许POST/GET/DELETE/HEAD请求方法
res,setHeader('Access-Control-Allow-Methods','POST','GET','DELETE','HEAD')
```

### 7. 在express中配置使用`art-template`模板引擎

- [art-template](https://aui.github.io/art-template/zh-cn/docs/)

安装

```shell
npm install --save art-template
npm install --save express-art-template
```

配置：

```javascript
app.engine('art', require('express-art-template'))
// 第一个参数标识，当前以 .art 结尾的文件的时候，使用art-template模板引擎
// 也可以把第一个参数改写为html
app.engine('html', require('express-art-template'))
```

使用：

```javascript
// Express 为 Response 响应对象提供了一个方法 ： render
// render 方法默认是不可以使用的，但是如果配置了模板引擎就可以使用了
// res.render('html模板名', {模板数据})
// render方法中第一个参数不能写路径，默认会去项目中的 views 目录查找该模板文件
// 也就是说 Express 有一个约定：开发人员把所有的视图文件都放到 views 目录中 
// Express 默认查找目录就是 views 目录
// 呈现视图并将呈现的HTML字符串发送到客户端
app.get('/', function (req, res) {
    res.render('404.art')
})

// 如果想要修改默认的views 为别的目录
// app.set('views', 'render函数的默认路径')
app.set('views', './views')

// 查找views 目录下的文件
app.get('/admin', function (req, res) {
    res.render('admin/index.html', {
        title : '管理系统'
    })
})
```

当需要直接返回页面的时候，也可以使用render方法

```javascript
//当需要返回路径的时候
app.get('/', function (req, res) {
    res.render('index.html')
})
```

### 8. express 中重定向的使用

```javascript
//原生的重定向
res.statusCode = 302
res.setHeader('Location', '/')
//express 中封装的重定向
res.redirect('/')
```

### 9. express 中设置状态码

- express的响应对象`response`中有一个方法`status()`可以通过传递参数来设置响应码，该方法还可以链式编程，

```javascript
res.status(500).send('hello')
```

> 数据库中的性别`gender`一般使用0或1代替

### 10. Express-crud 路由设计

- 路由设计就是请求不同的url，响应不同的结果
- 路由的设计：考虑请求方式、请求路径、请求参数。

| 请求方式 | 请求路径             | get请求 | post请求                     | 备注     |
| ---- | ---------------- | ----- | -------------------------- | ------ |
| GET  | /students        |       |                            | 渲染首页   |
| GET  | /students/new    |       |                            | 渲染新增页面 |
| POST | /students/new    |       | name、age、gender、hobbies    | 处理新增请求 |
| GET  | /students/edit   | id    |                            | 渲染编辑页面 |
| POST | /students/edit   |       | id、name、age、gender、hobbies | 处理编辑请求 |
| GET  | /students/delete | id    |                            | 处理删除请求 |

路由的文件采用模块化开发，需要单独写一个模块。

>     注意：配置模板引擎和body-parser 一定要在 app.use(router) 挂载路由之前，才能正常执行

### 11. 获取函数异步操作的结果（封装异步API）

- 获取一个函数中异步操作的结果，则必须通过回调函数来获取
- 回调函数的关键作用

例如：

```javascript
function fn (callback) {
    setTimeout(function () {
        var data = 'ddd'
        callback(data)
    }, 1000)
}
fn(function (data) {
    console.log(data)
})
```

    只要是获取函数中的异步函数的数据（函数中还有执行的函数），都可以采用上述方法。通过引用类型可以获取到异步操作的结果。（试一下对象行不行） 实际运行的过程中是上面的fn函数中代用了回调函数。

> 注意：callback的运行一定要写在函数的最后一行，要不然会阻碍后边代码的执行

## Chapter 5

### 1. 新版本的npm特性（npm5 以上）

1. 新版本的的npm不需要加 --save 命令
2. uninstall 不加--save 会删除依赖
3. 当安装包的时候，会自动创建或者是更新`package-lock.json`这个文件
4. `package-lock.json`文件中保存着所有依赖树（所有包）的信息，下载地址，删除node_modules文件之后能够根据该文件快速下载依赖。
   - 这样重新`npm install`的速度就会提升
5. 从文件来看有`lock`，是用来锁定版本的
   - 如果项目依赖了某第三方库的 1.1.1 版本，如果重新安装有可能升级这个库，而`package-lock.json`会锁住依赖的版本，不会出现升级造成项目不可用的情况

### 2. MySQL数据库

1. DataType数据类型
- int 整数

- varchar(len) 字符串

- tinyint(1) 布尔值
2. 字段的特殊标识
- PK (Primary Key) 主键,唯一标识

- NN (Not Null) 值不允许为空

- UQ (Unique) 值唯一

- AI (Auto Increment) 值自动增长
2. SQL中的语法格式

`select`语句

>     SQL语句中的关键字对大小写不敏感

```sql
SELECT * FROM 表名称      //*表示查询所有数据
SELECT 列名称 FROM 表名称
```

`insert`语句

>     结尾记得打分号

```sql
-- 语法解读:向指定的表中,插入如下几列数据,列的值通过 values 指定
-- 注意: 列和值要一一对应,多个列和多个值之间,使用英文的逗号分隔
insert into table_name (列1,列2) values (值1,值2)
```

`update`语句

```sql
-- 1. 用 update 指定要更新哪个表中的数据
-- 2. 用 set 指定列对应的新值
-- 3. 用 where 指定更新的条件
update 表名称 set 列名称 = 新值 where 列名称 = 某值
```

`delete`语句

```sql
-- 从指定的表中,根据where条件,删除对应的数据行
delete from table_name where list_name = value
```

>     如果不加where限定条件,很容易导致将整张表删除

- 其他关于SQL语句知识再详细学习,不做深入
3. 在`项目`中操作`MySQL`
- 安装并配置MySQL模块

```javascript
const mysql = require('mysql')
const db = mysql.createPool({
    host:'127.0.0.1',    //数据库的IP地址
    user:'root',         //登录数据库的账号
    password:'admin123', //登录数据库的密码
    database:'my_db_01'  //指定要操作哪个数据库
})
```

- 测试是否连接到数据库

```javascript
const mysql = require('mysql')
const db = mysql.createPool({
    host:'localhost',    //数据库的IP地址
    user:'root',         //登录数据库的账号
    password:'admin123', //登录数据库的密码
    database:'my_db_01'  //指定要操作哪个数据库
})

//测试MySQL模块能否正常工作
db.query('select 1',(err,results)=>{
    //mysql模块工作期间报错了
    if(err) return console.log(err.mes);
    //能够执行成功的SQL语句
    console.log(results);
})
```

- 使用MySQL模块进行查找/插入/更新/删除

```javascript
//查询users表中所有数据
const sqlStr = 'select * from users'
db.query(sqlStr,(err,results)=>{
    //查询数据失败
    if (err) {
        return console.log(err.message);
    }
    //查询数据成功,执行结果是一个数组
    console.log(results);
})
```

```javascript
//向users表中,新增一条数据,其中username的值为spider-man,password为pcc123
const user = {username:'Spider-man',password:'pcc123'}
//定义执行的SQL语句,使用 ? 占位符提供两个值个值
const sqlStr = 'insert into users (username,password) values (?,?)'
//执行SQL语句
db.query(sqlStr,[user.username,user.password],(err,results)=>{
    //执行SQL语句失败
    if(err) return console.log(err.message);
    //成功
    //可以通过 affectedRows 属性,来判断是否插入数据成功
    if (results.affectedRows ===1) {
        console.log('插入数据成功');
    }
})
```

>     插入数据的便捷方式: 'insert into users set ?'    [自动匹配]
> 
>     也无需填充数组了,只用填充user对象就行了

```javascript
//更新用户信息
const user = { id:2,username:'aaa',password:'000'}
//定义SQL语句
const sqlStr = 'update users set username=?,password=? where id=?'
//执行SQL语句
db.query(sqlStr,[user.username,user.password,user.id],(err,results)=>{
    if(err) return console.log(err.message);
    if (results.affectedRows === 1) {   
        console.log('更新成功');
    }    
})
```

>     执行update后返回一个对象,且可以使用简写形式: 'update user set ?' [自动匹配]
> 
>     也无需填充数组了,只用填充user对象就行了

```javascript
//删除客户数据
const sqlStr = 'delete from users where id=?'
db.query(sqlStr,5,(err,results)=>{
    if(err) return console.log(err.message);
    //返回结果为一个对象
    if (results.affectedRows === 1) { 
        console.log('删除成功');
    }
})
```

- `标记删除`

    防止用户不小心点到一次就删除了,仅设置为标记删除

```javascript
//标记删除
const sqlStr = 'update users set status=? where id=?'
db.query(sqlStr,[1,6],(err,results)=>{
    if(err) return console.log(err.message)
    if(results.affectedRows === 1){
        console.log('标记删除成功')
    }
})
```

### Chapter 6

#### 6.1 Web开发模式

- 基于服务端渲染的 web 开发模式 (多用于企业级网站)

- 基于前后端分离的 web 开发模式 (多用于后台管理项目)

#### 6.2 身份认证

不同开发模式下的身份认证

    对于服务端渲染和前后端分离这两种开发模式,分别有不同的身份认证方案

1. 服务端渲染推荐使用 `Session 认证机制`

2. 前后端分离推荐使用 `JWT 认证机制`

#### 6.3 Session 认证机制

1. HTTP协议的无状态性    (服务器不主动保留每次HTTP请求的状态)

2. 突破HTTP无状态的限制    (Cookie,一般不超过4KB)

       `Cookie的几大特性`:

- 自动发送未过期的cookie

- 域名独立

- 过期时限

- 4KB限制
3. Cookie不具有安全性

#### 6.4 在 Express 中使用 Session 认证

1. 安装并配置express-session

```javascript
npm install express-session
//1.导入 session 中间件
const session = require('express-session)
//2.配置 Session 中间件
app.use(session({
    secret:'keyboard cat',    //secret 属性的值可以为任意字符串
    resave: false,            //固定写法
    saveUninitialized: true    //固定写法
}))
```

2. 向 session 中`存数据`

```javascript
// 登录的 API 接口
app.post('/api/login', (req, res)=>{
  // 判断用户提交的登录信息是否正确
  if (req.body.username !== 'admin' || req.body.password !== '000000') {
    return res.send({ status: 1, msg: '登录失败' })
  }
  // TODO_02：请将登录成功后的用户信息，保存到 Session 中
  // 注意：只有成功配置了 express-session 这个中间件之后，才能够通过 req 点出来 session 这个属性
  req.session.user = req.body // 用户的信息
  req.session.islogin = true // 用户的登录状态

  res.send({ status: 0, msg: '登录成功' })
})
```

3. 从 session 中`取数据`

```javascript
// 获取用户姓名的接口
app.get('/api/username', (req, res)=>{
// TODO_03：请从 Session 中获取用户的名称，响应给客户端
  if (!req.session.islogin) {
    return res.send({ status: 1, msg: 'fail' })
  }
  res.send({
    status: 0,
    msg: 'success',
    username: req.session.user.username,
  })
})
```

4. `清空` session

    清空服务器保存的 session 信息 (退出登录)

```javascript
req.session.destory()
```

#### 6.5 JWT 认证机制

cookie不支持跨域认证,需要配合session实现;

JWT (JSON Web Token) 是目前最流行的跨域认证解决方案

    用户的信息通过 Token 字符串的形式,保存在客户端浏览器中,服务器通过Token认证

JWT 组成部分: Header.Payload.Signature

- Payload 部分才是真正的用户信息,它是用户信息经过加密之后生成的字符串

- Header和Signature 是安全性相关的部分,只是为了保证Token的安全性

JWT 收到后会被存储在 localStorage 或 sessionStorage中

```javascript
Authorization: Bearer <token>
```

#### 6.6 在Express中使用 JWT

1. 安装并配置 JWT

```javascript
npm install jsonwebtoken express-jwt
//导入包
const jwt = require('jsonwebtoken')
//导入用于将客户端发送过来的 JWT 字符串,解析还原成JSON对象的包
const expressJWT = require('express-jwt')
```

- jsonwebtoken 用于生成JWT字符串

- express-jwt 用于将JWT字符串解析还原成 JSON 对象
2. 定义 secret 密钥

防止JWT字符串被人破解

- 生成JWT字符串对用户信息加密

- 解析成JSON对象时,使用secret进行解密
3. `生成` JWT 字符串 

```javascript
  // 登录成功
  // TODO_03：在登录成功之后，调用 jwt.sign() 方法生成 JWT 字符串。并通过 token 属性发送给客户端
  // 参数1：用户的信息对象
  // 参数2：加密的秘钥
  // 参数3：配置对象，可以配置当前 token 的有效期
  // 记住：千万不要把密码加密到 token 字符中
  const tokenStr = jwt.sign({ username: userinfo.username }, secretKey, { expiresIn: '30s' })
  res.send({
    status: 200,
    message: '登录成功！',
    token: tokenStr, // 要发送给客户端的 token 字符串
  })
})
```

4. JWT 字符串还原成 JSON对象

```javascript
// TODO_04：注册将 JWT 字符串解析还原成 JSON 对象的中间件
// 注意：只要配置成功了 express-jwt 这个中间件，就可以把解析出来的用户信息，挂载到 req.user 属性上
app.use(expressJWT({ secret: secretKey }).unless({ path: [/^\/api\//] }))
```

### Chapter 7

### MongoDB

#### 1. 关系型数据库和非关系型数据库

- 所有的关系型数据库都需要通过`spl`语言来操作
- 所有的关系型数据库在操作之前都需要设计表结构
- 而且数据表还支持约束（保证数据的完整性）
  - 唯一的
  - 主键
  - 默认值
  - 非空
- 非关系型数据库非常灵活
- 有的非关系型数据库就是 key-value 对
- MongoDB 不需要设计表结构

### 2. 启动和关闭数据库

启动：

```shell
# mongodb 默认使用执行 mongod 命令所处盘符根目录下的 /data/db 作为自己的数据存储目录 
# 所以在第一次执行命令之前先自己手动新建一个 /data/db (在需要执行的盘符根目录下)
mongod
```

修改默认的数据存储目录:

```shell
mongod --dbpath=数据存储目录路径
```

连接：

```shell
# 默认连接本机的 MongoDB 服务
mongo
```

退出：

```shell
#输入 exit 退出连接
exit
```

#### 3. 基本命令

- `show dbs`
  - 查看数据库列表，查看显示所有数据库（查看有数据的数据库）
- `db`
  - 查看当前操作的数据库
- `use 数据库名称`
  - 切换到指定的数据库（如果没有回创建）
  - 例如新建数据库： use zbb
- 插入数据（不设计表结构，默认每个数据自带ID）
  - `db.students.insertOne({"name":"jack"})`相当于新建了一个students数据往里面加元素
- show collections
  - 可以查看当前表里的集合
- db.集合名.find()
  - 查看当前集合中的数据

### 4. 在Node中如何操作 MongoDB 数据库

#### 4.1 使用官方的`mongodb`包来操作

- 可以查看官方文档 github 搜索 mongodb node

#### 4.2 使用第三方 mongoose 来操作 MongoDB 数据库

第三方包：`mongoose`基于MongoDB 官方的 `mongodb`包再做的一次封装.

安装： npm i mongoose

开启mongodb 数据库，然后执行例程代码

```javascript
const mongoose = require('mongoose');
//连接MongoDB数据库，引号中的第一个为本机，第二个为本机的test数据库。
//指定连接的数据库不需要存在，当你插入第一条数据之后就会自动被创建出来
mongoose.connect('mongodb://localhost/test', { useMongoClient: true });
mongoose.Promise = global.Promise;
// 创建一个模型就是在设计数据库
const Cat = mongoose.model('Cat', { name: String });
// Cat 为表名（最终生成名称为 cats），第二个参数为数据结构

// 实例化一个Cat 
const kitty = new Cat({ name: '喵喵'});

// 持久化保存 kitty 实例
kitty.save(function (err) {
  if (err) {
    console.log(err);
  } else {
    console.log('meow');
  }
});
```

#### 4.3 MongoDB 数据库的基本概念

按开发顺序：

- 可以有多个数据库
- 一个数据库中可以有多个集合（表）
- 一个集合中可以有多个文档（表记录）
- 文档结构很灵活，没有任何限制
- MongoDB 非常灵活，不需要像 MySQL 一样先创建数据库、表、设计表结构
  - 在这里只需要：当你需要插入数据的时候，只需要指定往那个数据库的哪个集合操作就可以
  - 一切都有 MongoDB 来帮助自动完成建库建表这件事
- MongoDB 默认开启端口 27017

#### 4.4 mongoose 官方指南

1. 设计Schema 发布 model

- 可以在文档中的 `model.js` 中查看增删改查的方法

```js
// 此模块是为了新建一个数据库
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
// 连接数据库
mongoose.connect('mongodb://localhost/zbb')
// 相当于设计集合结构（表结构）
// 字段名称就是表结构中的属性名称
// 每个属性的值做了强制要求，都为js代码
// 约束的目的是为了保证数据的完整性，不要有脏数据
// var blogSchema = new Schema({
//   title:  String,
//   author: String,
//   body:   String,
//   comments: [{ body: String, date: Date }],
//   date: { type: Date, default: Date.now },
//   hidden: Boolean,
//   meta: {
//     votes: Number,
//     favs:  Number
//   }
// });

// 设计约束案例：
const userSchema = new Schema({
  username: {
    type: String,
    required: true //必须要有不能为空
  },
  password: {
    type: String,
    required: true
  },
  // email: {
  //   type: String
  // }
  email: String
})

// 3. 将文档结构发布为模型
// mongoose.model 方法就是用来将一个架构发布为 model
// 第一个参数：传入一个大写名词单数字符串来表示你的数据库名称
//              mongoose 会自动将大写名词的字符串生成小写复数的集合名称
//              User在数据库中会变成users 集合名称
//  第二个参数：架构 Schema    
//  返回值 ：模型构造函数       
// var Blog = mongoose.model('Blog', blogSchema)
const User = mongoose.model('User', userSchema)
// 4. 当我们游了模型构造函数之后就可以使用构造函数对users 集合中的数据操作了（增删改查）
```

2. `增加数据`

```javascript
// 当我们有了模型构造函数之后就可以使用构造函数对users 集合中的数据操作了（增删改查）
// new 模型构造函数 参数为对象
const admin = new User({
  username: 'admin',
  password: 'aaa1'
})

// 5. 数据持久化
// 使用 .save() 方法
admin.save(function (err, ret) {
  if (err) {
    console.log('保存失败');
  } else {
    console.log('保存成功');
    console.log(ret);  //就是刚刚保存的数据，id自动生成
  }
})
```

3. `查询`

查询所有：

```javascript
User.find(function (err, ret) {
  if(err) {
    console.log('查询失败')
  } else {
    console.log('查询成功');
    console.log(ret);
  }
})
```

按条件查询所有：

```javascript
User.find({
  username: 'zs'
}, function (err, ret) {
  if(err) {
    console.log('查询失败')
  } else {
    console.log(ret);
  }
})
```

按条件查询单个：

```javascript
User.findOne({
  username: 'zs'，
  password: '123'
}, function (err, ret) {
  if(err) {
    console.log('查询失败')
  } else {
    console.log(ret);
  }
})
```

4. `删除数据`

按条件删除：

```javascript
User.remove({
  username: 'zs'
}, function (err) {
  if (err) {
    console.log('删除失败')
  } else {
    console.log('删除成功')
    console.log(ret);
  }
})
```

根据条件删除一个：

```javascript
Model.findOneAndRemove(conditions, [options], [callback])
```

根据id删除一个：

```javascript
Model.findByIdAndRemove(id, [options], [callback])
```

5. `更新数据`

根据id更新一个：

```javascript
User.findByIdAndUpdate('数据的id', {
  更新的数据
  password: '1234'
}, function (err, ret) {
  if(err) {
    console.log('更新失败');
  } else {
    console.log('更新成功');
  }
})
```

> 剩余的更新方法查看文档。
