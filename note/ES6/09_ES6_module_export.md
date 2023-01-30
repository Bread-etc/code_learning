# ES6模块化语法 | 集创考核第九篇笔记

---

## 概述

​	在 ES6 前， 实现模块化使用的是 RequireJS 或者 seaJS（分别是基于 AMD 规范的模块化库，  和基于 CMD 规范的模块化库）。ES6 引入了模块化，其设计思想是在编译时就能确定模块的依赖关系，以及输入和输出的变量。ES6 的模块化分为**导出（export）** @与**导入（import）**两个模块。

---

## export与import

export 命令用于规定模块的对外接口。

import 命令用于输入其他模块提供的功能。

---

## 三种暴露方式

### 分别暴露

```javascript
// 分别暴露
export let school = 'gdut'

export function teach(){
    console.log('我开摆啦！');
}
```

### 统一暴露 

```javascript
let school = 'gdut'
function findJob(){
    console.log("我又开摆啦！");
}

//
export {school,findJob}
```

### 默认暴露 

```javascript
export default {
    school: 'gdut',
    change: function() {
        console.log("我又又又开摆啦！");
    }
}
```

---

## 引入方式

### 通用的导入方式 

```javascript
//引入m1.js 模块内容
import * as m1 from "./js/m1.js"
//引入 m2.js 模块内容
import * as m2 from "./js/m2.js"
//引入 m3.js 模块内容
import * as m3 from "./js/m3.js";
console.log(m3);
m3.default.change()
```

### 解构赋值引入

```javascript
import { school,teach } from "./js/m1.js";
import {school as gd ,findJob} from './js/m2.js'
import {default as m3} from './js/m3.js'
//console.log(school);
//console.log(teach);
//console.log(gd,findJob);
//console.log(m3);
```

### 简便形式引入(仅针对默认暴露)

```javascript
import m4 from "./js/m3.js"
console.log(m4);
```

**Tip**:或者可以选择通过**App.js**接口文件来引入

```javascript
<script type="module" src="./js/app.js"></script>
```

