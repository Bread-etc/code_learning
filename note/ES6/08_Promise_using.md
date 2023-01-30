# Promise | 集创考核第八篇笔记

---

## Promise 状态

Promise 异步操作有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）。除了异步操作的结果，任何其他操作都无法改变这个状态。

Promise 对象只有：从 pending 变为 fulfilled 和从 pending 变为 rejected 的状态改变。只要处于 fulfilled 和 rejected ，状态就不会再变了即 resolved（已定型）

```javascript
//实例化promise对象
    const p = new Promise(function(resolve,reject){
        setTimeout(() => {
            let data = "user's data in sql"
            //resolve 表示成功
            resolve(data)

            let err = "data reading failed"
            //reject 表示失败
            reject(err)
        }, 1000);
    })
```

---

## then 方法

then 方法接收两个函数作为参数，第一个参数是 Promise 执行成功时的回调(value)，第二个参数是 Promise 执行失败时的回调(reason)，两个函数只会有一个被调用。

```JavaScript
//调用promise对象的then方法
    p.then(function(value){
        console.log(value);
    },function(reason){
        console.error(reason)
    })
```

注意:调用then方法 then方法的返回结果是promise对象。对象状态由回调函数决定 

---

## Promise封装Ajax请求

```javascript
// 接口地址：URL
    //1. 创建对象
    const p = new Promise((resolve,reject) => {
        const xhr = new XMLHttpRequest()
        //2. 初始化
        xhr.open("GET","URL")
        //3. 发送
        xhr.send()
        //4. 绑定事件，处理响应结果
        xhr.onreadystatechange = function(){
            if (xhr.readyState === 4) {
                //判断响应状态码 200-300
                if (xhr.status >= 200 && xhr.status < 300) {
                    //表示成功
                    resolve(xhr.response);
                }else{
                    //如果失败
                    reject(xhr.status);
                }
            }
        }
    })
    p.then(function(value){
        console.log(value);
    },function(reason){
        console.error(reason);
    })
```

往后有更多的Promise的相关知识再进行补充。