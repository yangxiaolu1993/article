

> 单页 Web 应用（single page web application，SPA），是当今网站开发技术的弄潮儿，仅靠加载单个 HTML 页面就在网站开发中占据了一席之地。很多传统网站正在或者已经转型为单页 Web 引用。单页 Web 应用网站也如雨后春笋般出现在大众眼前。前后端分离技术、MVVM 模式、前端路由、webpack 打包器也随之孕育而生。如果你是一名 Web 应用开发人员，却还没有发开或者甚至不了解单页 Web 应用，那就要加油了！  

为了配合单页 Web 应用快速发展的节奏，这几年，各类前端组件化技术栈层出不穷。通过不断的版本迭代 React、Vue 脱颖而出，成为当下很受欢迎的两大技术栈。

技术栈 |  发布   | NPM 下载量（2020.1.1~2020.7.31）| GitHub Star| 发展趋势 
---   |  :--:  | :--:    |   :--:     | :--:
React | 2011年 | 227,023,229 | 154 K | 持续增长
Vue   | 2014年 | 46,249,304  | 170 K | 持续增长

仅 7 个月的时间，两个技术栈的下载量就突破了百万，React 甚至突破了千万。可以想象这两个技术栈是多么受欢迎。

不管是现下流行的 React、Vue，还是红极一时的 Angular、Ember。只要是单页 Web 应用开发，都离不开前端路由的配合。如果我们把单页 Web 应用比作一间房，每个页面分别对应房子中的每个房间，那么路由就是每个房间的门，所以不管房间装饰的有多漂亮，没有门，也无法展示在用户眼前，路由在单页面 Web 应用开发的重要性也就不言而喻了。

OK，既然路由这么重要，那我们从这几个方面，来讲解前端路由吧。

![](https://img12.360buyimg.com/imagetools/jfs/t1/149777/13/5226/148749/5f31054dEcecc1fea/289a687a8a070dcc.png)

## 前端路由前世今生

### 后端路由

路由这个概念最先是在后端出现的，在 Web 开发早期的「刀耕火种」年代里，一直是后端路由占据主导地位。用户通过 URL 访问到的页面，大多都是通过后端路由匹配之后在返回给浏览器的。

![服务端渲染](https://img12.360buyimg.com/imagetools/jfs/t1/145816/20/5225/177101/5f325408Eb6b62cc1/09641c19f311ff92.png)

在前端路由出现之前，HTML，CSS，JavaScript 的文件以及数据载体 json(xml) 等文件，都是以模板的形式放到后端网站目录下的。在 Web 后端，不管是什么语言的后端框架，都会有一个专门开辟出来的路由模块或者路由区域，用来匹配用户给出的 URL 地址，以及一些表单提交、ajax请求的地址。请求一个 URL 地址时，URL 地址进行后端路由匹配，将模板拼接好后将之返回给前端完整的 HTML，浏览器拿到这个 HTML 文件后直接解析展示了。


### 过渡

以后端路由为基础，开发的 Web 应用，都会存在一个弊端。每跳转到不同的 URL，都是重新访问服务端，服务器拼接形成完整的 HTML，返回到浏览器。浏览器的前进、后退键都会重新访问服务器，没有合理地利用缓存。 

随着前端页面复杂性越来越高，功能越来越完善，后端网站目录下的代码文件会越来越多，耦合性也越来越严重。就算简单的颜色修改，也需要前后端的同步操作。由于以 JavaScript 为代表的前端技术尚未崛起，程序猿没办法实现前端渲染。

直到 1998 年，微软的 Outloook Web App 团队提出 Ajax 的基本概念（XMLHttpRequest 的前身），2005 年 Google Map 的发布让 Ajax 这项技术发扬光大，向人们展示了它真正的魅力，前后端分离的开发模式开始兴起。2008 年，Google V8 引擎发布，JavaScript 随之崛起，前端工程师开始借鉴后端模板思想，单页面应用就此诞生。

于是，我们开始进入了前端路由的时代。

### 前端路由

单页应用不仅仅是在页面交互是无刷新的，连页面跳转都是无刷新的，为了实现这一功能，前端路由问世了。页面跳转的 URL 规则匹配由前端来控制。

![客户端渲染](https://img11.360buyimg.com/imagetools/jfs/t1/122174/23/9461/155946/5f325431Ed7c3ac00/131fbced1f4ffe8c.png)

前端路由的兴起，使得页面渲染由服务器渲染变成了前端渲染。为什么这么说呢！请求一个 URL 地址时，服务器不需要拼接模板，只需返回一个HTML即可，一般浏览器拿到的 html 是这样的：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Demo</title>
  <link href="app.css" rel="stylesheet"/>
</head>
<body>
  <div id="app"></div>
  <script type="text/javascript" src="app.js"></script>
</body>
</html>

```
这里空荡荡的只有一个```<div id="app"></div>```，以及一系列的js文件，所以说这个html是不完整的。我们看到的页面是通过这一系列的js渲染出来的，也就是我们常说的前端渲染。前端渲染通过客户端的算力来解决页面的构建，很大程度上缓解了服务端的压力。加上前端路由的配合，实现了无缝页面切换体验的用户体验。

## 路由原理解析  

了解了前端路由的由来，那路由到底是个啥？在单页 Web 应用中，路由描述的是 URL 与 UI 之间的映射关系，这种映射是单向的，即 URL 变化引起 UI 更新（无需刷新页面）。 

现在流行的前端路由模式分为两种：

* Hash 模式：地址栏 URL 中有 #，即 hash 值。通过改变 hash 值实现路由跳转。
* History 模式：地址栏 URL 中没有 #。通过 pushState() 和 replaceState() 改变浏览器历史记录栈，实现路由跳转。

Hash 模式与 History 模式其实实现原理很简单，现在就跟着小编一起来揭开它神秘的面纱吧！

### History

用户访问网页的历史记录通常会被保存在一个类似于栈对象中，即 window.history 对象，点击返回就出栈，跳下一页就入栈。在单页面兴起之前，History 只提供了三个 API ：

* history.back()：回退到上一个访问记录，同浏览器后退键
* history.forward()：前进到下一个访问记录，同浏览器前进键
* history.go(n)：跳转到相应的访问记录；若 n > 0，则前进，若 n < 0，则后退，若 n = 0，则刷新当前页面

随着单页面的发展，大前端的到来，HTML5 对 History API 新增的两个方法：pushState()、replaceState()。  
* pushState(stateData, title, url)：在 history 中创建一个新的访问记录，不能跨域，且不造成页面刷新;
* replaceState(stateData, title, url)：修改当前的访问记录，不能跨域，且不造成页面刷新

![History API](https://img10.360buyimg.com/imagetools/jfs/t1/141154/19/5653/297566/5f3a609cE41ebbe25/c23845924a284491.png)

另外，HTML5 新增了可以监听 history 和 hash 变化的方法：window.onpopstate()、window.onhashchange()  

![Window API](https://img14.360buyimg.com/imagetools/jfs/t1/119378/39/13661/236999/5f3a6046E97ab7aa0/6f519d1c5e2d6d88.png)

我们发现触发 window.onpopstate()、window.onhashchange() 监听事件的方法有重合的部分。使用 history.back()、history.forward()、history.go(n)、&lt;a&gt;标签改变 hash 值，window.onpopstate()、window.onhashchange() 都会触发。     
> 我们可以得到一个结论：在 hash 路由模式下，我们同样可以使用 window.onpopstate() 事件，监听路由的改变。在这里提前透露一下：vue-router 的 hash 模式下路由的改变，就是监听的 window.onpopstate() 事件。

### Hash 

一个完整的 URL 包括：协议、域名、端口、虚拟目录、文件名、参数、锚。  

![URL 组成](https://img12.360buyimg.com/imagetools/jfs/t1/116233/29/15155/17882/5f3a73a9Ef5592b82/7dbf415ead483d09.png)  

hash 值指的是 URL 地址中的锚部分，也就是 # 后面的部分。Hash 路由就是利用 hash 值的变化来实现页面的跳转。那么 hash 值都有哪些特点呢！

* hash 值是网页的标志位，HTTP 请求不包含锚部分
* 因为 HTTP 请求不包含锚部分，所以 hash 值改变时，不触发网页重载
* 改变 hash 值会改变浏览器的访问历史，即 history 栈
* 改变 hash 值会触发 window.onhashchange() 事件

改变 hash 值的方式有三种：

1. ```<a/>```标签使锚点值变化，例：```<a href='#/home'></a>``` 
2. window.localhost.hash 
3. 浏览器前进键（history.forword()）、后退键(history.back())
4. 手动刷新当前的 URL（点击 Enter 键进行刷新）

到现在我们可以知道，前三种改变 hash 值的方式，并不会导致浏览器向服务器发送请求，浏览器不发出请求，也就不会刷新页面。hash 值改变，触发全局 window 对象上的 hashchange 事件。通过 hashchange 事件来监听到 URL 的变化，从而进行 DOM 操作来模拟页面跳转。  

手动刷新 URL，与在浏览器中输入 URL，Enter 回车的情况是一样的，初始化页面，浏览器会重新向服务器发送请求，服务器返回 index.html，不会触发 hashchange 事件，但是会触发 load 事件。

![hash 流程图](https://img13.360buyimg.com/imagetools/jfs/t1/119923/18/9500/67035/5f335933Ee392a6e3/cf3e580523d52c0a.png)  

### History 模式与 Hash 模式

History 模式与 Hash 模式都属于浏览器自身的特性。不管是在哪种模式下，路由的跳转，都会在浏览器历史记录中创建一个新的历史记录项，history.length 的值也会 +1。若是使用 replaceState() 进行路由跳转，情况就不一样了。replaceState() 会修改当前的历史记录项，不会创建新的历史记录项，history.length 的值保持不变。

![浏览器历史记录](https://storage.360buyimg.com/imgtools/a7de05d4b9-97a5bee0-e0f6-11ea-b779-5171ebe3afba.gif)

浏览器历史记录可以看成是一个栈，遵循先进后出的规则，即最先进入的历史项，在栈的最底部。使用 pushState() 在浏览器记录中分别创建两个记录历史项 cart、list，压入历史记录栈中，此时历史栈为 cart -> list 。接着，使用 replaceState() 修改浏览器记录，将最后一次压入栈的 list 修改为 item，此时历史栈中更新为 cart -> item。触发浏览器后退键，最后一次压入栈的 list 出栈，cart 路由页面渲染。  

单页面路由利用浏览器自身的特性，实现了 URL 与 UI 之间的映射关系：

* Hash 模式：URL 的锚点值（hash 值） + window.onhashchange() 事件
* History 模式：history.pushState() + history.replaceState() + window.onpopstate() 事件

## 原生 JS 实现路由

知道了 History 模式与 Hash 模式的实现原理，我们何不上手实现一下呢！

### 原生 JS 实现 hash 路由

使用原生 JS 实现 hash 路由，我们需要先明确，触发路由跳转的方式：

* 使用 &lt;a&gt; 标签，触发锚点改变，监听 window.onhashchange() 进行路由跳转
* JS 动态改变 hash 值，window.location.hash 赋值，进行路由跳转

那我们就一步一来，先来完成 HTML 的设计：

index.html

```
<body>
  <div>
      <h3>Hash 模式路由跳转</h3>
      <ul class="tab">
          <!-- 定义路由 -->
          <li><a href="#/home"> &lt;a&gt; 标签点击跳转 home</a></li>
          <li><a href="#/about"> &lt;a&gt; 标签点击跳转 about</a></li>
      </ul>
      <!-- JS 动态改变 hash 值，实现跳转 -->
      <div id="handleToCart"> JS 动态点击跳转 cart</div>
      <!-- 渲染路由对应的 UI -->
      <div id="routeViewHash" class="routeView"></div>
  </div>
</body>
```
接下来，定义 JSHashRouter 类

```
class JSHashRouter {
    constructor(routerview){
        this.routerView = routerview
    }
    init(){
        /**
         * 页面 首次渲染
         * 如果不存在 hash 值，那么重定向到 #/,若存在 hash 值，就渲染对应的 UI
         */
        if(!location.hash){
            location.hash="#/"
        }else{
            this.routerView.innerHTML = '当前路由：'+ location.hash
        }
        // 监听 hash 值改变
        window.addEventListener('hashchange', ()=>{
            this.routerView.innerHTML = '当前路由：'+ location.hash
        })
    }
    push(path){
        window.location.hash = path
    }
}

```

JSHashRouter 类自身定义了 routerView 属性，接收渲染路由 UI 的容器。JSHashRouter 类定义了2个方法：init() 和 push()

* init()   
 首先页面渲染时，不会触发 window.onhashchange()，根据当前 hash 值，渲染 routerView。监听 window.onhashchange() 事件，一旦事件触发，重新渲染 routerView。

* push()  
 在实际开发过程中，进行路由跳转需要使用 JS 动态设置。通过为 window.location.hash 设置值，实现路由跳转。 这里需要注意，window.location.hash 改变 hash 值，也会触发 window.onhashchange() 事件。

所以不管是 &lt;a&gt; 标签改变 hash，还是 window.location.hash 改变 hash，统一在 window.onhashchange() 事件中，重新渲染 routerView。

接下来只要 JSHashRouter 实例化，调用就可以了：  
index.html
```
<body>
  <div>
      ……
      <!-- JS 动态改变 hash 值，实现跳转 -->
      <div id="handleToCart"> JS 动态点击跳转 cart</div>
      <!-- 渲染路由对应的 UI -->
      <div id="routeViewHash" class="routeView"></div>
  </div>

  <script type="text/javascript" src="./hash/hash.js"></script>
  <script>
    window.onload = function () {
        let routerview = document.getElementById('routeViewHash')
        // HashRouter 实例化
        let hashRouter = new JSHashRouter(routerview)
        hashRouter.init()
        // 点击 handleToCart ，JS 动态改变 hash 值
        var handleToCart = document.getElementById('handleToCart');
        handleToCart.addEventListener('click', function(){
            hashRouter.push('/cart')
        }, false); 
    }
  </script>
</body>

```  
那我们来看一下效果：   

![hash-js](https://storage.360buyimg.com/imgtools/dbd41c75b7-1a163350-e066-11ea-b779-5171ebe3afba.gif)

### 原生 JS 实现 history 路由

实现 History 模式的路由同样需要明确触发跳转路由的方式：

* JS 动态触发 pushState()、replaceState()
* 拦截 &lt;a&gt; 标签点击事件，检测 URL 变化，使用 pushState() 进行跳转。

History 模式触发路由跳转的方式与 Hash 模式稍有不同。通过之前的介绍，大家都了解，pushState()、replaceState()、&lt;a&gt; 标签改变 URL 都不会触发 window.onpopstate() 事件。那该怎么办，在实际开发过程中，我们是需要在 HTML 中进入跳转的。好在我们可以拦截 &lt;a&gt; 标签的点击事件，阻止 &lt;a&gt; 标签默认事件，检测 URL，使用 pushState() 进行跳转。

index.html
```
<div>
    <ul class="tab">
        <!-- 定义路由 -->
        <li><a href="/home">点击跳转 /home</a></li>
        <li><a href="/about">点击跳转 /about</a></li>
    </ul>
    <!-- JS 动态改变 URL 值，实现跳转 -->
    <div id="handlePush" class="btn"> JS 动态 pushState 跳转 list</div>
    <div id="handleReplace" class="btn"> JS 动态 replaceState 跳转 item</div>
    <!-- 渲染路由对应的 UI -->
    <div id="routeViewHistory" class="routeView"></div>
</div>
```
接下来，定义 JSHistoryRouter 类

```
class JSHistoryRouter {
    constructor(routerview){
        this.routerView = routerview
    }
    init(){
        let that = this
        let linkList = document.querySelectorAll('a[href]')
        linkList.forEach(el => el.addEventListener('click', function (e) {
            e.preventDefault()  // 阻止 <a> 默认跳转事件
            history.pushState(null, '', el.getAttribute('href')) // 获取 URL，跳转
            that.routerView.innerHTML = '当前路由：' + location.pathname
        }))
        // 监听 URL 改变
        window.addEventListener('popstate', ()=>{
            this.routerView.innerHTML = '当前路由：' + location.pathname
        })
    }
    push(path){
        history.pushState(null, '', path)
        this.routerView.innerHTML = '当前路由：' + path
    }
    replace(path){
        history.replaceState(null, '', path)
        this.routerView.innerHTML = '当前路由：' + path
    }
}
```
JSHistoryRouter 类自身定义了 routerView 属性，接收渲染路由 UI 的容器。JSHistoryRouter 类定义了3个方法：init() 、push()、replace()。

* init()  
    init(）中主要做了2件事：
    1. window.onpopstate() 事件，用于 history.go()、history.back()、history.forword() 的监听。  
    2. 点击 &lt;a&gt 标签，浏览器 URL 更新为 href 的 URL，e.preventDefault() 阻止默认事件。将 href url 通过 pushState() 更新浏览器 URL，重新渲染 routerView。
* push()
    通过 history.pushState() 更新浏览器 URL，重新渲染 routerView。
* replace()
    通过 history.replaceState() 更新浏览器 URL，重新渲染 routerView。

ok，现在只要实例化 JSHistoryRouter 类，调用方法就可以了！

index.html

```
<body>
  <div>
      ……
      <!-- JS 动态改变 URL 值，实现跳转 -->
      <div id="handlePush" class="btn"> JS 动态 pushState 跳转 list</div>
      <div id="handleReplace" class="btn"> JS 动态 replaceState 跳转 item</div>
      <!-- 渲染路由对应的 UI -->
      <div id="routeViewHash" class="routeView"></div>
  </div>
  <script type="text/javascript" src="./history/history.js"></script>
  <script>
    window.onload = function () {
      let routerview = document.getElementById('routeViewHistory')
      // HistoryRouter 实例化
      let historyRouter = new JSHistoryRouter(routerview) 
      historyRouter.init()
      // JS 动态改变 URL 值
      document.getElementById('handlePush').addEventListener('click', function(){
        historyRouter.push('/list')
      }, false); 
      document.getElementById('handleReplace').addEventListener('click', function(){
        historyRouter.replace('/item')
      }, false); 
    }
  </script>
</body>
```
来来来，展示效果啦！
![](https://storage.360buyimg.com/imgtools/aaa3272210-7e7718e0-e102-11ea-8f90-d15419c43e51.gif)

> 细心的同学应该已经发现了，在 HashRouter 类的 init() 方法中，处理了页面首次渲染的情况，但在 HistoryRouter 类中却没有。Hash 模式下，改变的是 URL 的 hash 值，浏览器请求是不携带 hash 值的，所以 http:localhost:8080/#/home 与 http:localhost:8080/#/cart，浏览器请求都是 http:localhost:8080。History 模式下，改变的则是 URL 除锚外的其他部分，所以 http:localhost:8080 与 http:localhost:8080/home 浏览器请求是不同的。这就也就可以解释在 vue-router 下，Hash 模式后端只需要将域名指向 index.html 即可，History 模式后端需要将域名下匹配不到的静态资源，返回到同一个 index.html 页面。

### 基于 Vue 实现路由

Vue Router 与 React Router 可以说是现在最流行的单页面路由管理器了。虽然二者的使用方式有些差别，但是设计理念是一样的，只要掌握了其中一个，另一个也就不难理解了。这里我们以 Vue Router 为例，来聊聊如何实现 Vue Router。首先通过 vue-cli 搭建简单的环境，搭建过程在这里就不赘述了，调整之后的目录如下：

```
├── config                   
├── index.html               
├── package.json
├── src
│   ├── App.vue
│   ├── main.js
│   ├── assets
│   │   ├── css
│   │   ├── image
│   │   └── js
│   ├── components
│   ├── plugins                  // 插件
│   │   └── myRouter             
│   │       ├── index.js         // MyRouter 类
│   │       └── install.js       // MyRouter 对象的 install 函数
│   ├── router
│   │   └── myRouter.js
│   ├── util
│   └── view
│       └── home.vue
├── static
└── vue.config.js
```

#### Vue Router 的本质

在实现 Vue Router 之前，我们先来观察一下 Vue 技术栈中是如何引入基础的 Vue Router 的。

1. 通过 NPM 安装 vue-router 依赖包，```import VueRouter from 'vue-router'```引入
2. 定义路由变量 routes，每个路由映射一个组件
    ```
    const Foo = { template: '<div>foo</div>' } // 可以通过 import 等方式引入
    const Bar = { template: '<div>bar</div>' }

    const routes = [
        { path: '/foo', component: Foo },
        { path: '/bar', component: Bar }
    ]
    ```
3. router 实例化，将路由变量 routes 作为配置项
    ```
    const router = new VueRouter({
        mode:'history',
        routes // (缩写) 相当于 routes: routes
    })
    ```
4. 通过 Vue.use(router) 使得每个组件都可以拥有 router 实例
5. 将 router 挂载在根实例上。
    ```
    export default new Vue({
        el: '#app',
        router
    })
    ```
通过这五步，启动应用，就顺利完成了路由的配置工作，在任何组件内可以通过 this.$router 访问 router 实例，也可以通过 this.$route 访问当前路由。在配置路由的过程中，我们获得哪些信息呢？

* router 实例是通过```new VueRouter({...})```创建的，也就是说，我们引入的 Vue Router 其实就是 VueRouter 类。
* router 实例化的配置项是一个对象，也就是，VueRouter 类的 contractor 方法有入参，routes、mode 是其中的参数。
* 通过 Vue.use(...) 为每个组件注入 router 实例，而 Vue.use() 是原则上是执行对象的 install 方法。

通过这3点信息，我们基本上可以将 MyRouter 的基本结构设计出来：

```
class MyRouter{
    constructor(options){
        this.routes = options.routes || []
        this.mode = options.mode || 'hash' // 模式 hash || history
        ......
    }
}

MyRouter.install = function(Vue,options){
    ......
}

export default MyRouter
```

将项目中 Vue Router 替换成定义好的 MyRouter 插件，启动应用，页面空白，没有报错。

> 我按照自己的开发逻辑，将 MyRouter 的基本结构拆分成2个 js 文件 install.js 与 index.js。install.js 文件用于实现 install 方法，index.js 用于实现 MyRouter 类。  

#### Vue.use() 

> 为了能更好的理解 Vue Router 的实现原理，我们简单了解一下使用 Vue.use() 定义的插件中，都可以做哪些事情。如果你已经掌握了 vue.use() 的使用，可以直接跳过。
  
Vue 官网说明：如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue、vue 实例化时参入的选项 options 作为参数传入，利用传入的 Vue 我们便可以定义一些全局的变量、方法、组件等。

* 全局组件注册  

    ```
    MyRouter.install = function(Vue,options){
        Vue.component("my-router-view", {template: '<div>渲染路由 UI</div>'})
    }
    ```
    项目中的任何组件都可以直接使用```<my-router-view/>```，不需要引入。

* Vue.mixin() 全局注册一个混入
    ```
    MyRouter.install = function(Vue,options){
        Vue.mixin({
            beforeCreate(){
                Object.defineProperty(this,'$location',{
                    get(){ return  window.location}
                })
            }
        })
    }
    ```
    使用全局混入，它将影响每个之后单独创建的 Vue 实例。通过 Object.defineProperty() 为注入的 Vue 实例，添加新的属性 $location，运行应用，在每一个 Vue 实例（组件）中，都可以使用```this.$location```获得 location 对象。

> 自定义插件中定义的全局组件、方法、过滤器、处理自定义选项注入逻辑等都是在 install 方法中完成的。

#### $myRouter 与 $myRoute

项目中使用了 VueRouter 插件后，在每个 vue 实例中都会包含两个对象 $router 和 $route。

* $route 是一个跳转的路由对象，每一个路由都是一个 route 对象，是局部对象。
* $router 是 VueRouter 的实例化对象，是全局对象，包含了所有 route 对象。

**$myRouter**
在配置 VueRouter 时，最后一步是将 router 挂载在根实例上，在实例化根组件的时候，将 router 作为其参数的一部分。也就是目前只有根组件有 router 实例，其他子组件都没有。问题来了，如何将 router 实例放到每个组件呢？

install.js
```
MyRouter.install = function(Vue,options){
    Vue.mixin({
        beforeCreate(){
            if (this.$options && this.$options.myRouter){  // 根组件
                this._myRouter = this.$options.myRouter;
            }else { 
                this._myRouter= this.$parent && this.$parent._myRouter // 子组件
            }
            // 当前实例添加 $router 实例
            Object.defineProperty(this,'$myRouter',{
                get(){
                    return this._myRouter
                }
            })
        }
    })  
}
```
通过之前对 Vue.use() 的基本了解，我们知道：Vue.mixin() 可以全局注册一个混入，之后的每个组件都会执行。混入对象的选项将被“混合”进入该组件本身的选项，也就是每个组件在实例化时，install.js 中定义的 beforeCreate 方法，会与组件中的 beforeCreate 方法合并。```this```即为该实例对象。

Vue 初始化 beforeCreate 阶段，会将 Vue 之前定义的原型方法、全局 API 属性、全局 Vue.mixin() 内的参数，合并成一个新的options，并挂载到 $options 上。在根组件上，$options 可以获得到 myRouter 对象。

子组件在初始化 beforeCreate 阶段，除了合并上述内容外，还会将父组件的参数进行合并，比如父组件定义在子组件上的 props。这也就可以解释为什么要在 beforeCreate() 定义了。

不知道大家有没有思考一个问题，当知道组件为子组件时，为什么就可以直接拿父组件的 _myRouter 对象呢？想要解释这个问题，我们先来思考一下，父组件与子组件生命周期的执行顺序：

父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 create -> 子 beforeMount ->子 mounted -> 父 mounted

问题的答案是不是很清楚了，子组件 beforeCreate 执行时，父组件 beforeCreate 已经执行完了，_myRouter 已经挂载了。

然后，我们通过 Object.defineProperty 将 ```$router``` 挂载到组件实例上。

**$myRoute**  

$myRoute 用于存储当前路径。参照 VueRouter 的 $route，$myRoute 对象包括：path、name、hash、meta、fullPath、query、params。

install.js
```
MyRouter.install = function(Vue,options){
    Vue.mixin({
        beforeCreate(){
            ......

            // 为当前实例添加 $route 属性
            Object.defineProperty(this,'$myRoute',{
                get(){
                    return {
                        path:'',
                        name:'',
                        hash:'',
                        meta:{},
                        fullPath:'',
                        query:{},
                        params:{}
                    }
                }
            })
        }
    })  
}
```
在 Hash 模式或 History 模式下，获取当前路径的方式是不同的，所以想要完善 $myRoute，必须先完善 MyRouter 类。这里就先留个小尾巴。

#### MyRouterLink 组件与 MyRouterView 组件

好了，回到我们之前的话题，我们通过观察 Vue Router 的引入，实现了 MyRouter 类的基本结构。接下来，我们在看看 Vue Router 是如何使用的。

Vue Router 定义了2个组件：```<router-link/>```、```<router-view/>```。```<router-link/>```用来路由导航，默认会被渲染成 &lt;a&gt; 标签，通过传入 to 属性指定跳转链接。```<router-view/>```用来渲染匹配到的路由组件。

```
<div id="app">
  <p>
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <router-view></router-view>
</div>
```
![](https://img11.360buyimg.com/imagetools/jfs/t1/127251/15/10009/254224/5f3bc8a1E045a62d5/902cde9f5d338f7b.png)

那我们就仿照 Vue Router，也在 MyRouter 插件中，添加两个全局组件 ```<my-router-view>```、```<my-router-link>```。通过上面对 Vue.use() 定义插件的介绍，相信大家已经知道该如何在插件中定义组件了，那就先来定义```<my-router-link>```组件吧。  
我们知道```<router-link/>```会被渲染成 &lt;a&gt; 标签，```<router-link to="/foo">Go to Foo</router-link>```在最终在浏览器中渲染的结果是```<a href="/foo">Go to Foo</a>```。所以在```<my-router-link>```组件中放的是 &lt;a&gt; 标签，```<solt/>```代替模板内容，那我们就按照开发组件的方式，一步一步来吧！

link.vue
```
<template>
  <a :href="to"><slot/></a>
</template>
<script>
export default {
  name: 'MyRouterLink',
  props:['to'],
  data () {return {}}
}
</script>
```
install.js
```
import MyRouterLink from './view.js'
MyRouter.install = function(Vue,options){
    Vue.component(MyRouterLink.name, MyRouterLink)
}
```

好了，组件定义好了，我们来印证一下。

App.vue
```
<div class="footer">
    <my-router-link to="/home">首页</my-router-link>
    <my-router-link to='/classify'>分类</my-router-link>
    <my-router-link to='/cart'>购物车</my-router-link>
</div>
```
![](https://img13.360buyimg.com/imagetools/jfs/t1/112250/23/15452/170005/5f3c8c28Ef094bb91/ab5e22716b0f284f.png)

Bingo！没有报错，并且得到了我们想要的效果。但是这种方式的局限性太大了，如果模板内容为 ```<div/>```，如果是将 ```<my-router-link/>```放到```<ul>```标签中，想让其渲染成```<li>```，如果路由导航的触发条件不是 click，是 mouseover 等等。这些问题改如何处理呢？Vue 中提供的 render() 渲染函数可以帮助我们解决这些问题。我们按照 render() 函数的规则，重写 MyRouterLink 组件。需要注意一点，render 函数开发的组件其实是一个对象，所以文件后缀是 .js

link.js
```
export default {
    name: 'MyRouterLink',
    functional: true,
    props: {
        to: {
            type: [String, Object],
            required: true
        },
        tag:{
            type: String,
            default: 'a'
        }
    },
    render: (createElement, {props,parent}) => {
        let toRoute = parent.$myRoute.mode == 'hash'?`#/${props.to}`:`/${props.to}`
        return createElement(props.tag,{
            attrs: {
                href: toRoute
            }
        },context.children)
    }
};
```
OK，引用看一下效果

App.vue
```
<div class="footer">
    <my-router-link to="home" tag="p">首页</my-router-link>
    <my-router-link to='classify'>分类</my-router-link>
    <my-router-link to='cart'>购物车</my-router-link>
</div>

```
![](https://img10.360buyimg.com/imagetools/jfs/t1/144416/18/5668/172131/5f3c94e3E51a4e870/be832c279b8dd5f5.png)

正常渲染，没有报错，第一个使用 &lt;p&gt; 标签渲染。MyRouterLink 组件的基本实现先到这里，还有的疑问没有解决，渲染成了别的标签，Hash 模式该如何监听 URL 变化？改如何阻止 &lt;a&gt; 标签默认跳转？不要着急，之后会一一解答的。咱们先来按照 MyRouterLink 组件的实现思路，把 MyRouterView 组件实现。   

MyRouterView 组件是根据 URL 匹配配置的路由数组。在每个组件中，我们可以通过 $myRouter 获取到 myRouter 实例了，那实现 MyRouterView 组件就简单多了。

view.js
```
export default {
    name: 'MyRouterView',
    functional: true,
    render: (createElement, {props, children, parent, data}) => {
        let path = ''
        let temp = ''
        if(parent.$myRouter){
            switch (parent.$myRouter.mode){
                case 'hash':
                    path = location.hash.slice(1)
                case 'history':
                    path = 
            }
            parent.$myRouter.routes.forEach(route=>{
                if(route.path == path) temp = route.component
            })
        }
        return createElement(temp)
    }
}

```
install.js
```
import MyRouterView from './view'
MyRouter.install = function(Vue,options){
    Vue.component(MyRouterView.name, MyRouterView)
}
```
接下来，在 App.vue 中添加上 <my-router-view/> 组件，来看看能不能得到想要的

App.vue
```
<template>
  <div id="app">
    <my-router-view/>
    <div class="footer">
        <my-router-link to="home" tag="p">首页</my-router-link>
        <my-router-link to='classify'>分类</my-router-link>
        <my-router-link to='cart'>购物车</my-router-link>
    </div>
  </div>
</template>
```
![](https://img14.360buyimg.com/imagetools/jfs/t1/136238/9/7451/34271/5f3cecceE28439237/00bfd2854f093e2c.png)

赞，MyRouter 插件已将初见雏形了，太不容易了！

### MyRouter 类

终于到了完善 MyRouter 类了，在完善之前，我们先来确定一下，MyRouter 类需要做哪些事情。

1. 接收 myRouter 实例化时传入的选项 options，数组类型的路由表 routes、代表当前路由模式的 mode
2. VueRouter 使用时，不光可以使用 path 匹配，还可以通过 name 匹配，为了方便之后的操作，将路由表转换成 key:value 的形式，key 为路由表 routes 配置的 path 或 name
3. 为了区分路由的两种模式，创建 HashRouter 类、HistoryRouter 类，合称为 Router 类。
4. myRouter 实例化判断是 hash 模式还是 history 模式，history 存储实例化的 Router 类
5. 存储当前路由，通过 $myRoute 挂载到每一个实例上，解决之前留的小尾巴
6. 定义 push()、replace()、go()方法

MyRouter 类做的事情还是挺多的，对于程序猿来说，只要逻辑清楚，再多都不怕。那我们就一点一点的来实现吧！先来将 MyRouter 类的基本结构搭起来。

index.js

```
import HashRouter from './hash'
import HistoryRouter from './history'
import Utils from './utils'
class MyRouter {
    constructor(options){
        this.routes = options.routes || [] // 路由表
        this.mode = options.mode || 'hash' // 模式 hash || history
        this.routesMap = Utils.createMap(this.routes) // 路由表装换成 key:value 形式
        this.history = null  // 实例化 HashRouter 或 HistoryRouter
        this.current= {
            name: '',
            meta: {},
            path: '/',
            hash: '',
            query:{},
            params: {},
            fullPath: '',
            component:null
        } // 记录当前路由
        // 根据路由模式，实例化 HashRouter 类、HistoryRouter 类
        switch (options.mode) {
            case 'hash':
                this.history = new HashRouter(this)
            case 'history':
                this.history = new HistoryRouter(this)
            default:
                this.history = new HashRouter(this)
        }
    }
    init(){
        this.history.init()
    }
    push(params){
        this.history.push(params)
    }
    replace(params){
        this.history.replace(params)
    }
    go(n){
        this.history.go(n)
    }
}
export default MyRouter
```
> MyRouter 类初始化的时候，我们实例化的 Router 类放到了 history 中，在使用 MyRouter 类的 push()、replace()、go() 方法，就是在调用 HashRouter 类或 HistoryRouter 类的方法。所以在 HashRouter 类与 HistoryRouter 类中同样需要包含这3个方法。

Utils.createMap(routes) 

```
createMap(routes){
    let nameMap = {} // 以每个路由的名称创建 key value 对象
    let pathMap = {} // 以每个路由的路径创建 key value 对象
    routes.forEach((route)=>{         
        let record = {
            path:route.path || '/',
            component:route.component,
            meta:route.meta || {},
            name:route.name || ''
        }
        if(route.name){
            nameMap[route.name] = record
        }
        if(route.path){
            pathMap[route.path] = record
        }
    })
    return {
        nameMap,
        pathMap
    }
}
```
将路由表 routes 中的每一个路由对象，重新组合，分别调整为以 route.path、route.name 为关键字，value 值为 record 对象的形式。这里只是一个简单的处理，并没有考虑嵌套路由、父组件路由、重定向、props等情况。

还记得之前留关于 $myRoute 的小尾巴吗，在这里我们就可以把它解决掉了。$myRoute 是用于存储当前路由对象，与 MyRouter 类中的 current 属性作用是一样的，install 函数中可以很容易拿到 myRouter 实例，那我们就把 myRouter.current 挂载到 $myRoute 上不就行了，之后只要 URL 发生变化，更新 current 值就可以了。开心，那就更新一下 install.js 吧！ 

install.js 
```
MyRouter.install = function(Vue,options){
    if (this.$options && this.$options.myRouter){ 
        this._myRouter = this.$options.myRouter
        // 新增 myRouter 初始化
        this.$options.myRouter.init()
    }else { 
        this._myRouter= this.$parent && this.$parent._myRouter
    }
    ... ... 
    // 更新 为当前实例添加 $route 属性
    Object.defineProperty(this,'$myRoute',{
        get(){
            return this._myRouter.current
        }
    })
}

```
好了，之前说的 MyRouter 类需要做的 6 件事情，就剩 HashRouter 类与 HistoryRouter 类的实现了，那就抓紧时间吧！

### HashRouter 类

顾名思义，HashRouter 类用来实现 Hash 模式下的路由的跳转。通过定义 MyRouter 类，我们知道 HashRouter 类需要定义3个函数。同时 HashRouter 类需要操作 MyRouter 类中的属性，需要一个参数接收。HashRouter 类的基本框架就出来了。

```
export default class HashRouter {
    constructor(router){
        this.router = router // 存储 MyRouter 对象
    }
    init(){}
    push(params){}
    replace(params){}
    go(n){}
}
```
HashRouter 类最重要的作用就是监听 URL Hash 值的变化，来实现路由跳转。还记得之前原生 JS 实现的 Hash 路由吗，原理是一模一样的，代码直接拿来用都可以。

```
export default class Hash {
    constructor(router){
        this.router = router // 存储 MyRouter 对象
    }
    init(){
        <!-- 页面首次加载时，判断当前路由 -->
        this.createRoute()
        <!-- 监听 hashchange -->
        window.addEventListener('hashchange', this.handleHashChange.bind(this))
    }
    handleHashChange(){
        this.createRoute()
    }
    <!-- 更新当前路由 current  -->
    createRoute(){
        let path = location.hash.slice(1)  
        let route = this.router.routesMap.pathMap[path] 
        // 更新当前路由
        this.router.current = {
            name: route.name || '',
            meta: route.meta || {},
            path: route.path || '/',
            hash: route.hash || '',
            query:location.query || {},
            params: location.params || {},
            fullPath: location.href,
            component: route.component
        }  
    }
    push(params){}
    replace(params){}
    go(n){}
}

```

HashRouter 类与之前原生 JS 实现的 JSHashRouter 类实现原理是一样的，稍有不同的是对 UI 渲染的方式。HashRouter 类需要将路由匹配到的组件用 ```<my-router-view/>``` 渲染出来。

不知道大家有没有注意到，HashRouter 类监听到的路由变化与```<my-router-view/>```组件之间没有任何联系，```<my-router-view/>```组件是直接通过对 location 的操作渲染 UI 的。如何将这两者之间联系起来呢？$myRoute 可以完成这个任务。

HashRouter 类中的 createRoute() 获取 URL 的 hash 值，即 path 值，通过 MyRouter 类中的 routesMap.pathMap 对象，获取到当前路由匹配的路由配置选项，将路由配置选项合并到 myRouter.current 对象中，myRouter.current 通过 $myRoute 挂载到了每个实例中。也就是说，$myRoute.component 就是我们匹配到的路由组件。

view.js
```
export default {
    name: 'MyRouterView',
    functional: true,
    render: (createElement, {props, children, parent, data}) => {
        let temp = parent.$myRoute?parent.$myRoute.component:''
        return createElement(temp)
    }
};
```
```<my-router-view/>```更新完以后是不是清晰多了。代码实现到这里，应该可以实现基本的跳转了，来看一下效果，验证一下！









