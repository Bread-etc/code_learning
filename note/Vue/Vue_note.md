#Vue

## Vue basic

Vue优点：
1.采用组件化，提高代码复用率，让代码更好维护
2.声明式编码，无需直接操作DOM，提高开发效率

---

### 1.2 初识Vue

生产环境不引入Vue.js    Vue.config (全局配置)
一个Vue实例只能对应一个容器

```javascript
//创建Vue实例
const x = new Vue({
    el:'#root',        //el用于指定当前Vue实例为哪个容器服务
                   //其中的#指id选择器,可以使用一个点来使用类选择器
    data:{
        //data中用于存储数据,数据供el所指定的容器去使用,
        name:'guangzhou';
    }
})
```

注意区分: js表达式 和 js代码(语句)
    1.表达式:一个表达式会生成一个值,可以放在任何一个需要值的地方
        (1).a
        (2).a+b
        (3)demo(1)
        (4)x === y ? 'a' : 'b'
    2.js代码(语句)
        (1)if语句
        (2)for语句

---

### 1.3 模板语法

v-bind绑定语法,简写一般为一个冒号(:)
功能:用于解析标签(包括:标签属性 标签体内容 绑定事件......)

```javascript
<body>
    <div id="root">
        <h1>hello</h1>
        <h2>名字1{{name}}</h2>
        <h2>名字2{{second.name}}</h2>
        <hr>
        <a v-bind:href="url">点我进百度1</a>
        <a :href="url">点我进百度2</a>
        <!--v-bind可以用冒号:简写-->
    </div>
</body>
<script>
    //插值语法通常是指定标签体内容
    //指令语法通常用于解析标签，包括标签属性，标签体内容，绑定事件
    //指令语法格式通常是v-???
    new Vue({
        el:'#root',
        data:{
            url:'http://www.baidu.com',
            name:'jack',
            second:{
                name:'Tom'
            }
        }
    })
</script>
```

#### 数据绑定

单向数据绑定: v-bind
双向数据绑定: v-model    ==>  根据value值查找
简写形式不一样: v-bind => :xxx ;  v-model =>  :value
单向绑定在根上改变才会改变,但不能回头影响data;双向绑定可以相互影响改变
双向绑定一般用于表单元素上(input frame)

### 1.4 MVVM模型

1. M：模型(Model) ：对应 data 中的数据 
2. V：视图(View) ：模板
3. VM：视图模型(ViewModel) ： Vue 实例对象

![1673166054512](C:\Users\LENOVO~1\AppData\Local\Temp\1673166054512.png)

### 1.5 回顾数据代理

利用`Object.defineProperty`这一方法为对象添加属性
    利用该方法添加的方法可以遍历    (Object.keys()  返回一个键名的数组)

```javascript
let number = 30
let person = {
    name:'jack',
    sex:'male',
}
//传入三种参数
Object.defineProperty(person,'age',{
    value:18,
    enumerable:true,    //返回属性为是否可以枚举，默认为false
    writable:true,      //控制属性是否可以被修改，默认为false
    configurable:true,  //控制属性是否可以被删除，默认值为false

//当有人读取person的age属性时，get函数(getter)会被调用，返回值返回到age中
    get(){
        console.log("有人读取age属性了")
        return number
    },

//当有人修改person的age属性时，set函数(setter)会被调用，且会收到修改的具体值
    set(value){
        console.log("有人修改age属性了,且值是:",value)
        number = value
    }
})
```

何为数据代理?    
    数据代理：通过一个对象代理对另一个对象中属性的操作 (读和写) 
![1673242713902](C:\Users\LENOVO~1\AppData\Local\Temp\1673242713902.png)
通过前面介绍到的Object.defineProperty来添加属性值到data上

### 1.6 事件处理  (methods)

利用v-on:xxx进行事件处理,或者是用@符号完成事件
    其中showinfo方法后面添加小括号表示传参($event,123)

```javascript
<body>
    <div id="root">
        <h2>欢迎来到{{city}}</h2>
        <button v-on:click="showInfo1">点我提示信息1(不传参)</button>
        <button @click="showInfo2($event,66)">点我提示信息2（传参）</button>
        <!-- 两种写法 -->
    </div>
</body>
<script>
    const vm = new Vue({
        el:'#root',
        data:{
            city:'Guangzhou'
        },
        methods: {
            showInfo1(event){
                console.log(event.target.innerText)
            },
            showInfo2(event,number){
                console.log(event,number)
            }
        },
    })
</script>
```

#### 事件修饰符

1. .prevent : 阻止事件的默认行为 event.preventDefault()  (常用)
2. .stop : 停止事件冒泡 event.stopPropagation()  (常用)
3. once : 事件只触发一次 (常用)
4. capture : 使用事件的捕获模式
5. self : 只有event.target是当前操作的元素才是触发事件
6. passive : 事件的默认行为立即执行,无需等待事件回调执行完毕

<img title="" src="file:///C:/Users/LENOVO~1/AppData/Local/Temp/1673263474762.png" alt="1673263474762" data-align="inline">

#### 键盘事件

Vue中常用的按键别名 :
    enter ==> 回车
    delete ==>  删除
    esc  ==>  退出
    space  ==>  空格
    up down left right  ==>  上下左右
    tab  ==>  换行  (特殊,必须配个keydown去使用)
其中tab可以使用keydown.tab来实现功能，相较其他keyup.xxx比较特殊系统修饰键 ctrl shift meta alt 较为特殊
    (1) 配合keyup使用或其他按键使用 ctrl + other keys 随后释放
    (2) 配合keydown使用，正常触发事件

### 1.7 计算属性  (computed)

计算属性中的`getter`和`setter`的this都指向vm
优势 : 与methods相比,`computed`内部有缓存机制(复用),效率更高,测试更方便

```javascript
computed:{
    fullName:{
    //get什么时候调用？1.初次读取fullname时 
                    2.所依赖的属性数据发生变化时
        get(){
            console.log('getter被调用了')
            return this.FirstName + '-' + this.LastName
        };
    //set什么时候调用，当fullname的参数被修改的时候
        set(value){
            console.log('setter被调用了')
        }
    }
}
```

1. 要显示的数据不存在，要通过计算得来。 
2. 在 computed 对象中定义计算属性。 
3. 在页面中使用{{方法名}}来显示计算的结果。 

computed的简写形式 : 

```javascript
computed:{
    fullName(){
        //仅仅调用,只有getter有反应  (只考虑读取)
        return this.FirstName + '-' + this.LastName
    }
}
```

### 1.8 监视属性  (watch)

使用`watch`配置项

1.当被监视的属性发生变化时,回调函数自动调用,进行相关操作

2.监视属性必须存在,才能进行监视!

3.监视的两种写法:

    (1) new Vue 时传入watch配置

    (2) 通过vm.$watch监视

```javascript
const vm = new Vue({
        el:'#root',
        data:{
            isHot:true
        },
        computed:{
            info(){
                return this.isHot ? 'hot' : 'cool'
            }
        },
        methods: {
            changeWeather(){
                this.isHot = !this.isHot
            }
        },
        watch:{
            isHot:{
                immediate:true,  //初始化时让handler调用一下

                //handler什么时候调用？当ishot发生改变的时候
                handler(newValue,OldValue){
                    console.log('isHot be changed',newValue,OldValue)
                }
            }
        }
    })

//第二种写法
vm.$watch('isHot',{
    immediate:true,
    handler(newValue,oldValue){
        console.log('isHot被修改了',newValue,oldValue)
}
})
```

#### 深度监视

监视多级结构中某个属性的变化

```javascript
'number.a':{
    handler(){
        console.log(123)
    }
}
```

若要监视多级结构中所有属性的变化 : 加入配置项deep:true

如果不需要immediate和deep配置项的时候可以简写

```javascript
//简写
isHot(newValue,oldValue){
    console.log(666)
}

vm.$watch('isHot',function(newValue,oldValue){
  console.log(123123)  
})    
```

computed和watch的小区别 : 

    计算属性中无法开启异步任务,但是watch属性中可以实现异步计算

>  箭头函数的this指向它被创建时的环境
> 
> 定时器的回调函数 Ajax的回调函数的this一般是指window,最好使用箭头函数来指向

两个重要的小原则:

    1.所有被Vue管理的函数,最好写成普通函数,这样的this指向才是vm 或 组件实例对象

    2.所有不被vue管理的函数(定时器的回调函数,Ajax回调函数,Promise的回调函数),最好写成箭头函数,这样的this指向才是vm 或 组件实例对象

### 1.9 绑定class样式

1. :class = 'xxx'  

2. 表达式是字符串 :'classA'  (适用于类名不确定,需要动态指定)

3. 表达式是对象 :{classA:isA,classB:isB}  (适用于样式个数确定,名字确定,但需要动态绑定)

4. 表达式是数组 :[classA:isA,classB:isB]  (适用于绑定的样式个数不确定,名字也不确定)

### 1.10 绑定style样式

1. :style="{ color: activeColor, fontSize: fontSize + 'px' }" 
2. 其中 activeColor/fontSize 是 data 属性

### 1.11 条件渲染

#### v-if 和 v-else

    对布尔值进行判断,不保留结构,适用于切换频率比较低的场景

#### v-show

    对布尔值进行判断,仍保留结构(DOM元素未被移除),但不展示效果

---

> 1. 如果需要频繁切换 v-show比较好
> 
> 2. 当条件不成立时,v-if的所有子节点不会解析

### 1.12 列表渲染

v-for指令

    1. 用于显示列表数据

    2. 语法 : v-for="(item,index) in xxx" :key="yyy"

    3. 可遍历 : 数组 对象 字符串 指定次数

#### key的作用与原理

    1. key是虚拟DOM对象的标识,当数据发生变化时,vue会根据新数据生成新的虚拟DOM

    2. 利用key进行新旧虚拟DOM对比,节省性能,增加复用性

    3. 用index做为key可能会引发的问题 (逆序添加,产生错误DOM更新)

    4. 最好使用唯一标识作为key

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-01-10-19-31-44-image.png)

#### 列表过滤

判断字符串中是否包含某个值 : indexOf(value)

```javascript
keyWord(val){
    this.filtPersons = this.person.filter(p)=>{
    return p.name.indexOf(val) !== -1
}
}

'abc'.indexOf('x')    //-1 只要不等于-1字符串中就包含指定字符
'abc'.indexOf('a')    //0
'abc'.indexOf('c')    //2
```

> filter 返回的是一个全新的数组,会覆盖掉老的数组;需要拿一个新的数组去接收
> 
>         需要使用return返回新的数组

#### sort中升序和降序问题

```javascript
let arr = [1,3,2,6,4,5]
arr.sort((a,b)=>{
    return a-b    //升序  ==> [1,2,3,4,5,6]
})

arr.sort((a,b)=>{
    return b-a    //降序  ==> [6,5,4,3,2,1]
})
```

其中a-b和b-a表示前减后和后减前,分别表示升序和降序

#### 数组更新检测

##### 变更方法 :

Vue 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

你可以打开控制台，然后对前面例子的 `items` 数组尝试调用变更方法。比如 `example1.items.push({ message: 'Baz' })`。

##### 替换数组 :

变更方法，顾名思义，会变更调用了这些方法的原始数组。相比之下，也有非变更方法，例如 `filter()`、`concat()` 和 `slice()`。它们不会变更原始数组，而**总是返回一个新数组**。当使用非变更方法时，可以用新数组替换旧数组：

```
example1.items = example1.items.filter(function (item) {  return item.message.match(/Foo/)})
```

你可能认为这将导致 Vue 丢弃现有 DOM 并重新渲染整个列表。幸运的是，事实并非如此。Vue 为了使得 DOM 元素得到最大范围的重用而实现了一些智能的启发式方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

---

1. vue会监视data中所有的层次的数据

2. 通过setter实现监视对象后追加的属性不做响应式，使用vue.set()的API就有响应式

3. 通过包裹数组更新元素的方法实现

4. 在Vue中修改数组某个元素使用以下方法

            pop()  push()  shift()  unshift()  splice()  sort()  reverse()

            或者使用vue.set()/vm.$set()   (但是这两种方法不能给根数据对象追加属性)

    5.在使用filter(),slice(),contact()这些方法的时候可以使用原来的数组进行替换操作，使得Vue可以识别实例对象

### 1.13 Vue.set()方法的使用

- 格式 : Vue.set(target,propertyName/index,value)

- **参数**：
  
  - `{Object | Array} target`
  - `{string | number} propertyName/index`
  - `{any} value`

- **返回值**：设置的值。

- **用法**：
  
  向响应式对象中添加一个 property，并确保这个新 property 同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新 property，因为 Vue 无法探测普通的新增 property (比如 `this.myObject.newProperty = 'hi'`)

> 注意对象不能是 Vue 实例，或者 Vue 实例的根数据对象。

### 1.14 收集表单数据

v-model得到的是value值，用户输入的就是value，选框需要自己配置

    1.没有配置value的属性，收集的是checked

    2.配置value属性：

        1.v-model为非数组，收集的是checked 是否勾选

        2.v-model的初始值是数组，收集的值就是value组成的数组

    3.v-model的三个修饰符

        lazy：失去焦点再收集数据,不进行敏感收集

        number：输入字符串转变为有效数字

        trim：输入首尾空格过滤

### 1.15 过滤器

> vue3已经移除过滤器filter,需要用到再详细学习

定义 : 对要显示的数据进行特定格式化后再显示 (使用与一些简单逻辑的处理)

语法 : 

    1.注册过滤器 : Vue.filter(name,callback) 或 new Vue{filters:{}}

    2. 使用过滤器 : {{ xxx | 过滤器名}} 或 v-bind:属性 = "xxx | 过滤器名"

备注 : 

    1.过滤器也可以接收额外参数,多个过滤器可以串联

    2.并没有改变原本的数据,是产生新的对应的数据

### 1.16 内置指令

        1. v-text : 更新元素的 textContent (向其所在的节点中渲染文本内容,替换掉原本的内容)

        2. v-html : 更新元素的 innerHTML

        3. v-if : 如果为 true, 当前标签才会输出到页面

        4. v-else: 如果为 false, 当前标签才会输出到页面

        5. v-show : 通过控制 display 样式来控制显示/隐藏

        6. v-for : 遍历数组/对象

        7. v-on : 绑定事件监听,` 一般简写为@`

        8. v-bind : 绑定解析表达式, 可以省略 v-bind

        9. v-model : 双向数据绑定

        10. v-cloak : 防止闪现, 与 css 配合: [v-cloak] { display: none }  `(配合网速慢展示问题)`

        11.v-once : 在初次动态渲染之后就成为静态内容了,可以用于`优化性能`

        12. v-pre : 跳过其所在节点的编译过程,跳过没有使用插值语法的结点，会`加快编译`

### 1.17 自定义指令

利用`directives`配置项 , 分局部指令和全局指令

其中this的指向是window

```javascript
const vm = new Vue({
        el:'#root',
        data:{
            n:1,
        },
        directives:{
            //big(element,binding) 元素，绑定值
            //big函数何时会被调用？
            //1.指令与元素成功绑定时。(一上来)
            //2.指令所在的模板被重新解析时

            //出现多个单词不能使用驼峰命名，要使用杠连接
            'big-number'(element,binding){
                console.log('big-number')
            },
            //可以不加冒号

            big(element,binding){
                console.log("big",this)     //此处的this为window
                element.innerText = binding.value * 10
            },
            fbind:{
                //指令与元素成功绑定时调用
                bind(element,binding){
                    console.log('bind')
                    element.value = binding.value
                },
                //指令所在元素被插入页面时调用
                inserted(element,binding){
                    console.log('inserted')
                    element.focus()
                },
                //指令所在的模板被重新解析时调用
                update(element,binding){
                    console.log('update')
                    element.value = binding.value
                }
            }
        }
    })
```

### 1.18 Vue生命周期

常用的生命周期钩子：

    1. `beforeCreate` : 初始化:生命周期 事件,但数据代理还未开始(无法访问vm中data)

    2.`created` : 初始化: 数据监测和数据代理等(可以访问vm中data和methods)

    3.`beforemounted` : 呈现未经编译的DOM结构,将虚拟DOM转为真实(对DOM操作无效)

    4.`mounted `: 发送Ajax请求，启动定时器，绑定自定义事件，订阅消息等

    5.`beforeupdate` : 数据是新的,但页面仍为旧页面

    6.`updated` : 页面和数据保持同步,数据是新的,页面也是新的(会发生新旧比较)

    7.`beforeDestroy `: 清除定时器、解绑自定义事件、取消订阅消息等 (data仍可用)

    8.`destroy` : 可以利用vm.$destroy()来销毁实例

![生命周期.png](C:\Users\Lenovo%20PC\Desktop\code\Vue\资料（含课件）\02_原理图\生命周期.png)

> 生命周期函数中的 this 是指向vm或组件实例对象

生命周期可以概括为四对: `创建` `挂载`  `更新`  `销毁`  

一般在挂载的时候(mounted)打开定时器,在销毁之前关闭定时器(beforeDestroy)

关于销毁Vue实例:

    1.销毁后借助Vue开发者工具看不到任何信息

    2.销毁后自定义事件会失效,但原生DOM事件依然有效

    3.一般不会在beforeDestroy操作数据,因为即便操作数据,也不会再触发更新流程了

### 2.组件化编程(Component)

#### 非单文件组件

    利用`Vue.extend()`定义子组件,使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。而且需要在vm中注册子组件,才能使用.

>     组件实例对象 data 必须为函数，目的是为了**防止多个组件实例对象之间共用一个 data ，产生数据污染**。 采用函数的形式， initData 时会将其作为工厂函数都会返回全新 data 对象

```javascript
//定义hello组件
    const hello = Vue.extend({
    //无需写入el配置项  
    template:`
        <div>
            <h2>你好，{{msg}}</h2>
        </div>`,
    //data必须使用函数式data(){}
        data() {
            return {
                msg:'Vue'
            }
        },
    })


    const app = Vue.extend({
        template:`
        <div>
            <school></school>
            <hello></hello>
        </div>
        `,
        //局部注册(注册组件)
        components:{
            school,
            hello
        }
    })
```

1.关于组件名:

     一个单词组成：

        第一种写法(首字母小写)：school

        第二种写法(首字母大写)：School

    多个单词组成：

        第一种写法(`kebab-case`命名)：my-school

        第二种写法(`CamelCase`命名)：MySchool (需要Vue脚手架支持)

        备注：(1).组件名尽可能回避HTML中已有的元素名称，例如：h2、H2都不行。

                    (2).可以使用name配置项指定组件在开发者工具中呈现的名字。

2.关于组件标签:

    第一种写法：<school></school>

    第二种写法：<school/>

        备注：不用使用脚手架时，<school/>会导致后续组件不能渲染。

3.一个简写方式：

     const school = Vue.extend(options) 可简写为：const school = options

---

关于VueComponent：

    1.school组件本质是一个名为`VueComponent`的构造函数，且不是程序员定义的，是Vue.extend生成的。

    2.我们只需要写<school/>或<school></school>，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：new VueComponent(options)。

    3.特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！！！！

    4.关于this指向：

        (1).组件配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是`【VueComponent实例对象】`。

        (2).new Vue(options)配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是`【Vue实例对象】`。

    5.VueComponent的实例对象，以后简称`vc`（也可称之为：组件实例对象）Vue的实例对象，以后简称`vm`。

---

> 1.一个重要的内置关系 : VueComponent.prototype.__proto__ === Vue.prototype
> 
> 2.为什么要有这个关系 : 让组件实例对象(VC)可以访问到Vue原型上的属性和方法

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-01-14-18-32-49-image.png)

#### 单文件组件

一个文件就是一个组件(xxx.vue),最好组件名和文件名保持一致.

可以利用vetur插件打`<v` 来快捷键生成单文件组件结构

    需要在`main.js`里引入App.vue,并且要在main.js里建立vm

    然后再html页面内引入`vue.js`和`main.js`

### 3.1 使用Vue脚手架

第一步（仅第一次执行）：全局安装@vue/cli。
    npm install -g @vue/cli
第二步：切换到你要创建项目的目录，然后使用命令创建项目
    vue create xxxx
第三步：启动项目
    npm run serve

#### 脚手架结构

   ├── node_modules

   ├── public

   │   ├── favicon.ico: 页签图标

   │   └── index.html: 主页面

   ├── src

   │   ├── assets: 存放静态资源

   │   │   └── logo.png

   │   │── component: 存放组件

   │   │   └── HelloWorld.vue

   │   │── App.vue: 汇总所有组件

   │   │── main.js: 入口文件

   ├── .gitignore: git版本管制忽略的配置

   ├── babel.config.js: babel的配置文件

   ├── package.json: 应用包配置文件

   ├── README.md: 应用描述文件

   ├── package-lock.json：包版本控制文件

#### 不同版本的脚手架

1. vue.js与vue.runtime.xxx.js的区别：

    (1) vue.js是完整版的Vue，包含：核心功能 + 模板解析器。

    (2) vue.runtime.xxx.js是运行版的Vue，只包含：核心功能；没有模板解析器。

2. 因为vue.runtime.xxx.js没有模板解析器，所以不能使用template这个配置项，需要使用`render`函数接收到的createElement函数去指定具体内容。

#### vue.config.js配置文件

1. 使用vue inspect > output.js可以查看到Vue脚手架的默认配置。

2. 使用vue.config.js可以对脚手架进行个性化定制，详情见：https://cli.vuejs.org/zh

### 3.2 ref和props

#### ref属性

1. 被用来给元素或子组件注册引用信息（id的替代者）

2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象（vc）

3. 使用方式：

    1. 打标识：```<h1 ref="xxx">.....</h1>``` 或 ```<School ref="xxx"></School>```

    2. 获取：```this.$refs.xxx```

#### props配置项

1. 功能：让组件接收外部传过来的数据 (父组件传给子组件) [父组件的data优先级更高]

2. 传递数据：```<Demo name="xxx"/>```

3. 接收数据：

    1. 第一种方式（只接收）：```props:['name'] ```

    2. 第二种方式（限制类型）：```props:{name:String}```

    3. 第三种方式（限制类型、限制必要性、指定默认值）：

```javascript
props:{
         name:{
         type:String, //类型
         required:true, //必要性
         default:'老王' //默认值
}
```

>     props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。

### 3.3 混入mixin

1. 功能：可以把多个组件共用的配置提取成一个混入对象

2. 使用方式：

    第一步定义混合：

```javascript
    {
        data(){...},
        methods:{...}
        ...
        mixins:['hunhe,hunhe2']
    }
```

    第二步使用混入：

    ​ 全局混入：```Vue.mixin(xxx)```

    ​ 局部混入：```mixins:['xxx']  ```

### 3.4 插件

1. 功能：用于增强Vue

2. 本质：包含`install`方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。

3. 定义插件：

```javascript
对象.install = function(Vue,options){
        // 1. 添加全局过滤器
        Vue.filter(...)
        // 2. 添加全局指令
        Vue.directive(....)
        // 3. 配置全局混入(合)
        Vue.mixin(...)
        // 4. 添加实例方法
        Vue.prototype.$myMethod = function () {...}
        Vue.prototype.$myProperty = xxxx
}
```

4. 使用插件：```Vue.use()```

### 3.5 scoped 样式

1. 作用：让样式在局部生效，防止冲突。

2. 写法：```<style scoped>```

### 总结TodoList案例

1. 组件化编码流程：

    ​ (1).拆分静态组件：组件要按照功能点拆分，命名不要与html元素冲突。

    ​ (2).实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用：

    ​       1).一个组件在用：放在组件自身即可。

    ​       2). 一些组件在用：放在他们共同的父组件上（<span style="color:red">状态提升</span>）。

    ​ (3).实现交互：从绑定事件开始。

2. props适用于：

    ​ (1).父组件 ==> 子组件 通信

    ​ (2).子组件 ==> 父组件 通信（要求父先给子一个函数）

3. 使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的！

4. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做。

### 3.6 本地存储(webStorage)

1. 存储内容大小一般支持5MB左右（不同浏览器可能还不一样）

2. 浏览器端通过 Window.`sessionStorage `和 Window.`localStorage `属性来实现本地存储机制。

3. 相关API：

    1. ```xxxxxStorage.setItem('key', 'value');```

        该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值。

    2. ```xxxxxStorage.getItem('person');```

        该方法接受一个键名作为参数，返回键名对应的值。

    3. ```xxxxxStorage.removeItem('key');```

        该方法接受一个键名作为参数，并把该键名从存储中删除。

    4. ``` xxxxxStorage.clear()```

        该方法会清空存储中的所有数据。

> 备注：
> 
>     1. SessionStorage存储的内容会随着浏览器窗口关闭而消失。(会话)
> 
>     2. LocalStorage存储的内容，需要手动清除才会消失。(本地存储)
> 
>     3. ```xxxxxStorage.getItem(xxx)```如果xxx对应的value获取不到，那么getItem的返回值是null。
> 
>     4. ```JSON.parse(null)```的结果依然是null。

### 3.7 组件的自定义事件

1. 一种组件间通信的方式，适用于：<strong style="color:red">子组件 ===> 父组件</strong>

2. 使用场景：A是父组件，B是子组件，B想给A传数据，那么就要在A中给B绑定自定义事件（<span style="color:red">事件的回调在A中</span>）。

3. 绑定自定义事件：

    1. 第一种方式，在父组件中：```<Demo @atguigu="test"/>```  或 ```<Demo v-on:atguigu="test"/>```

    2. 第二种方式，在父组件中：

```javascript
<Demo ref="demo"/>
        ......
        mounted(){
           this.$refs.xxx.$on('atguigu',this.test)
        }
```

    3. 若想让自定义事件只能触发一次，可以使用```once```修饰符，或```$once```方法。

4. 触发自定义事件：```this.$emit('atguigu',数据)```    

5. 解绑自定义事件```this.$off('atguigu')```

6. 组件上也可以绑定原生DOM事件，需要使用```native```修饰符。

7. 注意：通过```this.$refs.xxx.$on('atguigu',回调)```绑定自定义事件时，回调<span style="color:red">要么配置在methods中</span>，<span style="color:red">要么用箭头函数</span>，否则this指向会出问题！

### 3.8 全局事件总线

作用 : 一种组件间通信的方式，适用于`任意组件间通信`。

用法 : 安装全局事件总线`$bus`

```javascript
new Vue({
       beforeCreate() {
           Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
       },
       ......
 }) 
```

使用事件总线：

   1. 接收数据：A组件想接收数据，则在A组件中给$bus绑定自定义事件，事件的回调留在A组件自身。

```javascript
methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.$bus.$on('xxxx',this.demo)
}
```

   2. 提供数据：```this.$bus.$emit('xxxx',数据)```

> 最好在beforeDestroy钩子中，用`$off`去解绑当前组件所用到的事件。

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-01-14-18-27-46-image.png)

### 3.9 消息订阅与发布 (pubsub.js)

作用 : 一种组件间通信的方式，适用于任意组件间通信

使用步骤 ：

   1. 安装pubsub：```npm i pubsub-js```

   2. 引入: ```import pubsub from 'pubsub-js'```

   3. 接收数据：A组件想接收数据，则在A组件中订阅消息，订阅的回调留在A组件自身.

```javascript
methods(){
        demo(data){......}
    },
      ......
      mounted(){
        this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息
        this.pid = pubsub.subscribe('hello',function(msgName,data)=>{
            console.log(this)
    })
    }
    //其中this.demo可以是箭头函数等方法
```

   4. 提供数据：```pubsub.publish('xxx',数据)```

   5. 最好在beforeDestroy钩子中，用```PubSub.unsubscribe(pid)```去取消订阅。

> 一般引入第三方库写在上面,自带的库写在下面

### 3.10 nextTick (较常用)

1. 语法：```this.$nextTick(回调函数)```

2. 作用：在下一次 DOM 更新结束后执行其指定的回调。

3. 什么时候用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行。

### 3.11 过渡与动画

1. 作用：在插入、更新或移除 DOM元素时，在合适的时候给元素添加样式类名。

2. 图示：

<img title="" src="file:///C:/Users/Lenovo%20PC/AppData/Roaming/marktext/images/2023-01-15-16-41-54-image.png" alt="" data-align="inline">

1. 写法：
   
   (1) 准备好样式：

      - 元素进入的样式：

        1. v-enter：进入的起点

        2. v-enter-active：进入过程中

        3. v-enter-to：进入的终点

      - 元素离开的样式：

        1. v-leave：离开的起点

        2. v-leave-active：离开过程中

        3. v-leave-to：离开的终点

       (2) 使用```<transition>```包裹要过度的元素，并配置name属性：

```javascript
<transition name="hello">
         <h1 v-show="isShow">你好啊！</h1>
</transition>
//仅用控制v-show来控制元素显示和隐藏
.hello-enter-active{
    animation: 0.5 liner
}
```

   3. 备注：若有多个元素需要过度，则需要使用：```<transition-group>```，且每个元素都要指定```key```值。

### 4.1 Vue中的Ajax

解决跨域问题 : 使用代理服务器

通过使用vue-cli中vue.config中的devServer配置项配置代理服务器

```javascript
//方式一
devServer:{
  proxy:"http://localhost:5000"
}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。

2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。

3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器 （优先匹配前端资源）

```javascript
//开启代理服务器(方式二)
  devServer: {
    proxy: {
      //"/api"请求前缀
      '/api': {    // 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',    //<url>
        pathRewrite:{'^/api':''},    //重写路径,利用正则匹配
        ws: true, //用于支持websocket
        changeOrigin: true  //用于控制请求头中的host值
      },
      '/api2': {    // 匹配所有以 '/api2'开头的请求路径
        target: 'http://localhost:5001',
        pathRewrite:{'^/api2':''},    //重写路径,利用正则匹配
        ws: true, //用于支持websocket
        changeOrigin: true  //用于控制请求头中的host值
      }
    }
```

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。

2. 缺点：配置略微繁琐，请求资源时必须加前缀。/api

### 4.2 vue中的Ajax库

#### vue-resource

`vue-resource`是vue的插件库,目前已经停止维护

```javascript
//引入插件
import vueResource from 'vue-resource
//使用插件
Vue.use(vueResource)    
this.$http.get('url').then(
    ...
)
```

### 4.3 插槽 (slot)

1. 作用：让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式，适用于 `父组件 ===> 子组件 `。

2. 分类：默认插槽、具名插槽、作用域插槽

3. 使用方式：

#### 默认插槽

    没有传输具体结构的时候就输出默认值

```javascript
//父组件中：
<Category>
    <div>html结构1</div> //传入插槽
</Category>
//子组件中：
<template>
    <div>
    <!-- 定义插槽 (等待组件使用者填充)-->
        <slot>插槽默认内容...</slot>
    </div>
</template>
```

#### 具名插槽

    每一个插槽都有自己的名字,有两种写法 : `slot='name'` 和 `v-slot:footer` (仅在template)

```javascript
 //父组件中：
<Category>
    <template slot="center">
        <div>html结构1</div>
    </template>

    <template v-slot:footer>
        <div>html结构2</div>
    </template>
</Category>
//子组件中：
<template>
    <div>
    <!-- 定义插槽 -->
        <slot name="center">插槽默认内容...</slot>
        <slot name="footer">插槽默认内容...</slot>
    </div>
</template>
```

#### 作用域插槽

      1. 理解：`数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定`（games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定）

      2. 具体编码：

```javascript
//父组件中：
<Category>
    <template scope="scopeData">
    <!-- 生成的是ul列表 -->
        <ul>
            <li v-for="g in scopeData.games" :key="g">{{g}}</li>
        </ul>
    </template>
</Category>

<Category>
    <template slot-scope="scopeData">
    <!-- 生成的是h4标题 -->
        <h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
    </template>
</Category>

//子组件中：
<template>
    <div>
        <slot :games="games"></slot>
    </div>
</template>

<script>
export default {
    name:'Category',
    props:['title'],
    //数据在子组件自身
    data() {
        return {
            games:['红色警戒','穿越火线','劲舞团','超级玛丽']
        }
    },
}
</script>
```

### 5.认识Vuex

#### 概念

​     在Vue中实现`集中式状态（数据）管理`的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

#### 何时使用？

​     多个组件需要共享数据时,Vuex不属于任何一个组件,属于一个`共享的组件`

工作原理图 : 

    `仅需要调用dispatch和commit这两个api`

![vuex.png](C:\Users\Lenovo%20PC\Desktop\code\Vue\资料（含课件）\02_原理图\vuex.png)

三个点Actions Mutations State由`store`管理,三样都是object对象

#### 搭建Vuex环境

1. 创建文件：```src/store/index.js```

```javascript
//引入Vue核心库
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//应用Vuex插件
Vue.use(Vuex)
//准备actions对象——响应组件中用户的动作
const actions = {}
//准备mutations对象——修改state中的数据
const mutations = {}
//准备state对象——保存具体的数据
const state = {}
//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

2. 在```main.js```中创建vm时传入```store```配置项

```javascript
......
//引入store
import store from './store'
//创建vm
new Vue({
    el:'#app',
    render: h => h(App),
    store
})
```

> import 语句有提升,优先执行

#### 基本使用

1. 初始化数据、配置```actions```、配置```mutations```，操作文件```store.js```

```javascript
//引入Vue核心库
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//引用Vuex
Vue.use(Vuex)
const actions = {
//响应组件中加的动作
jia(context,value){
// console.log('actions中的jia被调用了',miniStore,value)
context.commit('JIA',value)
    },
}
const mutations = {
//执行加
JIA(state,value){
// console.log('mutations中的JIA被调用了',state,value)
        state.sum += value
    }
}
//初始化数据
const state = {
    sum:0
}
//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state,
})
```

2. 组件中读取vuex中的数据：```$store.state.sum```

3. 组件中修改vuex中的数据：```$store.dispatch('action中的方法名',数据)``` 或 ```$store.commit('mutations中的方法名',数据)```

>     备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写`dispatch`，直接编写`commit`

tip : 一般业务逻辑(判断)都放在`actions`中,不单独放在组件中

#### getter配置项

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。

2. 在```store.js```中追加```getters```配置

```javascript
const getters = {
    bigSum(state){
        return state.sum * 10    //需要写返回值
    }
}
//创建并暴露store
export default new Vuex.Store({
    ......
    getters    //需要调用getters配置项
})
```

3. 组件中读取数据：```$store.getters.bigSum```

#### 四个map方法的使用

1. <strong>mapState方法：</strong>用于帮助我们映射```state```中的数据为计算属性

```javascript
computed: {
    //借助mapState生成计算属性：sum、school、subject（对象写法）
    ...mapState({sum:'sum',school:'school',subject:'subject'}),
    //借助mapState生成计算属性：sum、school、subject（数组写法）
    ...mapState(['sum','school','subject']),
},
```

2. <strong>mapGetters方法：</strong>用于帮助我们映射```getters```中的数据为计算属性

```javascript
computed: {
    //借助mapGetters生成计算属性：bigSum（对象写法）
    ...mapGetters({bigSum:'bigSum'}),
    //借助mapGetters生成计算属性：bigSum（数组写法）
    ...mapGetters(['bigSum'])
},
```

3. <strong>mapActions方法：</strong>用于帮助我们生成与```actions```对话的方法，即：包含```$store.dispatch(xxx)```的函数

```javascript
methods:{
    //靠mapActions生成：incrementOdd、incrementWait（对象形式）
    ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'}),
    //靠mapActions生成：incrementOdd、incrementWait（数组形式）
    ...mapActions(['jiaOdd','jiaWait'])
}
```

4. <strong>mapMutations方法：</strong>用于帮助我们生成与```mutations```对话的方法，即：包含```$store.commit(xxx)```的函数

```javascript
methods:{
    //靠mapActions生成：increment、decrement（对象形式）
    ...mapMutations({increment:'JIA',decrement:'JIAN'}),
    //靠mapMutations生成：JIA、JIAN（对象形式）
    ...mapMutations(['JIA','JIAN']),
}
```

>     备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。

#### 模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确。

2. 修改```store.js```

```javascript
const countAbout = {
     namespaced:true,//开启命名空间
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }

const personAbout = {
     namespaced:true,//开启命名空间
     state:{ ... },
     mutations: { ... },
     actions: { ... }
   }

const store = new Vuex.Store({
     modules: {
       countAbout,
       personAbout
     }
   })
```

3. 开启命名空间后，组件中读取state数据：

```javascript
//方式一：自己直接读取
this.$store.state.personAbout.list
//方式二：借助mapState读取：
...mapState('countAbout',['sum','school','subject']),
```

4. 开启命名空间后，组件中读取getters数据：

```javascript
//方式一：自己直接读取
this.$store.getters['personAbout/firstPersonName']
//方式二：借助mapGetters读取：
...mapGetters('countAbout',['bigSum'])
```

5. 开启命名空间后，组件中调用dispatch

```javascript
//方式一：自己直接dispatch
this.$store.dispatch('personAbout/addPersonWang',person)
//方式二：借助mapActions：
...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
```

6. 开启命名空间后，组件中调用commit

```javascript
//方式一：自己直接commit
this.$store.commit('personAbout/ADD_PERSON',person)
//方式二：借助mapMutations：
...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
```

>     注意 : 通过mapxxx的方法使用的格式基本一致,但直接使用dispatch/commit/getters需要使用'/'作为查找方式
> 
>     且可以使用模块化将personAbout和countAbout分成两个js文件,最后引入index.js文件中

### 6.vue-router (路由)

1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。专门用于实现SPA应用

2. 前端路由：key是路径，value是组件或function。

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-01-20-00-53-52-image.png)

#### 基本使用

1. 安装vue-router，命令：```npm i vue-router```

2. 应用插件：```Vue.use(VueRouter)```

3. 编写router配置项:

```javascript
//引入VueRouter
import VueRouter from 'vue-router'
//引入Luyou 组件
import About from '../components/About'
import Home from '../components/Home'
//创建router实例对象，去管理一组一组的路由规则
const router = new VueRouter({
    routes:[
        {
            path:'/about',
            component:About
        },
        {
            path:'/home',
            component:Home
        }
    ]
})
//暴露router
export default router
```

4. 实现切换（active-class可配置高亮样式）

```vue
<router-link active-class="active" to="/about">About</router-link>
```

5. 指定展示位置

```vue
<router-view></router-view>
```

#### 几个注意点

1. 路由组件通常存放在```pages```文件夹，一般组件通常存放在```components```文件夹。

2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。

3. 每个组件都有自己的```$route```属性，里面存储着自己的路由信息。

4. 整个应用只有一个router，可以通过组件的```$router```属性获取到。

#### 嵌套(多级)路由

1. 配置路由规则，使用children配置项：

```javascript
routes:[
      {
         path:'/about',
         component:About,
      },
      {
         path:'/home',
         component:Home,
         children:[ //通过children配置子级路由
            {
               path:'news', //此处一定不要写：/news
               component:News
            },
            {
               path:'message',//此处一定不要写：/message
               component:Message
            }
         ]
      }
]
```

2. 跳转（要写完整路径）：

```vue
<router-link to="/home/news">News</router-link>
```

#### 路由的query参数

1. 传递参数

```javascript
<!-- 跳转并携带query参数，to的字符串写法 -->
<router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link>
<!-- 跳转并携带query参数，to的对象写法 -->
<router-link
      :to="{
         path:'/home/message/detail',
         query:{
            id:666,
            title:'你好'
         }
      }"
>跳转
</router-link>
```

2. 接收参数：

```javascript
$route.query.id
$route.query.title
```

>  标签内key或to前面加冒号':' 表示将后面的数据视作JavaScript运算符来解析

#### 命名路由

1. 作用：可以简化路由的跳转。

2. 如何使用

       1. 给路由命名：

```javascript
{
    path:'/demo',
    component:Demo,
    children:[
        {
            path:'test',
            component:Test,
            children:[
                {
                    name:'hello' //给路由命名
                    path:'welcome',
                    component:Hello,
                }
            ]
        }
    ]
}
```

       2. 简化跳转：

```javascript
<!--简化前，需要写完整的路径 -->
<router-link to="/demo/test/welcome">跳转</router-link>
<!--简化后，直接通过名字跳转 -->
<router-link :to="{name:'hello'}">跳转</router-link>
<!--简化写法配合传递参数 -->
      <router-link
         :to="{
            name:'hello',
            query:{
               id:666,
               title:'你好'
            }
         }"
      >跳转</router-link>
```

#### 路由的params参数

1. 配置路由，声明接收params参数(需要使用占位符占用)

```javascript
   {
      path:'/home',
      component:Home,
      children:[
         {
            path:'news',
            component:News
         },
         {
            component:Message,
            children:[
               {
                  name:'xiangqing',
                  path:'detail/:id/:title', //使用占位符声明接收params参数
                  component:Detail
               }
            ]
         }
      ]
   }
```

2. 传递参数

```javascript
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <router-link :to="/home/message/detail/666/你好">跳转</router-link>
   <!-- 跳转并携带params参数，to的对象写法 -->
   <router-link
      :to="{
         name:'xiangqing',
         params:{
            id:666,
            title:'你好'
         }
      }"
   >跳转</router-link>
```

>     特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！(query对应path,params对应name)

3. 接收参数：

```javascript
$route.params.id
$route.params.title
```

#### 路由的props配置

​  作用：让路由组件更方便的收到参数

```javascript
{
   name:'xiangqing',
   path:'detail/:id',
   component:Detail,
   //第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
   // props:{a:900}

   //第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
   // props:true

   //第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
   props(route){
      return {
         id:route.query.id,
         title:route.query.title
      }
   }
}
```

#### ```<router-link>```的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式

2. 浏览器的历史记录有两种写入方式：分别为```push```和```replace```，```push```是追加历史记录，```replace```是替换当前记录。路由跳转时候默认为```push```

3. 如何开启```replace```模式：```<router-link replace .......>News</router-link>```

>     类似于无痕浏览

#### 编程式路由导航

1. 作用：不借助```<router-link> ```实现路由跳转，让路由跳转更加灵活

2. 具体编码：

```javascript
   //$router的两个API
   this.$router.push({
       name:'xiangqing',
           params:{
               id:xxx,
               title:xxx
           }
   })

   this.$router.replace({
       name:'xiangqing',
           params:{
               id:xxx,
               title:xxx
           }
   })
   this.$router.forward() //前进
   this.$router.back() //后退
   this.$router.go() //可前进也可后退
```

#### 缓存路由组件

1. 作用：让不展示的路由组件保持挂载，不被销毁。

2. 具体编码：

```javascript
//缓存一个
<keep-alive include="News">
    <router-view></router-view>
</keep-alive>
//缓存多个
<keep-alive include="News">
    <router-view></router-view>
</keep-alive>
```

>  include 中缓存的名字指的是`组件名`

#### 两个新的生命周期钩子

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。

2. 具体名字：

       1. `activated`路由组件被激活时触发。

       2. `deactivated`路由组件失活时触发。

> 另一个生命周期钩子是nextTick => 会在页面渲染完成后执行

#### 路由守卫

1. 作用：对路由进行`权限`控制

2. 分类：`全局守卫`、`独享守卫`、`组件内守卫`

3. 全局守卫:

```javascript
    //全局前置守卫：初始化时执行、每次路由切换前执行
    router.beforeEach((to,from,next)=>{
    console.log('beforeEach',to,from)
      if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
         if(localStorage.getItem('school') === 'atguigu'){ //权限控制的具体规则
            next() //放行
         }else{
            alert('暂无权限查看')
            // next({name:'guanyu'})
         }
      }else{
         next() //放行
      }
   })
   //全局后置守卫：初始化时执行、每次路由切换后执行
   router.afterEach((to,from)=>{
      console.log('afterEach',to,from)
      if(to.meta.title){
         document.title = to.meta.title //修改网页的title
      }else{
         document.title = 'vue_test'
      }
   })
```

4. 独享守卫:

```javascript
   beforeEnter(to,from,next){
      console.log('beforeEnter',to,from)
      if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
         if(localStorage.getItem('school') === 'atguigu'){
            next()
         }else{
            alert('暂无权限查看')
            // next({name:'guanyu'})
         }
      }else{
         next()
      }
   }
```

>  独享路由守卫没有后置路由守卫

5. 组件内守卫：

```javascript
//进入守卫：通过路由规则，进入该组件时被调用
beforeRouteEnter (to, from, next) {
},
//离开守卫：通过路由规则，离开该组件时被调用
beforeRouteLeave (to, from, next) {
}
```

#### 路由器的两种工作模式

1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。

2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。

3. hash模式：

       1. 地址中永远带着#号，不美观 。

       2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。

       3. 兼容性较好。

4. history模式：

       1. 地址干净，美观 。

       2. 兼容性和hash模式相比略差。

       3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

### 7. Vue UI组件库

#### 7.1 移动端常用UI组件库

1. Vant 

2. Cube UI

3. Mint UI

#### 7.2 PC端常用UI组件库

1. Element UI

2. IView UI
