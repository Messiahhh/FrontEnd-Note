# Akara前端开发复习

[TOC]





## HTML5

script脚本的**执行**会阻塞HTML的解析

``` html
<script>
	// do something...
</script>
<div>
    <!-- html解析被阻塞 -->
</div>
```

因此，外链脚本的**加载**也会阻塞HTML的解析

``` html 
<script src="./index.js"></script>
<div>
    // 加载完脚本，执行完脚本，才进行html的解析
</div>
```

不过，多个外链script的**加载是并行的**。

``` html
<script src="1.js"></script>
<script src="2.js"></script>
<script src="3.js"></script>
<script src="3.js"></script>
```

按理来说，因为加载会阻塞接下来HTML的解析，所以加载也会是串行的。

但实际上浏览器会进行**预解析**，提前把html中要引用到的资源放进任务队列中。

### defer和async的区别

共同点：script资源的加载和html的解析是同步的，不会阻塞html的解析。

不同点：

- async属性。脚本下载完之后会停止html的解析，开始脚本的执行，等脚本执行完后再继续html的解析。
- defer属性。等整个html文档解析完（DOMContentLoaded事件发生），脚本才开始执行。

async属性

<img src="https://segmentfault.com/img/bVWhRl?w=801&amp;h=814" style="zoom:80%;" />

### href和src的区别

href： 用于在当前文档和指定资源间确定联系

``` html
<a href="http://www.baidu.com"></a> 
<link type="text/css" rel="stylesheet" href="common.css"> 
```

src：下载资源并替换当前内容

``` html
<img src="img/girl.jpg"> 
<iframe src="top.html"> 
<script src="show.js"> 
```

### link和@import的区别

1. link是XHTML提供的标签，不仅可以加载CSS。@import是CSS提供的语法规则，只能加载CSS
2. 加载页面时，`link`标签引入的 CSS 被同时加载；`@import`引入的 CSS 将在页面加载完毕后被加载。



## CSS3

### CSS基础

##### 选择器

1. 标签选择器
2. ID选择器
3. class选择器
4. *选择器
5. 子代选择器（如div > p，父子关系）
6. 后代选择器 （如div p， 可以是爷爷和孙子的关系）
7. 相邻兄弟选择器（如div + p， 选择紧邻着div后面的p）
8. 属性选择器(如`[type=input]`)
9. 伪类选择器(`:hover`等)



`:first-child`和`:first-of-type` 不要混淆了

`:first-child`: 指定只有当<p>元素是其父级的第一个子级的样式。

`:first-of-type`: 选择每个p元素是其父级的第一个p元素



##### 继承属性

常见的继承属性：

1. `font`系列，如`font-weight`， `font-style`， `color`
2. `visibility`



##### 属性的权重

!important > 内联样式 > ID选择器 > class选择器 > 标签选择器 > 通配符（*） > 浏览器默认样式 > 继承样式



### 布局

**块级元素（block）**：div, h1~h6, p 等

独占一行；可以设置宽和高

**行内元素（inline）**：a, span, img, input 等

不独占一行；不能设置宽和高

**inline-block** ：selection

不独占一行；可以设置宽和高。

##### 水平垂直居中

一：absolute + margin-top

``` css
.outer {
    position: relative
}

.inner {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-top: -100px;
    margin-left: -100px;
}
```

二：absolute + calc

``` css
.outer {
    position: relative
}

.inner {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    position: absolute;
    top: calc(50% - 100px);
    left: calc(50% - 100px);
}
```

三：absolute + transform 

``` css
.outer {
    position: relative
}

.inner {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

四：flex + margin: auto

``` css
.outer {
    display: flex;
}
.inner {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    margin: auto;
}
```

五：flex

``` css
.outer {
    display: flex;
    justify-content: center;
    align-items: center;
}
```

##### 两栏布局

一：float + margin-left 或者 float + overflow: auto

``` html
<style>
	.aside {
        width: 30vw;
        height: 100vh;
        float: left;
        background: blue;
    }

    .main {
        margin-left: 30vw;
        // 或者换成 overflow: auto，使其成为BFC
    }
</style>
<body>
    <div class="aside">

    </div>
    <div class="main">
        <div class="content">

        </div>
    </div>
</body>
```

二：flex

``` html
<style>
    body {
        display: flex;
    }
    
    .aside {
        flex: 0 0 25vw;
        // or width: 25vw;
    }
    
    .main {
        flex: 1; // 等于flex: 1 1;
    }
</style>
<body>
    <div class="aside">

    </div>
    <div class="main">
        
    </div>
</body>
```

##### 三栏布局

一：圣杯布局

``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>实现三栏水平布局之圣杯布局</title>
    <style type="text/css">
      /*基本样式*/
      .left, .right, .main {
        min-height: 300px;
      }
      .left {
        width: 200px;
        background-color:thistle;
      }
      .main {
        background-color: #999;
      }
      .right {
        width: 300px;
        background-color: violet;
      }
      /* 圣杯布局关键代码 */
      .left, .main, .right {
        float: left;
        position: relative;
      }
      .main {
        width: 100%;
      }
      .container {
        padding-left: 200px;
        padding-right: 300px;
      }
      .left {
        margin-left: -100%;
        left: -200px;
      }
      .right {
        margin-left: -300px;
        right: -300px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="main">main</div>
      <div class="left">left</div>
      <div class="right">right</div>
    </div>
  </body>
</html>

```

二：双飞翼布局

``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>双飞翼布局</title>
    <style>
      .left,
      .right,
      .main {
        min-height: 200px;
      }
      .left {
        width: 200px;
        background-color: thistle;
      }
      .main {
        background: #999;
      }
      .right {
        width: 300px;
        background-color: violet;
      }
      /* 双飞翼布局重点 */
      .left,
      .main,
      .right {
        float: left;
      }
      .main {
        width: 100%;
      }
      .main-inner {
        margin-left: 200px;
        margin-right: 300px;
      }
      .left {
        margin-left: -100%;
      }
      .right {
        margin-left: -300px;
      }
    </style>
  </head>
  <body>
    <div class="main"><div class="main-inner">中心区</div></div>
    <div class="left">left</div>
    <div class="right">right</div>
  </body>
</html>

```

三：flex布局

``` html
<style>
	.left {
        width: 30vw;
    }

    .main {
        flex: 1;
    }

    .right {
        width: 20vw;
    }
</style>
<body>
    <div class="left">

    </div>
    <div class="main">

    </div>
    <div class="right">

    </div>
</body>
```

##### 移动端布局

``` html
<meta name="viewport" content="width=device-width,initial-scale=1">
```

**em和rem**

1em等于父元素的字体大小（font-size）

1rem等于根元素（html）的字体大小。移动端的时候根据媒体查询动态改变根元素的字体大小即可。



1vw 等于 1 / 100 视口宽

1vh 等于 1 / 100 视口高



### BFC 

BFC，也就是Block Formatting Contexts （块级格式化上下文)

**何时会触发BFC**：

- 根元素`<html>`
- `float`的值不为`none`。
- `position`的值不为`relative`和`static`。
- `overflow`的值为`auto`,`scroll`或`hidden`。
- `display`的值为`table-cell`, `table-caption`, `inline-block`中的任何一个。

**作用**

一：清除浮动

``` css
.outer {
	// 使用overflow: auto;使outer元素成为BFC（触发outer元素的BFC）
    overflow: auto;
}
.inner {
    width: 200px;
    height: 200px;
    float: left;
}
```

二：外边距合并：同属一个BFC的相邻元素会发生外边距（margin）重叠。

```html
<style>
    .upper {
        margin: 20px;
    }
    .lower {
        margin: 20px;
    }
    .bfc {
        overflow: auto;
    }
</style>
<div class="upper"></div>
<div class="bfc">
    <div class="lower">

    </div>
</div>
```

三：阻止元素被浮动元素覆盖，可用来实现**两列布局**

``` html
<style>
    .float {
        float: left;
    }
    
    .content {
        overflow: auto;
    }
</style>
<div class="float"></div>
<div class="content"></div>
```

### 清除浮动

一：上述的BFC清除浮动

二：添加额外标签，应用clear: both

``` html
<style>
    .float {
        float: left;
    } 
    .clear {
        clear: both;
    }
</style>
<div>
    <div class="float">
        
    </div>
    <div class="clear">
        
    </div>
</div>
```

三：使用伪元素，应用clear: both

``` html
<style>
    .float {
        float: left;
    } 
    .clearfix:after {
        content: "";
        display: block;
        clear: both;
    }
</style>
<div class="clearfix">
    <div class="float">
        
    </div>
</div>
```

### inline-block的间隙问题

两个display：inline-block元素放到一起会产生一段空白。

原因：此时两个元素间的回车/换行会被转换为空白符

``` html
<body>
    <div class="a">
        1
    </div>
    <div class="a">
        2
    </div>
</body>
```

解决方案:

1. 将子元素标签的结束符和下一个标签的开始符写在同一行或把所有子标签写在同一行

   ``` html
   <body>
       <div class="a">
           1
       </div><div class="a">
           2
       </div>
   </body>
   ```

2. 父元素设置font-size: 0; 子元素重新设置正确的font-size

### display: none，visibility: hidden, opacity: 0 的区别

三个样式的作用都是使目标元素不可见，不过三个元素之间也是有区别的

- 结构上

  `display: none` 会让目标元素不会被渲染进渲染树， 因此**不占空间，而且不能点击。**

  `visibility: hidden`目标元素会被渲染进渲染树，因此**占空间，但是不能点击。**

  `opacity: 0`目标元素会被渲染进渲染树，因此**占空间，而且能点击。**

- 继承上

  `display: none` 作用于父元素后，子元素也不会被渲染（即使给子元素加了`display: block`）

  `visibility: hidden`作用于父元素后，子元素继承这个属性，也不可见；不过可以给子元素设置`visibility: visible`使其可见。

  `opacity: 0`作用于父元素后，虽然子元素不会继承这个属性，但是子元素的透明度也会被影响，所以也不可见；而且不能通过给子元素设置`opacity: 1`使其变成不透明。

- 性能上

  `display: none`会造成回流/重绘，性能影响大

  `visibility: hidden`会造成元素内部的重绘，性能影响相对小

  `opacity: 0` 由于`opacity`属性启用了GPU加速，性能最好



`opacity`属性的补充

`opacity`是不继承属性，父元素设置opacity，子元素并不会继承。但是因为该属性的特殊性（类似`background`），父元素有了透明度，子元素的样式也会被影响。如果父元素设置`opacity: 0.5`，子元素设置`opacity: 0.5`，那么实际上子元素的透明度是0.5 * 0.5 = 0.25。



如果希望子元素不被父元素的透明度影响，我们可以使用`background: rgba`代替`opacity: 0`



### 文本溢出

##### 单行文本

``` css
overflow: hidden;
white-space: nowrap;
text-overflow: ellipsis;
```

##### 多行文本



 

## ECMAScript

### 数组

##### 判断数组

``` javascript
var arr = []

arr instanceof Array

Array.prototype.isPrototypeOf(arr)

arr.constructor === Array

Object.prototype.toString.call(arr) === "[object Array]"

Array.isArray(arr)
```

##### 转化为数组

``` javascript
var set = new Set([1, 2])

// 类数组对象以及部署了遍历器接口的对象
Array.from(set)

[...set]

// 类数组对象 (arguments和Nodelist) 
Array.prototype.slice.call(arguments)
```

##### 数组去重

``` javascript
var arr = [1, 2, 2, 4, 9, 6, 7, 5, 2, 3, 5, 6, 5]


// Set
Array.from(new Set(arr))


//缺点： indexOf或者includes都是遍历数组，时间复杂度高
function unique(arr) {
    let newArr = []
    for (let i = 0; i < arr.length; i++) {
        let item = arr[i]
        if (!newArr.includes(item)) {
            newArr.push(item)
        }
    }
    return newArr
}

// 把数组的值放在对象的键值里， 对于 数组中的 1 和 “1” ， 要再一次对比
// 空间复杂度高， 所谓的空间换时间
function unique(arr) {
    let newArr = []
    let obj = {}
    for (let i = 0; i < arr.length; i++) {
        let item = arr[i]
        let type = typeof item
        if (!obj[item]) {
            newArr.push(item)
            obj[item] = [type]
        } else if (!obj[item].includes(type)){
            newArr.push(item)
            obj[item].push(type)
        }
    }
    return newArr
}

// 排序法，先用sort排序再比较
// 相比前两种，时间和空间用的都没那么多
// 缺点：排序了
function unique(arr) {
    arr.sort()
    let newArr = [arr[0]]
    for (let i = 0; i < arr.length; i++) {
        let item = arr[i]
        if (newArr[newArr.length - 1] !== item) {
            newArr.push(item)
        }
    }
    return newArr
}
```

##### 数组扁平化

###### flat(Infinity)

``` javascript
var arr = [1, 2, [3, [4, 5]]]

arr.flat(Infinity)
```

###### replace + split

``` javascript
var arr = [1, 2, [3, [4, 5]]]
var str = JSON.stringify(arr)
arr = str.replace(/(\[|\])/g, "").split(",")
```

###### 递归

``` javascript
var arr = [1, 2, [3, [4, 5]]]
let result = []

function fn(arr) {
    arr.forEach(i => {
        if (Array.isArray(i)) fn(i)
        else {
            result.push(i)
        }
    })
}
```

##### reduce 实现 map

```javascript
Array.prototype.map = function (fn) {
    let result = []
    this.reduce((total, current, index) => {
        result.push(fn(current))
    }, [])
    return result
}
```

##### 数组乱序

``` javascript
let arr = [1, 2, 3, 4, 5]
arr.sort(() => {
    return Math.random() - 0.5
})

// 加强版
// 遍历数组，每一项和该项之前的随机项交换位置
function shuffle(arr) {
    for (let i = 0; i < arr.length; i++) {
        let j = ~~(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]]
    }
}
```





### 对象

##### 浅拷贝

``` javascript
// Object.assign
let source = {
    name: 'akara',
    age: 20,
}
let target = Object.assign({}, source)

// 扩展运算符
let source = {
    name: 'akara',
    age: 20,
}
let target = {...source}

// slice
let source = [1, 2, 3]
let target = source.slice()

// concat
let source = [1, 2, 3]
let target = source.concat()
```

##### 深拷贝

```javascript
// 一:
// 只能用于对象内部没有方法时
JSON.parse(JSON.stringify(obj))

// 二:
// 1.判断属性是对象还是函数
// 2.数组或是对象
// 3.判断是否为对象的isObject
// 4.WeakMap 实现 循环引用的检测

function deepClone(source, hash = new WeakMap()) {
    let target
    if (hash.has(source)) {
        return hash.get(source)
    }
    if (typeof source === 'object') {
        target = Array.isArray(source) ? [] : {}
        hash.set(source, target)
        for (let key in source) {
            if (source.hasOwnProperty(key)) {
                target[key] = isObject(source[key]) ? deepClone(source[key], hash) : source[key]
            }
        }
    }
    else {
        target = source
    }
    return target
}

function isObject(obj) {
    return (typeof obj === "object" || typeof obj === "function") && obj !== null
}
var obj = {}
obj.a = obj

deepClone(obj)


```

##### 继承

``` javascript
function Animal(name, size) {
    this.name = name
    this.size = size
}

Animal.prototype.eat = function (food) {
    console.log(this.name + "正在吃" + food)
}
```

###### 构造继承

1. 可以多继承。
2. 只能继承父类的实例属性和方法，不能继承原型属性和方法

``` javascript
function Cat() {
    Animal.call(this)
}

var cat = new Cat()
```

###### 原型链继承

1. 不能多继承。
2. 所有新实例会共享父类的属性

``` javascript
// cat >= Cat.prototype >= Animal.prototype >= Object.prototype
function Cat() {

}

Cat.prototype = new Animal()
Cat.prototype.name = "cat"

var cat = new Cat()
```

###### 组合继承

可以继承实例属性和方法，也可以继承原型属性和方法
缺点: 调用两次父类构造函数

``` javascript
function Cat (name) {
    Animal.call(this)
    this.name = name
}

Cat.prototype = new Animal()
Cat.prototype.constructor = Cat

var cat = new Cat()
```

###### 寄生组合继承(除es6继承外最推荐的方法)

``` javascript
function Cat(name) {
    Animal.call(this)
}

Cat.prototype = Object.create(Animal.prototype)
Cat.prototype.constructor = Cat
```

###### es6的extends

``` javascript
class Cat extends Animal {
    constructor(name) {
        super(name)
    }
}
```



### 函数

##### 函数防抖

```html
<input type="text" name="" value="">
<script type="text/javascript">
    let el = document.querySelector('input')
    el.addEventListener("input", debounce(A, 500))

    function debounce(fn, delay) {
        let timer = null
        return function () {
            timer && clearTimeout(timer)
            timer = setTimeout(() => {
                fn.call(this)
            }, delay)
        }
    }

    function A() {
        console.log(this.value)
    }
</script>
```

##### 函数节流

```javascript
function throttle (fn, time = 1000) {
    let canRun = true;
    return function () {
        if (!canRun) return false;
        canRun = false;
        setTimeout(() => {
            fn.call(this)
            canRun = true
        }, time)
    }
}

setInterval(throttle(function() {
    console.log("hello world")
}), 100)
```

##### 实现bind函数

```javascript
Function.prototype.bind = function (context, ...args) {
    return (...newArgs) => {
        this.call(context, ...args, ...newArgs)
    }
}
```

##### 实现call/apply函数

``` javascript
Function.prototype.call = function (context, ...args) {
    // context为null时，为window
    let context = context || window
    context.fn = this
    let result = context.fn(...args)
    delete context.fn
    return result
}

// apply 只需要把参数修改即可
Function.prototype.apply = function (context, args) {
    let context = context || window
    context.fn = this
    let result = context.fn(...args)
    delete context.fn
    return result
}
```



### Promise实现

``` javascript
class Promise {
    constructor(executor) {
        this.status = "pending"
        this.value = undefined
        this.onResolvedCallback = []
        this.onRejectedCallback = []

        const resolve = (value) => {
            if (value instanceof Promise) {
                value.then((data) => {
                    resolve(data)
                }, (reason) => {
                    reject(reason)
                })
            }
            else {
                if (this.status === "pending") {
                    this.status = "fulfilled"
                    this.value = value
                    this.onResolvedCallback.forEach(callback => {
                        callback()
                    })
                }
            }
        }

        const reject = (reason) => {
            if (this.status === "pending") {
                this.status = "rejected"
                this.value = reason
                this.onRejectedCallback.forEach(callback => {
                    callback()
                })
            }
        }

        try {
            executor(resolve, reject)
        } catch (e) {
            reject(e)
        }

    }

    then(onResolve, onReject) {
        let promise = new Promise((resolve, reject) => {
            if (this.status === "pending") {
                this.onResolvedCallback.push(() => {
                    // setTimeout 模拟异步， 实际上then是放进微队列而setTimeout是放进宏队列
                    setTimeout(() => {
                        try {
                            resolve(onResolve(this.value))
                        } catch (e) {
                            reject(e)
                        }
                    })
                })
                this.onRejectedCallback.push(() => {
                    setTimeout(() => {
                        try {
                            resolve(onReject(this.reason))
                        } catch (e) {
                            reject(e)
                        }
                    })
                })
            }
            else if (this.status === "fulfilled") {
                setTimeout(() => {
                    try {
                        let x = onResolve(this.value)
                        resolvePromise(promise, x, resolve, reject)
                        // resolve(onResolve(this.value))
                    } catch (e) {
                        reject(e)
                    }
                })
            }
            else if (this.status === "rejected") {
                setTimeout(() => {
                    try {
                        resolve(onReject(this.reason))
                    } catch (e) {
                        reject(e)
                    }
                })
            }
        })
        return promise
    }

    catch(onReject) {
        return this.then(null, onReject)
    }
    
    static all(promiseArr) {
        return new Promise((resolve, reject) => {
            let res = []
            let length = promiseArr.length
            let count = 0
            promiseArr.forEach((promise, index) => {
                promise.then(value => {
                    res[index] = value
                    count++
                    if (count === length) {
                        resolve(res)
                    }
                }, (reason) => {
                    reject(reason)
                })
            })
        })
    }


    static race(promiseArr) {
        return new Promise((resolve, reject) => {
            promiseArr.forEach((promise) => {
                promise.then(value => {
                    resolve(value)
                }, reason => {
                    reject(reason)
                })
            })
        })
    }
}

}

function resolvePromise(promise, x, resolve, reject) {
    if (x instanceof Promise) {
        x.then((data) => {
            resolvePromise(promise, data, resolve, reject)
        }, (reason) => {
            reject(x)
        })
    } else {
        resolve(x)
    }
}


// example 

let p1 = new Promise((resolve,reject)=>{
    setTimeout(() => {
        console.log(1);
        resolve(1)
    }, 3000)
})
let p2 = new Promise((resolve,reject)=>{
    setTimeout(() => {
        console.log(2);
        resolve(2)
    }, 2000)
})
let p3 = new Promise((resolve,reject)=>{
    setTimeout(() => {
        console.log(3);
        resolve(3)
    }, 1000)
})

Promise.all([p1, p2, p3])
.then(console.log)
.catch(console.error)

```

### AJAX

``` javascript
let xhr = new XMLHttpRequese()
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4) {
        
    }
}

xhr.onprogress = function (event) {
    if (event.lengthComputable) {
     	var complete = (event.loaded / event.total * 100 | 0);
      	progress.value = complete;
    }
}
xhr.open('get', '/getInfo')
// 设置请求头
xhr.setRequestHeader()
// 超时控制
xhr.timeout = 
xhr.ontimeout = function () {}
xhr.send()
```

##### 回调函数封装

``` javascript
const Ajax = ({
    method = 'get',
    url = '/',
    data,
    async = true
}, callback) => {
    let xhr = new XMLHttpRequest()
    xhr.onreadystatechange = () => {
        if (xhr.readyState === 4 && xhr.status === 200) {
            let res = JSON.parse(xhr.responseText)
            callback(res)
        }
    }
    xhr.open(method, url, async)
    if (method === 'get') {
        xhr.send()
    }
    if (method === 'post') {
        let type = typeof data
        let header
        if (type === 'string') {
            header = 'application/x-www-form-urlencoded'
        }
        else {
            header = 'application/json'
            data = JSON.stringify(data)
        }
        xhr.setRequestHeader('Content-type', header)
        xhr.send(data)
    }
}

Ajax.get = (url, callback) => {
    return Ajax({
        url
    }, callback)
}

Ajax.get('http://localhost:3000/getData', (res) => {
    console.log(res)
})
```

##### Promise封装

``` javascript
const Ajax = ({
    method = 'get',
    url = '/',
    data,
    async = true
}) => {
    return new Promise((resolve, reject) => {
        let xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
            if (xhr.readyState === 4 && xhr.status === 200) {
                let res = JSON.parse(xhr.responseText)
                resolve(res)
            }
        }
        xhr.open(method, url, async)
        if (method === 'get') {
            xhr.send()
        }
        if (method === 'post') {
            let type = typeof data
            let header
            if (type === 'string') {
                header = 'application/x-www-form-urlencoded'
            }
            else {
                header = 'application/json'
                data = JSON.stringify(data)
            }
            xhr.setRequestHeader('Content-type', header)
            xhr.send(data)
        }
    })
}

Ajax.get = (url) => {
    return Ajax({
        url
    })
}



Ajax.get('http://localhost:3000/getData')
    .then((data) => {
        console.log(data)
    })
```

##### Generator封装

``` javascript
const Ajax = ({
    method = 'get',
    url = '/',
    data,
    async = true
}) => {
    return new Promise((resolve, reject) => {
        let xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
            if (xhr.readyState === 4 && xhr.status === 200) {
                let res = JSON.parse(xhr.responseText)
                resolve(res)
            }
        }
        xhr.open(method, url, async)
        if (method === 'get') {
            xhr.send()
        }
        if (method === 'post') {
            let type = typeof data
            let header
            if (type === 'string') {
                header = 'application/x-www-form-urlencoded'
            }
            else {
                header = 'application/json'
                data = JSON.stringify(data)
            }
            xhr.setRequestHeader('Content-type', header)
            xhr.send(data)
        }
    })
}

Ajax.get = (url) => {
    return Ajax({
        url
    })
}


function* use() {
    let data = yield Ajax.get('http://localhost:3000/getData')
    console.log(data)
}

let obj = use()
obj.next().value.then((res) => {
    obj.next(res)
})
```

##### Generator + Co 封装

``` javascript
const Ajax = ({
    method = 'get',
    url = '/',
    data,
    async = true
}) => {
    return new Promise((resolve, reject) => {
        let xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
            if (xhr.readyState === 4 && xhr.status === 200) {
                let res = JSON.parse(xhr.responseText)
                resolve(res)
            }
        }
        xhr.open(method, url, async)
        if (method === 'get') {
            xhr.send()
        }
        if (method === 'post') {
            let type = typeof data
            let header
            if (type === 'string') {
                header = 'application/x-www-form-urlencoded'
            }
            else {
                header = 'application/json'
                data = JSON.stringify(data)
            }
            xhr.setRequestHeader('Content-type', header)
            xhr.send(data)
        }
    })
}

Ajax.get = (url) => {
    return Ajax({
        url
    })
}



function* use() {
    let data = yield Ajax.get('http://localhost:3000/getData')
    console.log(data)
}

co(use)
```

Co模块的原理类似如下

``` javascript
function co(gen){
  var g = gen();

  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data){
      next(data);
    });
  }

  next();
}
```

##### Async封装

``` javascript
const Ajax = ({
    method = 'get',
    url = '/',
    data,
    async = true
}) => {
    return new Promise((resolve, reject) => {
        let xhr = new XMLHttpRequest()
        xhr.onreadystatechange = () => {
            if (xhr.readyState === 4 && xhr.status === 200) {
                let res = JSON.parse(xhr.responseText)
                resolve(res)
            }
        }
        xhr.open(method, url, async)
        if (method === 'get') {
            xhr.send()
        }
        if (method === 'post') {
            let type = typeof data
            let header
            if (type === 'string') {
                header = 'application/x-www-form-urlencoded'
            }
            else {
                header = 'application/json'
                data = JSON.stringify(data)
            }
            xhr.setRequestHeader('Content-type', header)
            xhr.send(data)
        }
    })
    
}

Ajax.get = (url) => {
    return Ajax({url})
}


async function use() {
    let data = await Ajax.get('http://localhost:3000/getData')
    console.log(data)
}

use()
```

### fetch

``` javascript
fetch(url, options).then(function(response) { 
// handle HTTP response
	 if(response.status!==200){
            console.log("存在一个问题，状态码为："+response.status);
            return;
        }
        //检查响应文本
        response.json().then(function(data){
            console.log(data);
        });
}, function(error) {
 // handle network error
})
```

##### XHR（AJAX）和Fetch的区别

1. AJAX同源默认携带Cookie，不同源则默认不会携带Cookie。如果使用CORS进行跨域的AJAX请求时，若想带上Cookie，则应该同时在客户端和服务端进行设置

   1. 客户端

      ``` javascript
      var xhr = new XMLHttpRequest()
      xhr.withCredentials = true
      ```

   2. 服务端，设置响应头部

      ``` http
      Access-Control-Allow-Credentials: true
      ```

   Fetch的credentials属性，默认值为same-origin，想要跨域发送Cookie则设置为include

   - `omit`: 从不发送cookies.
   - `same-origin`: 只有当URL与响应脚本同源才发送 cookies、 HTTP Basic authentication 等验证信息.(浏览器默认值,在旧版本浏览器，例如safari 11依旧是omit，safari 12已更改)
   - `include`: 不论是不是跨域的请求,总是发送请求资源域在本地的 cookies、 HTTP Basic authentication 等验证信息.

   这一点来看ajax和fetch是相同的。

2. fetch不支持abort，也不支持超时控制（timeout）

   我们如何实现**fetch的超时控制**

   ``` javascript
   Promise.race([
       fetch(url),
       new Promise((resolve, reject) => {
           setTimeout(() => reject(new Error("request timeout")), 2000)
       })
   ])
   .then(data => {}) // 请求成功
   .catch(reason => {}) // 请求失败
   ```

3. fetch无法检测请求的进度(onprogress)

### axios

``` javascript
axios({
    method: 'post',
    url: '/user/12345',
    data: {
        firstName: 'Fred',
        lastName: 'Flintstone'
    }
})
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});
```

## Node.js

### Http模块

``` javascript
const http = require('http')
const server = http.createServer((req, res) => {
    console.log(req.url) // 请求url
    console.log(req.method) // 请求方法
    console.log(req.headers) // 请求头部
    
    if (req.url === '/') {
        // 设置响应的状态码和头部
        res.writeHead(200, {'Content-Type': 'text/plain; charset=utf-8'})
        
        // 单独设置状态码
        res.statusCode = 200
        // 单独设置响应头部
        res.setHeader('Content-Type', 'text/plain; charset=utf-8')
        res.setHeader('Set-Cookie', 'name=akara; secure')
        
        // 设置响应实体
        res.write("hello world")
        res.write("!!!")
        // 发送响应报文
        res.end()
    }
    
})

server.listen(3000, () => {
    console.log("服务器跑在3000端口")
})
```

##### 静态目录

``` javascript
const http = require('http')
const server = http.createServer((req, res) => {
    if (req.url === '/') {
        // ...
    }
    else {
        let filePath = path.join(__dirname, 'static', url.pathname)
        try {
            let file = await fs.readFileAsync(filePath)
            res.statusCode = 200
            res.end(file)
        } catch (error) {
            res.statusCode = 404
            res.end("404 Not Found")
        }
    }
}).listen(3000)
```

##### 处理Post请求（文件上传）

``` javascript
const http = require('http')
const fs = require('fs')
const server = http.createServer((req, res) => {
    if (req.url === '/upload') {
        
        let segment = []
        
        req.on('data', (chunk) => {
            // chunk为Buffer对象
            // 字符串aaa=bbb对应的Buffer对象如下
            // <Buffer 61 61 61 3d 62 62 62> 
            segment.push(chunk)
        })
        
        req.on('end', () => {
            // 文件上传代码
            segment = Buffer.concat(segment)
            // 下方代码获取buffer转成的字符串
            // segment = Buffer.concat(segment).toString()
            fs.writeFile('fileName', segment, (err) => {
                res.writeHead(200, {'Content-Type': 'text/plain; charset=utf-8'})
                res.write("文件上传成功！")
                res.end()
            })
        })
    }
})

server.listen(3000, () => {
    console.log("服务器跑在3000端口")
})
```

### fs模块

``` javascript
const fs = require('fs')
```

##### readFile

``` javascript
fs.readFile('./image.png', (err, buffer) => {
    if (err) throw err
 
})
```

##### writeFile

``` javascript
// 写入文本
fs.writeFile('index.txt', 'hello world', 'utf8')
// 写入buffer
fs.writeFile('image.png', buffer)
```

##### createReadStream

##### createWriteStream

``` javascript
const reader = fs.createReadStream(data.path)
const stream = fs.createWriteStream(`./image/${Math.floor(Math.random() * 10000)}.jpg`)
reader.pipe(stream)
```

### Path模块

``` javascript
const path = require('path')
```

##### __dirname

返回当前文件所在的绝对路径

#####path.resolve

将一个路径解析成绝对路径

``` javascript
const path1 = path.resolve('static')
console.log(path1)
// 输出
C:\Users\Messiah\test\static
```

##### path.join

用平台特定的分割符号(Linux为`/`， Window为`\`)对路径进行拼接

``` javascript
const path1 = path.join(__dirname, 'a')
const path2 = path.join(__dirname, 'a/b')
const path3 = path.join('/a', 'b', 'c/d', 'e', '..')

console.log(path1)
console.log(path2)
console.log(path3)
// 输出
C:\Users\Messiah\test\a
C:\Users\Messiah\test\a\b
\a\b\c\d

const path4 = path.join(__dirname, '/static')
const path5 = path.join(path.resolve('.'), '/static')
```

### url模块

``` javascript
// 当请求url为 http://localhost:3000/index.html?name=akara#aa
const url = require('url')
let {
    search, // '?name=akara'
    query, // 'name=akara'
    pathname, // '/index.html'
    path, // '/index.html?name=akara'
} = url.parse(req.url)
```

### querystring模块

``` javascript
const qs = require('querystring')
var str = 'foo=bar&abc=xyz&abc=123';

querystring.parse(str)
// { foo: 'bar', abc: [ 'xyz', '123' ] }
```

### Event模块

> 实现原理见本文的设计模式-发布订阅章节

``` javascript
var EventEmitter = require('events').EventEmitter
var emitter = new EventEmitter()

emitter.on('ev', function () {
    
})

emitter.emit('ev')
```

### Bluebird库

可以将回调函数实现的异步改写成Promise的方式来写

##### Bluebird + fs

回调

``` javascript
const fs = require('fs')
fs.readFile('index.html', (err, data) => {
    response.end(data)
})
```

Promise

``` javascript
const bluebird = require('bluebird')
const fs = bluebird.promisifyAll(require('fs'))

fs.readFileAsync('index.html')
.then(data => {
    response.end(data)
})
```

##### Bluebird + mysql

回调

``` javascript
const mysql = require('mysql')
// mysql配置文件
let config = require('./config')
conn.connect()

// 使用
conn.query(`sql code here...`, (err, data) => {
    
})
```

Promise

``` javascript
const bluebird = require('bluebird')
const mysql = require('mysql')
// mysql配置文件
let config = require('./config')
const conn = bluebird.promisifyAll(config)
conn.connect()

// 使用
let data = await conn.queryAsync(`sql code here...`)
```

## Koa

##### 使用

``` javascript
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
});


// response
app.use(ctx => {
  ctx.body = 'Hello Koa';
});

app.listen(3000);
```

##### 核心实现

``` javascript
const Emitter = require('events')
// 三个对象，提前定义好原型的方法
const context = require('./context')
const request = require('./request')
const response = require('./response')
class Koa extends Emitter {
    constructor() {
        super()
        this.middleware = []
        this.context = Object.create(context)
        this.request = Object.create(request)
        this.response = Object.create(response)
    }

    callback() {
        const fn = compose(this.middleware)
        return (req, res) => {
            const ctx = this.createContext(req, res)
            return this.handlerRequest(ctx, fn)
        }
    }

    use(fn) {
        if (typeof fn !== 'function') throw new TypeError('middleware must be a function!')
        this.middleware.push(fn)
        return this
    }

    listen(...args) {
        const server = http.createServer(this.callback())
        return server.listen(...args)
    }

    createContext(req, res) {
        // 其实就是根据已有的req和res创建上下文context
        const context = Object.create(this.context);
        const request = Object.create(this.request);
        const response = Object.create(this.response);
        context.request = request
        context.response = response
        context.app = request.app = response.app = this;
        // 重点，挂载req和res
        context.req = request.req = response.req = req;
        context.res = request.res = response.res = res;
        // 互相引用
        request.ctx = response.ctx = context;
        request.response = response;
        response.request = request;
        return context
    }

    handlerRequest(ctx, fn) {
        const res = ctx.res
        res.statusCode = 404
        fn(ctx).catch(reason => {
            console.log(reason)
        })
    }
}
```

Koa的实例app有三个公共的API

- use

  ``` javascript
  app.use((ctx, next) => {
      
  })
  ```

  use方法用于将参数中间件放进app的middleware数组里

- listen

  ``` javascript
  app.use(3000)
  ```

  等价于

  ``` javascript
   const server = http.createServer(this.callback())
   server.listen(3000)
  ```

- callback

  该函数内部实现三个功能

  1. 使用koa-compose函数将middleware中间件数组转化为中间件fn

  2. 调用app.createContext函数。创建context，request，response对象；将request和response挂载在context上；把req和res挂载在三个对象上。

     例如：request的原型对象上部分代码如下

     ``` javascript
     get header() {
     	return this.req.headers;
     },
     set header(val) {
     	this.req.headers = val;
     },
     ```

     我们现在就可以根据`ctx.request.header`获取req的headers了

  3. 执行handleRequest函数，本质是把组装好的context传入中间件fn执行

Koa源码中使用到了Koa-compose， 用于将多个中间件函数组合为一个中间件函数

##### Koa-compose

``` javascript
const compose = (middleware) => {
    if (!Array.isArray(middleware)) throw new TypeError("Middleware stack must be an array!")
    for (const fn of middleware) {
        if (typeof fn !== 'function') throw new TypeError("Middleware must be composed of functions!")
    }
    let length = middleware.length
    return function (ctx, next) {
        let index = -1
        return dispatch(0)
        function dispatch(i) {
            // 一个中间件内部多次调用next时，index大于等于i
            if ( index >= i) {
                return Promise.reject(new Error('next() called multiple times'))
            }
            let fn
            index = i
            if (i < length) {
                fn = middleware[i]
            }
            else if (i === length) {
                // 重点， 外部compose的next传进内部compose
                fn = next
            }
            // 最后一个中间件调用next时，什么也不做
            if (!fn) return
            // 官方源码使用Promise是为了使用async中间件，不过这里没有怎么实现这个功能，就一个样子
            return Promise.resolve(fn(ctx, dispatch.bind(null, (i + 1))))
        }
    }
}
```

### Koa-router

##### 使用

``` javascript
const Router = require('koa-router')
const router = new Router()
router
  .get('/', (ctx, next) => {
    ctx.body = 'Hello World!';
  })
  .post('/users', (ctx, next) => {
    // ...
  })
  .put('/users/:id', (ctx, next) => {
    // ...
  })
  .del('/users/:id', (ctx, next) => {
    // ...
  })
  .all('/users/:id', (ctx, next) => {
    // ...
  });
app.use(router.routes())
app.use(router.allowedMethods()) // 此处例子没有实现该方法
```

##### 简易实现

简易实现，只实现一个get方法，实际上要更复杂的多。

``` javascript
class Router {
    constructor() {
        this.stack = []
    }

    get(url, fn) {
        function middleware(ctx, next) {
            if (ctx.req.method.toLowerCase() === 'get' && ctx.req.url === url) {
                console.log('路由匹配成功');
                fn(ctx, next)
            }
            else {
                console.log('路由匹配失败');
                next()
            }
        }
        this.stack.push(middleware)
        return this
    }

    routes() {
        return (ctx, next) => {
            let fn = compose(this.stack)
            // 必须加上next参数
            // koa本身有一个compose， 这里也有一个，所以要把外部的next传给内部
            fn(ctx, next)
        }
    }
}
```



## React

##### JSX

JSX是JavaScript的一种语法扩展，JSX可以生成React元素。

``` jsx
const element = <h1>hello, world!</h1>
```

实际上Babel会把JSX转译为`React.createElement()`函数调用，下面代码是一样的。

``` jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```react
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

**这也是为什么你必须引入React库，因为使用JSX就需要React库。**

`React.createElement()`会创建这样的对象，也叫做React元素，其实就是**虚拟DOM**

``` javascript
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

##### 组件

组件名一定要大写，因为在JSX中小写的会当成html标签。

- <todo />  编译为 React.createElement('todo')
- <Todo />  编译为 React.createElement(Todo)

React的组件分为**函数组件**和**class组件**

**函数组件**没有内部的**状态**，也没有**事件**，也没有**生命周期**。

``` javascript
// 函数组件
function Hello(props) {
    return (
        <div>
        	// 函数组件使用props
            hello world {props.name}
        </div>
    )
}
```

**Class组件**则拥有**状态**，**事件**，**生命周期**

``` js
class Count extends React.Component {
    constructor(props) {
        super(props)
    }

    render() {
        return (
            <div>
            	// class组件使用props
                {this.props.count}
            </div>
        )
    }
}


class App extends React.Component {
    constructor(props) {
        super(props)
        // 根组件的状态
        this.state = {
            count: 0,
            name: 'akara'
        }
    }
    
    render() {
        return (
            <div>
                {this.state.count}
                <button onClick={this.handlerClick}>click me</button>
				// 根组件传值给子组件的props
                <Hello name={this.state.name}/>
                <Count count={this.state.count}/>
            </div>
        )
    }
    
    // 根组件的方法，已绑定this
    handlerClick = () => {
        this.setState({
            count: this.state.count + 1
        })
    }
    
    
}
```

##### 事件处理

传统的HTML使用的纯小写`onclick`，React使用的驼峰式`onClick`

``` html
// html
<button onclick='func'></button>
```

``` react
// react
<button onClick={activateLasers}>
	Activate Lasers
</button>
```

传统的HTML可以通过`return false`来阻止默认行为，React不行，必须使用`event.preventDefault`

``` html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

``` react
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

React中的e为**合成事件**，因此无需担心浏览器的兼容性问题。

当我们使用`onClick={this.handleClick}`时，我们需要给handleClick绑定this。

1. ``` react
   constructor() {
       this.handlerClick = this.handlerClick.bind(this)
   }
   ```

2. 使用箭头函数

   ``` react
    <button onClick={(e) => this.handleClick(e)}>
       Click me
     </button>
   ```

3. ``` javascript
   // 实验性语法
   // Create-React-App 默认支持
   class Btn extends React.component {
       handlerClick = (e) => {
           console.log(this)
       }
       
       render() {
           return (
           	 <button onClick={this.handlerClick}>
                   Click me
                </button>
           )
       }
   }
   ```

##### [组件生命周期](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

###### 挂载

- **constructor()**
- static getDerivedStateFromProps()
- **render()**
- **componetDidMount()**

###### 更新

- static getDerivedStateFromProps()
- shouldComponentUpdate()
- **render()**
- getSnapshotBeforeUpdate()
- **componetDidUpdate()**

###### 卸载

- **componentWillUnmount()**

##### setState

**不要直接修改State**

``` react
// Wrong
// 此代码不会重新渲染组件
this.state.comment = 'Hello';
```

而是应该使用`setState()`

``` react
// Correct
this.setState({comment: 'hello'})
```

**State的更新可能/通常是异步的**

``` react
class App ..{
    ..() {
        this.state = {
            count: 0
        }
    }
    
    ..() {
        this.setState({
        	count: 1
    	})
        console.log(this.state.count) // 输出0
    }
}
```

想要获取修改后的值，我们可以传一个回调函数给setState

``` react
this.setState({
    count: 1
}, () => {
    console.log(this.state.count) // 输出1
})
```

如果setState依赖于之前的state，如

``` react
this.setState({
    count: 1
}, () => {
    console.log(this.state.count);
})

this.setState({
    count: this.state.count + 1
})
```

由于setState是异步的，那么第二个setState中获取到的`this.state.count`为初始的0

为了解决这个问题，setState的第二个参数可以设置为函数

``` react
this.setState({
    count: 1
}, () => {
    console.log(this.state.count);
})

this.setState((state, props) => {
    return {
        count: state.count + 1
    }
})
```



**setState何时是异步的**

一句话描述，在**合成事件**和**组件的生命周期**中`setState`是异步的；在**原生事件**和**定时器**中`setState`是同步的。

React内部维护了一个标识`isBatchingUpdates`，当这个值为`true`表示把state缓存进队列，最后进行批量更行；当这个值为`false`表示直接进行更新。

**合成事件**和**组件的生命周期**中，会把`isBatchingUpdates`设置为true

**原生事件**和**定时器**中，会把`isBatchingUpdates`设置为false



##### 条件渲染

&&运算符

``` jsx
function App(props) {
    return (
    	<div>
        	{ props.count > 0 &&
            	<span>hello world</span>
            }
        </div>
    )
}
```

三目运算符

``` react
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```

阻止组件渲染

``` react
function App(props) {
    if(props.flag) return null
    ...
}
```

##### 列表渲染

``` react
{
    props.todos.map((todo) => {
        return <Todo todo={todo} key={todo.id}/>
    })
}
```

##### 表单

``` react
class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            value: ''
        }
    }
    handleChange = (e) => {
        this.setState({
            value: e.target.value
        })
    }
    render() {
        return (
            <div>
                {this.state.value}
                <input type="text" value={this.state.value} onChange={this.handleChange}/>
            </div>
        )
    }
}
```

**受控组件**

React的state成为组件/表单的唯一数据源，渲染表单的组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。当然，与之对应的成为“非受控组件”。

我们可以把用户输入的小写字符转化为大写

``` react
handleChange = (e) => {
    this.setState({
        value: e.target.value.toUpperCase()
    })
}
```

##### Refs

何时使用 Refs

- 管理焦点，文本选择或媒体交换
- 继承第三方DOM库
- 触发强制动画

使用refs

``` javascript
class App extends React.Component {
    constructor(props) {
        super(props)
        // 创建
        this.myRef = React.createRef()
    }

    handleClick = () => {
        this.myRef.current.focus()
    }

    render() {
        return (
            <div>
            	// 绑定
                <input type="text" ref={this.myRef} />
                <button onClick={this.handleClick}>点我</button>
            </div>
        )
    }
}
```









### Hook

Hook 是一个特殊的函数，它可以让你“钩入” React 的特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

##### useState

``` javascript
import React, { useState } from 'react'
function example(props) {
    let [count, setCount] = useState(0) // 初始值0
    return (
    	<div>
        	{ count }
            <button onClick={() => setCount(count + 1)}>
            	Click me
          	</button>
        </div>
    )
}
```

##### useEffect

*Effect Hook* 可以让你在函数组件中执行副作用操作

``` javascript
import React, {
    useState,
    useEffect,
} from 'react';

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

`useEffect` 会在每次渲染后都执行，包括初次渲染和每次数据更新之后。

`useEffect`可以返回一个函数来清除副作用

``` javascript
useEffect(() => {
    // 运行副作用
    ChatAPI.subscribe()
    // 清除副作用
    return () => {
        ChatAPI.unsubscribe()
    }
})
```

组件挂载时，运行副作用（effect）。

组件更新时，先清除上一个effect，再运行下一个effect

组件卸载时，清除最后一个effect

``` javascript
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

``` javascript
// Mount with { friend: { id: 100 } } props
ChatAPI.subscribeToFriendStatus(100, handleStatusChange);     // 运行第一个 effect

// Update with { friend: { id: 200 } } props
ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // 清除上一个 effect
ChatAPI.subscribeToFriendStatus(200, handleStatusChange);     // 运行下一个 effect

// Update with { friend: { id: 300 } } props
ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // 清除上一个 effect
ChatAPI.subscribeToFriendStatus(300, handleStatusChange);     // 运行下一个 effect

// Unmount
ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // 清除最后一个 effect
```

##### useRef

``` javascript
import React, { useRef } from 'react'

function App(props) {
    let refs = useRef(null)
    return (
    	<input ref={refs}>
    )
}
```



### Redux

**基本原理**

``` javascript
import { createStore } from 'redux'
// reducer 是个函数，参数为state（设置初始值）和action，返回值为新的state
const reducer = (state = 0, action) => {
    switch(action.type) {
    	case 'add':
        	return state + action.payload
    	case 'delete':
        	return 	state - action.payload
      	default:
        	return state
    }
}
// 生成store
let store = createStore(reducer)

// 获取状态
store.getState()
// action是个对象，通常有type属性
const action1 = {
	type: 'add',
	payload: 2,
}
// 我们也可以用一个action生成函数
const createAction = (val) => ({
	type: 'add',
	payload: val
})

// 使用store.dispatch(action)来分发action
store.dispatch(action1)
store.dispatch(createAction(111))


// store还可以订阅监听器
// 当我们dispatch了action后，会触发函数
store.subscribe(() => {
	console.log('change state')
})
```

**createStore的简单实现**

``` javascript
const createStore = (reducer) => {
    let state
    let listeners = []
    const getState = () => {
        return state
    }

    const dispatch = (action) => {
        state = reducer(state, action)
        listeners.forEach(listener => listener())
    }

    const subscribe = (listener) => {
        listeners.push(listener)
        return () => {
            listeners = listeners.filter((l) => l !== listener)
        }
    }

    dispatch()

    return {
        getState,
        dispatch,
        subscribe,
    }
}
```



**实战例子**

```javascript
// action.js
export const CHANGE_CHANNEL = 'CHANGE_CHANNEL'
// action 生成函数
export const changeChannel = (channel) => ({
    type: CHANGE_CHANNEL,
    channel
})


```

``` javascript
// reducers.js
import {
	CHANGE_CHANNEL
} from './action.js'
// combineReducers用来分隔reducer
import { combineReducers } from 'redux'

const channel = (state = "nintendo", action) => {
    switch(action.type) {
        case CHANGE_CHANNEL:
            return action.channel
        default:
            return state
    }
}

const name = (state = "test", action) => {
	return state
}

const rootReducer = combineReducers({
	channel,
  	name,
})


export default rootReducer
```

``` javascript
// index.js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider } from 'react-redux'
import reducer from './reducers.js'
import App from './app.js'
let store = createStore(reducer)

ReactDOM.render(
  	// 把store注入进组件
	<Provider store={store}>
        <App />
    </Provider>,
    document.querySelector('#root')
)

```



``` javascript
// app.js
import { connect } from 'react-redux'
import {
	changeChannel
} from './action.js'
const App = ({
	channel,
  	handlerClick,
}) => {
	return (
    	<div>
      		<span>{channel}</span>
      		<button onClick={handlerClick}>Click me</button>
      	</div>
    )
}

// 本质是运用的高阶组件，根据输入的容器组件APP生成UI组件
// mapStateToProps 把状态树中的状态映射进组件的props
const mapStateToProps = (state) => {
    return {
      	// 组件的props 和 状态树中的state.channel对应
        channel: state.channel
    }
}

// mapDispatchToProps 把Dispatch方法映射为组件中props的方法
const mapDispatchToProps = (dispatch) => {
    return {
        handlerClick: (value) => {
            dispatch(changeChannel(value))
        }
    }
}
// mapDispatchToProps 也可以是个对象
const mapDispatchToProps = {
  	// 这里的函数是个action creator
	handlerClick: () => {
		type: 'add'
    }
}

export default connect(
	mapStateToProps,
	mapDispatchToProps,
)(App)

// connect本质是高阶组件，我们可以使用react-redux自带的hook代替connect
import { useSelector, useDispatch } from 'react-redux' // 应该放开头，这里为了方便把import放在这里
// 使用起来很简单
const App = () => {
	const channel = useSelector(state => state.channel)
    const postsByChannel = useSelector(state => state.postsByChannel)
    
    return (
    	<div>
    		<div>...</div>
      	</div>
    )
}
```



### React-Router

安装

``` javascript
import React from 'react'
import {
    BrowserRouter as Router,
    Link,
    Switch,
    Route
} from 'react-router-dom' // web端用react-router-dom

function App () {
    return (
    	<Router>
        	<Link to='/'>首页</Link>
        	<Link to='/blog'>博客</Link>
            <Link to='/about'>关于我</Link>
        
        	<Switch>
        		<Route path='/about'>
        			<About />
        		</Route>
        		<Route path='/blog'>
        			<Blog />
        		</Route>
        		<Route path='/'>
        			<Home />
        		</Route>
        	</Switch>
        </Router>
    )
}


```

##### 动态路由匹配

``` javascript
import {
    ...
    useParams,
} from 'react-router-dom'

<Switch>
    <Route path='/user/:id'>
    	<User />    
    </Route>
</Switch>


function User() {
    let { id } = useParams()
    
    return (
    	<div>
        	user: { id }
        </div>
    )
}
```



## Vue

``` vue
<div class='app'>
	{{ count }}
    <button @click='increment'>
        点我加一
    </button>
</div>
<script>
	const vm = new Vue({
        el: '.app',
        data: {
            count: 0
        },
        methods: {
            increment: function () {
                this.count++
            }
        }
    })
</script>
```

##### 指令

- `v-bind` 缩写为`:`
- `v-on` 缩写为`@`
- `v-show`
- `v-if`
- `v-else-if`
- `v-else`
- `v-for`
- `v-model`
- `v-text` ：等价于`{{}}`
- `v-html`
- `v-once`

##### $属性

- `el`
- `data`
- `computed`
- `methods`
- `template`
- `store`
- `router`

##### 生命周期

- beforeCreate
- created
- beforeMount
- mounted
- beforeUpdate
- updated
- beforeDestory
- destoryed

### Vuex

``` javascript
const store = new Vuex.Store({
    state: {
        count: 0
    },

    getters: {
        countPlus: state => {
            return state.count + 1
        }
    },

    mutations: {
        increment: (state, payload) => {
            state.count += payload
        }
    }
})
new Vue({
    el: '.app',
    store,
    computed: {
        count: function() {
            return this.$store.state.count
        }
    },
    methods: {
        increment: function() {
            this.$store.commit('increment', 10)
        }
    },
    template: `
        <div>
            {{ count }}
            <button @click='increment'>点我</button>
        </div>
    `
})
```

##### mutation 和 action

action类似于mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作，Mutation只能包括同步操作。

### Vue Router

``` html
<div class="app">
    <router-link :to="/login"></router-link>
    <router-link :to="/logout"></router-link>
    <router-view></router-view>
</div>
```

``` javascript
const routes = [
    {
        path: '/login',
        component: { template: '<login></login>' }
    },
    {
        path: '/logout',
        component: { template: '<logout></logout>' }
    },
]

const router = new VueRouter({
    routes
})

const vm = new Vue({
    el: '.app',
    router
})
```

##### 动态路由匹配

如同/user/:id，/user/akara和/user/messiah都可以匹配到这个路由

``` javascript
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

##### 嵌套路由

简单来说就是router-view里面还有router-view

``` html
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

``` javascript
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

##### 路由导航

`router-link`提供了声明式导航，我们也可以使用`this.$router.push`进行函数式导航

``` javascript
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

##### Hash模式和History模式

Hash模式的Url结构类似：`https://example.com/#user/akara`

History模式的Url结构类似：`https://example.com/user/akara`

无论哪种模式，本质都是使用的`history.pushState`，每次pushState后，会在浏览器的浏览记录中添加一个新的记录，但是并**不会触发页面刷新**，也**不会请求新的数据**。

```js
// 使用history模式
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

不过使用History模式需要后端进行配置，如果不配置，当用户访问 `https://example.com/user/akara`的时候会返回404。所以我们需要设置当URL匹配不到任何资源的时候，同返回同一个`index.html`。

##### 导航卫士



### Vue双向绑定的原理

Vue是通过数据劫持结合发布-订阅模式的方式，实现的双向绑定。通过Object.defineProperty()来劫持属性的，使用属性的时候触发getter函数，收集依赖；修改属性的时候触发setter函数，触发相应的回调。

1. Observer 对数据的属性进行递归遍历，使用Object.defineProperty进行数据劫持。
2. Compiler 用于将模板编译为渲染函数，并渲染视图页面
   1. parse使用正则等方式解析template中的指令，class，style等数据，生成AST（抽象语法树）
   2. optimize进行优化，标记静态节点，该节点会跳过diff
   3. generate，把AST转化为渲染函数，渲染函数用于生成虚拟DOM
3. Watcher 是Observer和Compiler之间通信的桥梁
   1. 自身实例化的时候，调用getter函数，向deps添加watch
   2. 当数据修改时，调用getter函数，调用deps.notify，执行watch的update函数
   3. 执行watch的update函数，重新生成虚拟DOM，并进行Diff对页面进行修改

##### 流水线的解释

当我们使用`new Vue()  `生成Vue实例的时候，先会调用Vue._init 进行初始化。

1. 初始化生命周期，事件（以及initRender）

2. 调用BeforeCreate生命周期函数

3. 初始化数据（以及initInjections）

   1. 使用Object.defineProperty对data的属性进行数据劫持
   2. 当数据被渲染进页面时，调用get函数，把属性的Watcher放进dep内部的数组内
   3. 当数据被修改时，调用set函数，调用dep的notify方法，从而调用dep内部数组内所有Watcher的update方法

4. 调用Created生命周期函数

5. 查看有没有el参数，没有的话当vm.$mount()调用时进入下一步

6. 查看有没有template参数，有的话则把template转化成渲染函数，没有的话把el的outerHTML转化为渲染函数，渲染函数生成虚拟DOM

   1. parse用正则等方式解析template中的指令，class，style等数据，生成AST（抽象语法树）
   2. optimize用来标记静态节点，之后diff算法中就会跳过静态节点的对比
   3. generate把AST转化为渲染函数

7. 调用beforeMount生命周期函数

8. 利用虚拟DOM生成真实DOM并挂载在el元素上

9. 调用Mounted生命周期函数

   数据改变时

   1. 调用beforeUpdate生命周期函数

   2. 数据改变时，调用所有监听对应属性的Watcher的update函数，这个函数会把Watcher放进一个队列中，等到下一个tick时才取出。从而实现异步更新DOM。

   3. 重新生成虚拟DOM，并对新老VDom进行patch（patch的核心是diff算法）处理 

      1. 如果oldVnode不存在，不存在则直接根据newVnode新建节点

      2. 调用sameVnode对oldVnode和newVnode进行比较，只有当key， tag， isComment都相同，同时定义或同时未定义data， 或者两个都是input且type相同时才是sameVnode。那么就对两个VNode进行patchVnode操作

         1. 如果新老VNode都是静态的，且key值相同，并且新的VNode标记了v-once或是clone，则只需替换ele和componentsinstance
         2. 新老VNode都有children，则使用updateChildren对子节点进行diff

            1. 对于oldVnode的children，用oldCh表示。对于newVnode的children，用newCh表示


            2. 首先定义 oldStartIdx、newStartIdx、oldEndIdx 以及 newEndIdx 分别是新老两个 children 的两边的索引，同时 oldStartVnode、newStartVnode、oldEndVnode 以及 newEndVnode 分别指向这几个索引对应的VNode 节点。
            3. while循环，循环中oldStartIdx和oldEndIdx不断靠拢，newStartIdx和newEndIdx也不断靠拢。
            4. 比较，oldStartVnode和newStartVnode，oldEndVnode和newEndVnode   ，  oldStartVnode和newEndVnode  ， oldEndVnode和newStartVnode。如果两个是sameVnode则进行patchVnode, 不是就进行下一个的比较
            5. 如果以上四次比较都不是Vnode，那么找oldCh有没有和newStartVnode是sameVnode的节点
               1.  如果设置了key，直接通过newStartVnode的key查看有没有key相同的Vnode
               2. 如果没有key，则通过循环，一个个的调用sameVnode函数比较。（体现了**key能够提高diff算法的效率**）
               3. 如果找不到相同的Vnode，则新建一个Vnode
            6. 循环结束。处理多余的或者不够的真实节点。oldStartIdx > oldEndIdx 新增节点 或者 newStartIdx > newEndIdx 删除节点。
    
         3. 如果oldVnode没有children，newVnode有，则先清空老节点的文本内容，再为DOM加入子节点
    
         4. 如果oldVnode有children，newVnode没有，则删除该节点所有子节点
    
         5. 如果新老节点都没有子节点，替换DOM的文本

10. 调用updated生命周期函数
11. 调用vm.$destroy()
12. 调用beforeDestroy生命周期函数
13. 删除组件（包括watchers和事件监听器等）
14. 调用destroyed生命周期函数



## Vue和React的对比

共同点：

1. 都使用了Virtual DOM
2. 都提供了响应式和组件化的视图组件
3. 将注意力集中保持在核心库，其他功能如路由和全局状态管理交给相关的库

不同：

1. 优化：

   1. React应用中，某个组件的状态发生改变时，它会以组件为根，重新渲染整个组件子树。

      如果要避免不必要的子组件的渲染，需要使用PureComponent或者shouldComponentUpdate方法进行优化

   2. 在Vue应用中，组件的依赖是在渲染过程中自动追踪的，所以系统能精确知晓哪个组件确实需要被重渲染。

2. React中，一切都是JavaScript，html用jsx表示，css也可以纳入js中处理。



## 前端模块化

1. 立即执行函数（IIFE）

   ``` javascript
   // 定义模块
   (function (window) {
       function A() {
           return 'aaa'
       }
       
       function B() {
           return 'bbb'
       }
       
       window.myModule = {A, B}
   })(window)
   
   // 使用模块
   myModule.A()
   ```

2. AMD（需要安装require.js库）

   使用define定义模块，使用require加载模块

3. CommonJS

   1. 使用方法

      ``` javascript
      // 定义模块
      // a.js
      function getName() {
          return 'Akara'
      }
      
      module.exports = getName
      
      // 使用模块
      // b.js
      const getName = require('./a')
      getName() // 'Akara'
      ```

   2. require和module

      require相当于包装了一层立即执行函数

      ``` javascript
      const getName = require('./a')
      // 等价于
      const getName = (function () {
          function getName() {
              return 'Akara'
          }
      
          module.exports = getName
          
          // 返回module.exports
          return module.exports
      })()
      ```

      JS文件有两个全局变量，module和exports，module对象的结构如下

      ``` javascript
      module: {
          id: '.',
          exports: {}
      }
      ```

      module.exports 和 全局变量exports指向同一个对象

      ``` javascript
      module.exports === exports // true
      ```

      所以我们可以

      ``` javascript
      module.exports.a = 111
      // 等价于
      exports.a = 111
      ```

      但是我们不可以

      ``` javascript
      exports = {
          a: 111
      }
      console.log(module.exports === exports) // false
      ```

   3. **在Node中引入模块，会发生什么？**

      在Node中，模块分为两类：一类是node提供的**核心模块**，一类是用于编写的**文件模块**

      - 路径分析

        如果发现引入的是核心模块，则不用进行接下来的两步了，因为核心模块早已编译为二进制，当node进程启动时，部分核心代码已经直接加载进内存中。

      - 文件定位

      - 编译执行
      
       
      
   4. 缓存

      模块在被用require引入后会缓存。

4. UMD （IIFE + AMD + CommonJS）

   用来兼容多套模块系统

5. ES6模块

   想使用ES6模块的`import`和`export`，需要将文件名的后缀改为`.mjs`

   并且使用`--experimental-modules`开启此特性

   ``` javascript
   node --experimental-modules file.mjs
   ```
   
    
   
   CommonJS是运行时加载，ES6模块是编译时加载。
   
   不过在ES6模块里，我们也可以使用import()来实现运行时加载
   
   CommonJS我们即使只想使用库中的一个函数，也会加载全部的代码；ES6模块只会加载我们需要的那个函数。







## 计算机网络相关

### HTTP协议1.1和2.0的区别



### HTTPS的原理



### TCP三次握手和四次挥手



### HTTP缓存

##### HTTP缓存分为强制缓存和协商缓存。

浏览器请求资源时

1. 先判断有没有缓存，没缓存则向服务器发送请求。

2. 若有缓存，根据Cache-Control: max-age 或是 Expires 判断资源是否过期。

   1. 如果没过期，则直接从缓存读取（强制缓存）

   2. 如果过期了

      1. 上次资源的响应是否有Etag头部， 有的话发送请求，请求头为If-None-Match

      2. 没有Etag的话，看上次资源的响应是否有Last-Modified，有的话发送请求，请求头为If-Modified-Since。

      3. 如果两个头部都没有，则向服务器发送请求。

         如果1或2中了，服务器会根据请求头If-None-Match或If-Modified-Since来判断资源是否有改动。

         如果资源有改动，则发送新资源，响应的状态码为200

         如果资源没有改动，则浏览器从缓存中读取资源，响应的状态码为304



### 输入URL之后会发生什么

1. 浏览器解析URL获取协议，域名，端口，路径

2. 查看浏览器是否有资源的缓存

   1. 有。判断是否过期
      1. 没过期。直接读取缓存
      2. 过期。
         1. Etag和If-None-Match
         2. Last-Modify和lf-Modified-Since
         3. 文件修改了则请求（状态码200），没修改则读取缓存（状态码304）
   2. 没有则进行下一步

3. 首先进行DNS解析

   1. 寻找浏览器是否存在缓存，若没有
   2. 寻找操作系统是否存在缓存，若没有
   3. 寻找hosts文件中是否有域名和ip的对应关系，若没有
   4. 查找路由器中是否有缓存
   5. 寻找DNS服务器是否没缓存，若没有
   6. 向根域名服务器发送请求

4. 生成HTTP请求

5. 建立TCP连接，三次握手

   1. 客户端发送一个SYN=1,Seq=X的TCP包
   2. 服务端发回一个SYN=1,ACK=X+1，Seq=Y的TCP包
   3. 客户端发送ACK=Y+1，Seq=Z的TCP包

6. 如果是HTTP请求

   对HTTP报文进行报文分割并标记序号和端口号

7. 如果是HTTPS请求

   1. 将HTTP报文交给TLS处理，TLS和服务端进行TLS握手，交换版本信息，加密算法，压缩算法，随机数（浏览器一个，客户端一个）。
   2. 服务端发送证书，浏览器用CA的公钥对其进行验证。
   3. 浏览器用服务端的公钥加密生成的预备主密码发送给服务端，两个随机数和预备主密码生成主密码
   4. 使用主密码生成对称加密的密钥对，消息认证码的密钥对，对称加密的CBC分组（分组模式）需要的初始化向量密钥对。
   5. 握手之后进行加密，对HTTP报文分组，分组后压缩，压缩后的数据和MAC一起加密。
   6. 对称加密保障私密性，消息认证码保障完整性，数字证书保障认证，防止中间人攻击。

8. 对TCP报文打包，加入源IP地址和目标IP地址。

9. 根据目标IP地址和路由表，查询下一跳路由。使用ARP查询下一跳路由的MAC地址。

10. 对IP报文打包并附上MAC地址。

11. 发送数据，服务端接收到请求并返回响应。

12. 浏览器接收到HTTP响应，关闭TCP连接或保持复用，四次挥手。

13. （如果返回了HTML）根据响应头的字符集进行解码

14. 如果响应头没有字符集，则浏览器会默认用一套解码规则，当解析html解析到meta标签中的编码规则时，则替换成新的解码方式重新解码。

15. 资源预解析，会将一些请求资源提前加入请求队列中

16. 解析HTML为DOM树

    1. 标记化（tokenizing）: 将HTML解析成标记
    2. 构建树（tree construction）: 根据标记生成DOM树

17. 解析CSS为CSSOM

18. 根据DOM树和CSSOM生成DOM渲染树

    从DOM的根节点遍历所有可见节点，对其应用对应的CSSOM规则。不可见节点包括（script, meta标签， 被css隐藏的节点）

19. 布局：浏览器获取每个渲染对象的位置和尺寸

20. 绘制：将计算好的像素绘制到屏幕

21. 合成层合并



## 浏览器相关

### 浏览器渲染机制

1. 浏览器解析HTML，生成DOM树。解析CSS，生成样式树。
2. 浏览器将DOM树和样式树结合，生成渲染树。
3. 布局：浏览器获取每个渲染对象的位置和尺寸。
4. 绘制：将计算好的像素点绘制到屏幕。

##### 回流/重排

​	**当渲染对象的位置，尺寸，或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程。**

导致回流的操作：

1. 页面首次渲染

2. 元素位置或尺寸发生变化。

3. 添加或删除可见的DOM元素。

4. 浏览器窗口大小发生变化。

5. 查询某些属性或调用某些方法

   `clientWidth`、`clientHeight`、`clientTop`、`clientLeft`

   `offsetWidth`、`offsetHeight`、`offsetTop`、`offsetLeft`

   `scrollWidth`、`scrollHeight`、`scrollTop`、`scrollLeft`

   `scrollIntoView()`、`scrollIntoViewIfNeeded()`

   `getComputedStyle()`

   `getBoundingClientRect()`

   `scrollTo()`

##### 重绘

​	**样式的改变不改变渲染对象在文档流中的位置时（如：color, background-color的改变）浏览器重新绘制。**



**回流一定引发重绘，重绘不一定引发回流。回流比重绘的代价要更高**。



##### 优化 

1. 浏览器会维护一定队列，所有引起回流或重绘的操作会放进这个队列，一定时间后会对这些操作进行批处理。

   但当访问clientWidth, clientHeight之类的属性时，会刷新这个队列，所以要尽量减少这些属性的访问

2. 浏览器使用的流式布局模型，避免使用table。

3. 对DOM元素进行修改时，可以先使用`display: none`使其脱离文档流，再进行DOM操作，执行完再放回文档流。

4. 对于复杂的动画效果，可以用`display: position`使其脱离文档流

5. 使用CSS3中的`transform, opacity, filters`属性，启动GPU加速，这些属性的改变不会引发回流或重绘。



### 浏览器事件模型

##### 事件传播

```text
<body>
    <div class="outer">
        <div class="inner"></div>
    </div>
</body>
```

当我们点击inner元素的时候。

步骤①：点击事件传播途径：body -> outer -> inner 。这个过程从外往里，所以叫做事件捕获。

步骤②：点击事件传播途径：inner -> outer -> body 。这个过程从里往外，所以叫做事件冒泡。

总结而言，点击一个元素后，点击事件从外层元素开始向内传播（称为事件捕获），直到我们的被点击元素（event.target），之后从被点击元素开始向外传播（称为事件冒泡）

##### 阻止事件传播

当我们点击inner元素时，点击事件的传播路径如下

①outer触发点击事件(捕获标志) -> ②inner触发点击事件(捕获标志) -> ③inner触发点击事件(冒泡标志) -> ④outer触发点击事件(冒泡标志)

我们可以使用**event.stopPropagation**来组织事件的传播。

``` javascript
let inner = document.querySelector('.inner')
let outer = document.querySelector('.outer')

inner.addEventListener('click', function (e) {
    e.stopPropagation()
}, true) 

outer.addEventListener('click', function (e) {
    inner.style.display = 'none'
})
```

##### 事件代理/事件委托

事件代理是由**event.target**实现的

``` javascript
let ul = document.querySelector("ul")

ul.addEventListener("click", (e) => {
    console.log(e.target.innerHTML);
}, false)
```

**事件代理的好处**：

1. 减少内存的使用，只用给一个元素监听事件
2. 当动态增加或删除节点的时候，不用手动重新监听事件



### 浏览器客户端存储

##### LocalStorage

​	**持久化的本地存储，除非主动删除，否则数据不会过期。**

##### SessionStorage

​	**会话结束（关闭页面）后，数据清除。**

##### Cookie 

​	浏览器发送HTTP请求时，先检查是否有相应的Cookie，如果有则将Cookie放在请求头中的Cookie字段中发送。

1. expires: 设置Cookie的过期时间
2. secure: 当secure为true时只能使用https
3. httpOnly: 设置浏览器能否读取Cookie
4. domain和path: 限制Cookie能被哪些URL访问
5. SameSite



##### Session

通常使用Cookie时，会话数据都存在Cookie中。使用Session时，Cookie中只存放一个Session_id，会话数据放在服务端的内存或数据库中。



![Session鉴权原理](https://pic3.zhimg.com/v2-b4c952a1f71313670b94898b2bea4f6a_r.jpg)



##### JWT （JSON Web Token）

同样是用来做鉴权。Session的一个缺点就是由于会话数据保存在服务端，所以在使用服务器集群的时候处理起来很麻烦。而使用JWT的话，会话数据都保存在客户端，就没有这种问题了。

JWT是个很长的字符串，中间用两个`.`分割为三个部分，三个部分依次如下：Header（头部）， Payload（负载），Signature（签名）

###### Header

头部是个JSON对象，结构通常如下

``` json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

alg属性表示签名的算法，默认为HMAC SHA256(写成HS256)。

typ属性表示这个token的类型，通常为“JWT”

最后使用Base64URL把这个JSON对象转化为字符串

###### Payload

负载也是个JSON对象，存放需要传输的数据。除去官方字段，可以在这里定义私有字段。

###### Signature

Signature 部分是对前两部分的签名，防止数据篡改

``` javascript
HMACSHA256(
Base64URL(header) + "." + Base64URL(payload),
secret
)
```

其中secret为服务端指定的密钥。

![JWT鉴权原理](https://pic3.zhimg.com/v2-f1556c71042566d4a6f69ee20c2870ae_r.jpg)




## 前端安全（XSS和CSRF）

##### XSS（csoss-site scripting 跨站脚本攻击）

类型：

1. 反射型XSS

   将用户的输入反射给浏览器。

2. 存储型XSS

   将用户的输入存储在服务器。

3. 基于DOM的XSS

   类似于反射性XSS，但是与服务端并不交互。

   

###### XSS的防御，在数据输出时进行检测

XSS的本质是一种“HTML注入”，用户的输入数据被当成HTML代码的一部分来执行。	

1. 在HTML标签或属性中输出数据，使用HTMLEncode，将字符转化为html实体字符。通常转化& < > " ' / 这几个字符。
2. 在Script标签或事件中输出数据，使用JavaScriptEncode，使用转义符 \ 对特殊字符转义。除了数字和字母，对小于127的字符编码使用\xHH表示，对大于127的字符用Unicode表示。



其他：

1. Cookie设置为HttpOnly也可以防止XSS劫持Cookie
2. CSP 内容安全策略，本质建立白名单，开发者明确告诉浏览器哪些外部资源可以加载和执行。
   - 设置HTTP Header的Content-security-Policy
   
   - 或者设置meta标签的<meta http-equv="Content-Security-Policy">
   
   - 以设置 HTTP Header 来举例
   
     - 只允许加载本站资源
   
       ```
       Content-Security-Policy: default-src ‘self’
       ```
   
     - 图片只允许加载 HTTPS 协议
   
       ```
       Content-Security-Policy: img-src https://*
       ```
   
     - 允许加载任何来源框架
   
       ```
       Content-Security-Policy: child-src 'none'
       ```



##### CSRF（Cross-site request forgery 跨站请求伪造）

要完成一次CSRF攻击，受害者必须依次完成两个步骤：

　　1.登录受信任网站A，并在本地生成Cookie。

　　2.受害者访问危险网站B， 网站B中发送请求给网站A，请求会自动带上Cookie。

###### CSRF的防御

1. 验证码（因为CSRF的攻击往往在受害者不知情的时候成功）

2. 检查请求的Referer头部

   通常网站的页面与页面之间有一定的逻辑联系，例如想要发送登录的请求example.com/api/login时，通常用户在登录的页面example.com/login下。那么我们只需要验证请求的Referer是否为example.com/login即可。

   缺陷：某些情况下浏览器不会发送Referer

3. Cookie的SameSite属性。

   SameSite可以设置为三个值：Strict，Lax，None。

   Strict模式：浏览器禁止第三方请求携带Cookie，比如example.com以外的网站在向example.com/api/login发送请求时不会发送Cookie。

   Lax模式：相对宽松，只能在 `get 方法提交表单`况或者`a 标签发送 get 请求`的情况下可以携带 Cookie，其他情况均不能。

   None模式：默认模式，请求自动带上Cookie。

4. CSRF Token

   CSRF的本质在于**请求的参数可以被攻击者猜到**

   Token是一个随机数，同时存放在表单和用户的Cookie中，发送请求后服务器对请求实体的token和cookie中的token进行对比。

## 错误监控

### 前端错误的类型

##### 即时运行错误

也就是代码错误

##### 资源加载错误

比如图片加载失败，JS加载失败，CSS加载失败。

### 前端错误的捕捉方式

##### 即时运行错误的捕捉方式

1. try...catch

   ``` javascript
   try {
       var a = 1;
       var b = a + c;
   } catch (e) {
       // 捕获处理
       console.log(e); // ReferenceError: c is not defined
   }
   ```
   
2. window.onerror
  
  ``` javascript
  window.onerror = function(errorMessage, scriptURI, lineNo, columnNo, error) {
      console.log('errorMessage: ' + errorMessage); // 异常信息
      console.log('scriptURI: ' + scriptURI); // 异常文件路径
      console.log('lineNo: ' + lineNo); // 异常行号
      console.log('columnNo: ' + columnNo); // 异常列号
      console.log('error: ' + error); // 异常堆栈信息
  }
  ```
  

##### 资源加载错误的捕捉方式

1. object.onerror

   img标签、script标签都可以添加onerror事件，用来捕获资源加载错误

2. performance.getEntries

   可以获取所有已加载资源的加载时间，通过这种方式，可以间接的拿到没有加载的资源错误。

## 事件循环

##### 宏任务 和 微任务

macroTask: setTimeout， setInterval，setImmediate(Node专有)， I/O 操作，定时器

microTask: promise.then等

process.nextTick的任务放在NextTick队列里，类似于微队列，但在微队列前执行。



浏览器通过主线程和工作线程实现事件循环。

Node通过libuv来实现事件循环。



##### 浏览器事件循环

执行宏队列中的第一个宏任务 => 执行微队列中的所有微任务 => 执行宏队列中的下一个宏任务 => 执行微队列中的所有微任务 ...

##### Node中的事件循环

Node事件循环一共有六个阶段，每个阶段中都有一个宏队列，必须执行完一个阶段中宏队列内的全部宏任务，才回去执行所有微任务。

六个阶段：

1. Timer: SetTimeoute和SetInterval的任务放进这个阶段的宏队列执行。
2. pending callback: 执行一些系统操作的回调，例如TCP的错误。
3. idle, prepare: 内部调用。
4. poll: 大部分回调在这里调用。
5. check: SetImmediate的任务放进这个阶段的宏队列执行。
6. close callback: 一些结束时的回调，例如Socket.on("close")



注：高版本的Node和浏览器的行为更加统一。







## 跨域

同源：协议，域名，端口号相同

同源策略：不同源的脚本在没有授权的情况下，不能读写对方的资源



##### 跨域解决方案

1. 通过jsonp跨域

   ```
   <script>
   	function doSomething(json) {
       	//do something
   	}
   </script>

   <script src="http://api.example.com/data?callback=doSomething"></script>
   ```

   缺点：仅支持GET请求，安全性低

2. document.domain

   只适用于一级域名相同，二级域名不同的情况。

   如a.example.com和b.example.com。此时两个网站都设置 `document.domain =  "example.com"`

3. window.name

   只要在同一个窗口，前一个网页设置了window.name，后一个网页就能获取值。

   使用时，先设置iframe的src为目标网页，目标网页中设置window.name，再修改iframe的src为一个与本页面同源的网页，从而获取到window.name。

4. location.hash

   和window.name 类似，这个在iframe内部的页面修改location.hash，在父页面监听hash的改变。

5. CORS

   只需要后端在响应头设置`Access-Control-Allow-Origin: *`， * 为任意Origin，也可以指定Origin

   使用CORS时默认不发送Cookie，想要发送Cookie需要:

   1. 设置`Access-Control-Allow-Credentials: true`
   2. 此时`Access-Control-Allow-Origin`不能设置为 * ，必须指定Origin

   浏览器把请求分为简单请求与非简单请求

   简单请求必须满足以下两大条件

   1. 请求方法为 HEAD / GET / POST
   2. HTTP头部不超过以下几种
      1. Accept
      2. Accept-Language
      3. Content-Language
      4. Last-Event-ID
      5. Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

   不满足的就为非简单请求。

   非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求。

   这个请求的请求方法为`OPTIONS` ，预检请求的头部还会包括以下几个字段

   `Origin`

   `Access-Control-Request-Method` 用来表示非简单请求的请求方法

   `Access-Control-Request-Headers`  用来表示非简单请求的额外头部，例如自定义头部

   

6. postMessage 跨文档通信

   postMessage 和 onmessage

7. nginx反向代理

8. Node中间件代理






## 设计模式

##### 单例模式

一个类只有一个实例



##### 发布-订阅模式

```javascript
// Node中的EventEmitter 就是用的发布订阅模式
class EventEmitter {
      constructor() {
          this.list = {}
      }

      on(name, fn, type = 1) {
          if (!this.list[name]) {
              this.list[name] = []
          }
          this.list[name].push([fn, type])

      }

      once(name, fn, type = 0) {
          this.on(name, fn, type)
      }

      emit(name, ...args) {
          let fns = this.list[name]
          if (!fns || fns.length === 0) return
          fns.forEach((fn, index) => {
              fn[0].apply(this, args)
              if (fn[1] === 0) {
                  fns.splice(index, 1)
              }
          })
      }

      remove(name, func) {
          let fns = this.list[name]
          if (!fns) {
              this.list[name] = []
          }
          fns.forEach((fn, index) => {
              if (fn[0] === func) {
                  fns.splice(index, 1)
              }
          })
      }
  }

let bus = new EventEmitter()

bus.on("click", (value) => {
	console.log(value)
})

bus.emit("click", 111)
```

##### 观察者模式

```javascript
class Publisher {
      constructor() {
          this.list = []
      }

      addListener(listener) {
          this.list.push(listener)
      }

      removeListener(listener) {
          this.list.forEach((item, index) => {
              if (listener === item) {
                  this.list.splice(index, 1)
              }
          })
      }

      notify(obj) {
          this.list.forEach((item) => {
              item.process(obj)
          })
      }
  }

class Subscriber {
    process(obj) {
        console.log(obj.name)
    }
}
```





## 数据结构

### 二叉树

##### 二叉树的建立

```javascript
class Node {
    constructor(data, left, right) {
        Object.assign(this, {
            data,
            left,
            right
        })
    }
}

class BST {
    constructor() {
        this.root = null
    }

    insert(data) {
        let node = new Node(data)
        if (!this.root) {
            this.root = node
        }
        else {
            let current = this.root
            while (true) {
                if (data < current.data) {
                    if (current.left) {
                        current = current.left
                    }
                    else {
                        current.left = node
                        break
                    }
                }
                else {
                    if (current.right) {
                        current = current.right
                    }
                    else {
                        current.right = node
                        break
                    }
                }
            }
        }
    }
}


// 使用
let tree = new BST()
tree.insert(10)
tree.insert(20)
tree.insert(15)
tree.insert(12)
tree.insert(5)
```

##### 二叉树的递归遍历

``` javascript
// 递归的先序遍历
function preOrder (node, cb) {
    if (node) {
        cb(node.data)
        preOrder(node.left, cb)
        preOrder(node.right, cb)
    }
}

// 递归的中序遍历
function inOrder (node, cb) {
    if (node) {
        inOrder(node.left, cb)
        cb(node.data)
        inOrder(node.right, cb)
    }
}

// 递归的后序遍历
function postOrder (node, cb) {
    if (node) {
        postOrder(node.left, cb)
        postOrder(node.right, cb)
        cb(node.data)
    }
}

```

##### 二叉树的非递归遍历

``` javascript
// 非递归的先序遍历
function preOrder(node) {
    let stack = [], res = []
    stack.push(node)
    while (stack.length > 0) {
        let node = stack.pop()
        res.push(node.data)
        if (node.right) {
            stack.push(node.right)
        }
        if (node.left) {
            stack.push(node.left)
        }
    }
    return res
}

// 非递归的中序遍历
function inOrder(node) {
    let stack = [], res = []
    while (stack.length > 0 || node) {
        if (node) {
            stack.push(node)
            node = node.left
        } else {
            node = stack.pop()
            res.push(node.data)
            node = node.right
        }
    }
    return res
}

// 非递归的后序遍历 方法1
function postOrder(node, cb) {
    let stack = []
    let res = []
    while (stack.length > 0 || node) {
        res.unshift(node.data)
        if (node.right) {
            stack.push(node.right)
        }
        if (node.left) {
            stack.push(node.left)
        }
        node = stack.pop()
    }
    return res
}

// 方法2，其实就是在先序遍历的基础上把返回数组进行reverse处理
function postOrder(node) {
    let stack = [], res = []
    stack.push(node)
    while (stack.length > 0) {
        let node = stack.pop()
        res.push(node.data)
        if (node.right) {
            stack.push(node.right)
        }
        if (node.left) {
            stack.push(node.left)
        }
    }
    return res.reverse()
}
```

### 链表

##### 链表的建立

``` javascript
class Node {
    constructor(data) {
        this.next = null
        this.data = data
    }
}

class List {
    constructor() {
        this.head = null
        this.length = 0
    }

    append(data) {
        let node = new Node(data)
        if (this.head) {
            let current = this.head
            while(current.next !== null) {
                current = current.next
            }
            current.next = node
        }
        else {
            this.head = node
        }
        this.length++
    }

    insert(data, position) {
        let node = new Node(data)
        if (position >= 0 && position <= this.length) {
            let currentNode = this.head
            let previousNode = null
            let index = 0
            if ( position === 0 ) {
                node.next = this.head
                this.head = node
            }
            else {
                while( index++ < position ) {
                    previousNode = currentNode
                    currentNode = currentNode.next
                }
                previousNode.next = node
                node.next = currentNode
            }
            this.length++
        }
    }

    size() {
        return this.length
    }

    isEmpty() {
        return this.length === 0
    }
}

let list = new List()
```

##### 反转单向链表

``` javascript
function reverseList(list) {
    let head = list.head
    let currentNode = head
    let pre
    while (currentNode) {
        let nextNode = currentNode.next
        currentNode.next = pre
         pre = currentNode
         currentNode = nextNode
    }
    return pre
}
```







## 排序

```javascript
let arr = [9, 8, 7, 6, 2, 3, 4, 5, 1, 10]

// 快速排序
// 先找一个中间数，把比它小的放a数组，大的放b数组。递归。
function quickSort(arr) {
    if (arr.length <= 1) return arr
    let pivotIndex = Math.floor(arr.length / 2)
    let pivot = arr.splice(pivotIndex, 1)[0]
    let [left, right] = [[], []]
    for (let i = 0; i < arr.length; i++) {
        let item = arr[i]
        if (item <= pivot) {
            left.push(item)
        }
        else {
            right.push(item)
        }
    }

    return quickSort(left).concat([pivot], quickSort(right))
}

//冒泡排序
function bubbleSort (arr) {
    let len = arr.length
    for (let j = 0; j < len - 1; j++) {
        for (let i = 0; i < len - 1 - j; i++) {
            if (arr[i] > arr[i + 1]) {
                [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]]
            }
        }
    }

    return arr
}

//选择排序
function selectSort(arr) {
    let len = arr.length
    for (let i = 0; i < len - 1; i++) {
        let minIndex = i
        for (let j = i + 1; j < len; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j
            }
        }
        [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]]
    }
    return arr
}
//插入排序
function insertSort(arr) {
    let len = arr.length
    for (let i = 1; i < len; i++) {
        let current = arr[i]
        let preIndex = i - 1
        while (preIndex >= 0 && arr[preIndex] > current) {
            arr[preIndex + 1] = arr[preIndex]
            preIndex--
        }
        arr[preIndex + 1] = current
    }

    return arr
}

```

## 编程题

##### 两数之和

(给定无序、不重复的数组 data，取出 n 个数，使其相加和为 sum)

```javascript
// 使用Map而不是两个循环，空间换时间
function twoSum(arr, target) {
    const map = new Map()
    for (let i = 0; i < arr.length; i++) {
        let value = arr[i]
        let diff = target - value
        if (map.has(diff)) {
            return [diff, value]
        } else {
            map.set(value, i)
        }
    }
}

twoSum([1, 2, 3, 4], 7)
```

##### 实现累加器(柯里化)

描述

```js
// 实现一个add方法，使计算结果能够满足如下预期：
add(1)(2)(3) = 6;
add(1, 2, 3)(4) = 10;
add(1)(2)(3)(4)(5) = 15;
```

实现

``` javascript
function sum (...args) {
    function fn(...newArgs) {
        return sum(...args, ...newArgs)
    }
	
  	// 重点是这个toString
  	// 当最后返回函数的时候，自动调用toString函数进行累加
    fn.toString = () => {
        return args.reduce((a, b) => {
            return a + b
        })
    }

    return fn
}
```

##### 实现repeat

描述

```javascript
function repeat(func, times, wait) {
  // TODO
}
const repeatFunc = repeat(alert, 4, 3000);
repeatFunc("hellworld");
//会alert4次 helloworld，每次间隔3秒
```

实现

一：使用setInterval

``` javascript
function repeat(fn, times, wait) {
    let timer
    let count = 0
    return function (...args) {
        if (times > 0) {
            fn(...args)
        }
        timer = setInterval(() => {
            if (count < times - 1) {
                fn(...args)
                count++
            }
            else {
                clearInterval(timer)
            }
        }, wait)
    }
}
```

二：使用async/await + setTimeout

``` javascript
function repeat(fn, times, wait) {
    async function func(...args) {
        for (let i = 0; i < times; i++) {
            fn(...args)
            await new Promise((resolve, reject) => {
                setTimeout(resolve, wait)
            })
        }
    }
    return func
}
```

##### 实现LazyMan

注：微信事业群笔试题

描述

``` javascript
HardMan(“jack”) 输出:
I am jack

HardMan(“jack”).rest(10).learn(“computer”) 输出
I am jack
//等待10秒
Start learning after 10 seconds
Learning computer

HardMan(“jack”).restFirst(5).learn(“chinese”) 输出
//等待5秒
Start learning after 5 seconds
I am jack
Learning chinese
```

实现

``` javascript
class _HardMan {
    constructor(name) {
        this.tasks = []

        setTimeout(async () => {
            for (let task of this.tasks) {
                await task()
            }
        })

        this.tasks.push(() =>
            new Promise(resolve => {
                console.log(`I am ${name}`)
                resolve()
            })
        )
    }

    wait(sec) {
        return new Promise(resolve => {
            console.log(`//等待${sec}秒..`)
            setTimeout(() => {
                console.log(`Start learning after ${sec} seconds`)
                resolve()
            }, sec * 1000);
        })
    }


    rest(sec) {
        this.tasks.push(() => this.wait(sec))
        return this
    }

    restFirst(sec) {
        this.tasks.unshift(() => this.wait(sec))
        return this
    }

    learn(params) {
        this.tasks.push(() =>
            new Promise(resolve => {
                console.log(`Learning ${params}`)
                resolve()
            })
        )
        return this
    }
}

function HardMan(name) {
    return new _HarnMan(name)
}

// 解答分析：
// 1. 链式调用，每一个方法都返回this
// 2. 并不直接执行代码，而是使用SetTimeout，这样就先把想要执行的任务先放进队列再执行
// 3. sleep/wait 的使用，使用setTimeout，如果不用Promise把setTimeout包住，就无法堵塞后面代码的执行
// 4. 除了用Promise，也可以在每个任务中指定的调用下一个任务，如：
	next() {
        let task = this.tasks.shift()
        task && task()
    }

    wait(sec) {
      setTimeout(() => {
        //do something
        this.next()
      }, sec)
    }

```





## 面试题

##### 0.1 + 0.2 == 0.3为何为false

十进制的0.1转化为二进制的0.1时，得到一个无限循环小数。所以当使用有限的位数保存数字的时候，会产生精度的确实，最终的数只是0.1的近似数。

所以0.1和0.2的两个近似数相加，只能得到0.3的近似数。

##### 5升瓶子和6升瓶子装3升水

5L装满 -> 6L瓶子

5L再装满 -> 6L瓶子， 6L倒掉

5L再装满 -> 6L瓶子

5L再装满 -> 6L瓶子

5L瓶子剩下3L水

##### 计算时针和分针的夹角

``` javascript
function getDegree(m, n) {
    let mDegree = m * 30 + n * 0.5
    let nDegree = n * 6
    let degree = Math.abs(mDegree - nDegree)
    if (degree > 180) {
        degree = 360 - degree
    }
    return degree
}
```







## Webpack 复习笔记



```bash
npm init

// 安装webpack
npm install webpack webpack-cli webpack-dev-server webpack-merge -D

//安装Vue
npm install vue vue-router vuex -S

// 安装loader
npm install vue-loader vue-template-compiler -D // 处理Vue单文件组件
npm install style-loader css-loader -D // 处理css
npm install postcss-loader autoprefixed -D // postcss， 用js来处理css，如自动增加前缀（autoprefixed）等功能
npm install sass-loader node-sass -D // sass/scss，css预处理器
npm install babel-loader @babel/core @babel/preset-env // babel 编译JS代码
cnpm i eslint eslint-loader -D // eslint 代码检查
// 安装插件
npm install html-webpack-plugin clean-webpack-plugin optimize-css-assets-webpack-plugin uglifyjs-webpack-plugin -D
```



```javascript
//webpack.config.js

const webpack = require("webpack")
const path = require("path")

//使用Vue单文件组件时，需要vue-loader，同时需要vue-loader/lib/plugin里的插件
const VueLoaderPlugin = require("vue-loader/lib/plugin")
// 根据模板html，在dist目录下生成html
const HtmlWebpackPlugin = require("html-webpack-plugin")
//打包前先删除dist下文件
const { CleanWebpackPlugin } = require("clean-webpack-plugin")
//压缩CSS和混淆JS
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");

module.exports = {
    entry: "./src/main.js",
    output: {
        // `path` is the folder where Webpack will place your bundles
        path: path.resolve(__dirname, './dist'),
        // `publicPath` is where Webpack will load your bundles from (optional)
    	publicPath: 'dist/',
        // `filename` provides a template for naming your bundles (remember to use `[name]`)
        filename: "bundle.js",
        // `chunkFilename` provides a template for naming code-split bundles (optional)
      	chunkFilename: "[name].bundle.js"
    },
    devServer: {
        contentBase: './dist',
      	// 热更新
        hot: true,
    },
    module: {
        rules: [
            {
                test: /\.vue$/,
                use: "vue-loader",
            },
            {
                test: /\.m?js$/,
                use: "babel-loader",
            },
            {
                test: /\.scss$/,
                use: [
                    "vue-style-loader",
                    { loader: "css-loader", options: { importLoaders: 1}},
                    "postcss-loader",
                    "sass-loader"
                ],
            },
          	{
                test: /\.(jpg|png|gif|svg)$/,
                use: [
                    {
                        loader: "url-loader",
                        options: {
                            limit: 5000,
                            name: "imgs/[name].[ext]"
                        }
                    }
                ]
            }
        ]
    },
    plugins: [
        new VueLoaderPlugin()，
      	
        // 根据模板html，在dist目录下生成html
      	new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "./index.html")
        }),
        new CleanWebpackPlugin({
            cleanOnceBeforeBuildPatterns: [path.resolve(__dirname, "./dist/*")]
        }),
        new OptimizeCSSAssetsPlugin(),
        new UglifyJsPlugin(),
        //HRM 热更新
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ]
}

```














## 面经

##### 腾讯 微信事业群一面

第一次面试，很快的凉凉。总结一下自己失败的原因：一方面自己过于紧张，导致个别没那么难的问题没能答好，事后仔细想想觉得自己能答上来；另一方面，面试官确实问到了一些我不太熟悉的地方，主要还是自己的基础不够牢固，得好好反思。

不过这次面试不进流程，而且是寒假实习面试，所以就算挂掉了问题也不大（安慰自己）

1. 自我介绍。颤抖的声音介绍自己...

2. 讲一讲Vue的源码。我自信满满的从Vue实例的构建开始一步步讲起，结果还没讲一半就被叫停，说要我讲一下Vue源码的架构和结构。突然有点蒙，不知道该怎么回答比较好。

3. 浏览器的渲染原理。

   我以为问的是生成DOM渲染树，布局和绘制，以及回流和重绘的知识点。结果面试官又问我：HTML是怎么转换成DOM树的，然后我就蒙圈了...

   之后谷歌了一下，大概的步骤如下

   1. 编码
   2. 预解析
   3. 标记
   4. 构建树

   [详细的构建过程](https://segmentfault.com/a/1190000018730884)

4. 问了我Script标签对浏览器的阻塞问题。

   [参考](https://www.zcfy.cc/article/building-the-dom-faster-speculative-parsing-async-defer-and-preload-x2605-mozilla-hacks-8211-the-web-developer-blog)

   Script脚本的执行会阻塞html的解析，外链script的下载也会阻塞。

   那么多个script标签代码的执行，也必然是顺序执行，多个script外链的请求到底是串行还是并行的呢？

   比较容易混淆，所以容易会认为请求是串行的，因为

   ```
   	<script type="text/javascript" src="./test.js"></script>
       <script type="text/javascript" src="./test2.js"></script>
   ```

   可能会认为下面html的文档的解析要等到上面script执行完才会开始，因为会阻塞。

   但其实请求是并行的。

   因为html解析成dom的时候会先进行预解析。

5. md5。

6. 闭包。面试官问：闭包会造成什么？ 我脱口而出：内存泄漏。被钓鱼了，根本就不会造成内存泄漏。

7. 性能优化手段






## 网站优化

- 减少HTTP请求
  - 合并资源文件（CSS, JS, 雪碧图）
  - 压缩资源文件
  - 图片懒加载（借助IntersectionObserver）
  - 合理设置HTTP缓存， CDN缓存
- 首屏渲染优化
  - 代码分割，路由懒加载
  - 骨架屏
- 代码优化
  - 不用table （流式布局）
  - 不用with, eval
- CSS优化
  - CSS3（transform, opacity）硬件加速
  - 频繁操作DOM时，可以先用`display: none`使其脱离文档流再进行DOM操作
  - 对于复杂的动画效果，可以使用`display: position`使其脱离文档流
- JS优化
  - 函数防抖，函数节流



### 图片懒加载

常规方法（使用offsetTop - scrollTop  或者 getBoundingClientRect()）

``` javascript
<body>
    <div class="blank">
		// 很长的元素，使图片开始不在视口里
    </div>
    <div class="image" data-url="C:/Users/Messiah/Pictures/image.png">
		// 想要懒加载的图片
    </div>
    <script type="text/javascript">
        let image = document.querySelector('.image')

        window.onscroll = throttle(() => {
            // 方法一，使用offsetTop - scrollTop
            if (image.offsetTop - document.documentElement.scrollTop <                                   document.documentElement.clientHeight) 
            {
                let url = image.dataset.url
                image.style.backgroundImage = `url(${url})`
            }
            
            // 方法二，使用getBoundingClientRect
            if (image.getBoundingClientRect().top <                                                       document.documentElement.clientHeight) {
                    let url = image.dataset.url
                    image.style.backgroundImage = `url(${url})`
                }
        }, 200)

		// 节流函数
        function throttle (fn, time) {
            let canRun = true
            return function () {
                if (!canRun) return false
                canRun = false
                setTimeout(() => {
                    fn()
                    canRun = true
                }, time)
            }
        }

    </script>
</body>

```

![https://zhuanlan.zhihu.com/p/55311726](https://pic1.zhimg.com/80/v2-af1ab0c5f34e468e8647135c1f9f51e4_hd.jpg)

offsetParent定义: 一个元素的已定位（position不为static）的父元素, 类似于绝对定位中已经定位的父元素，如果一个元素没有已经定位的父元素，则该元素的offsetParent为body。

此例子中image.offsetTop，image没有已经定位的父元素，则image的offsetParent为body。

如果我们的代码结构如下， 

``` html
    <body>
        <div class="outer">
            <div class="blank">

            </div>
            <div class="image">

            </div>
        </div>
        
    </body>
```

滚动条在outer上，我们应该给outer加一个样式

``` css
.outer {
    position: relative;
}
```

那么，image.offsetParent就是outer，我们就可以继续使用以下代码来判断图片是否进入视口

``` javascript
image.offsetTop - outer.scrollTop < outer.clientHeight
```



**IntersectionObserver**

``` javascript
// 使用IntersectionObserver，十分方便 
let io = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
        if (!entry.isIntersecting) return
        else {
            let el = entry.target
            el.style.backgroundImage = `url(${el.dataset.url})`
            io.unobserve(el)
        }

    })
})
io.observe(document.querySelector(".image"))
// <div class="image" data-url="https://xxx.com/1.jpg" ></div>
```

这个API同样能用来实现无限滚动（Infinity Scroll）



### Base64

Base64就是用64个可见字符（26个大写字母，26个小写字母，10个数字，一个+号，一个/号共64个字符）来表示二进制的方法。

Base会将三个字节转化成四个字节，可以编码后的文本会比之前多三分之一左右。

例如 3 * 8 => 4 * 6 (前面加两个0变成8)



### 雪碧图

合并HTTP请求，使用background-position来选择使用的图片。



## 其他

##### 分号的问题

平时我写JavaScript代码的时候习惯不加分号，因为这样子代码看着更加简洁，不过有的时候必须得加

``` javascript
let y = 2
let x = 1
[y, x] = [x, y]
```

以上代码就被会当成

``` javascript
let y = 2
let x = 1[y, x] = [x, y]
```

##### 移动端300ms延迟

以前的移动端网页，点击事件会有个300ms的延迟，Fastclick库就是为了解决这个问题的。

不过现代化的浏览器已经没有这问题了。

``` html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

