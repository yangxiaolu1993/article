

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
接下来，定义 HashRouter 类

```
class HashRouter {
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

HashRouter 类自身定义了 routerView 属性，接收渲染路由 UI 的容器。HashRouter 类定义了2个方法：init() 和 push()

* init()   
 首先页面渲染时，不会触发 window.onhashchange()，根据当前 hash 值，渲染 routerView。监听 window.onhashchange() 事件，一旦事件触发，重新渲染 routerView。

* push()  
 在实际开发过程中，进行路由跳转需要使用 JS 动态设置。通过为 window.location.hash 设置值，实现路由跳转。 这里需要注意，window.location.hash 改变 hash 值，也会触发 window.onhashchange() 事件。

所以不管是 &lt;a&gt; 标签改变 hash，还是 window.location.hash 改变 hash，统一在 window.onhashchange() 事件中，重新渲染 routerView。

接下来只要 HashRouter 实例化，调用就可以了：  
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
        let hashRouter = new HashRouter(routerview)
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
接下来，定义 HistoryRouter 类

```
class HistoryRouter {
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
HistoryRouter 类自身定义了 routerView 属性，接收渲染路由 UI 的容器。HistoryRouter 类定义了3个方法：init() 、push()、replace()。

* init()  
    init(）中主要做了2件事：
    1. window.onpopstate() 事件，用于 history.go()、history.back()、history.forword() 的监听。  
    2. 点击 &lt;a&gt 标签，浏览器 URL 更新为 href 的 URL，e.preventDefault() 阻止默认事件。将 href url 通过 pushState() 更新浏览器 URL，重新渲染 routerView。
* push()
    通过 history.pushState() 更新浏览器 URL，重新渲染 routerView。
* replace()
    通过 history.replaceState() 更新浏览器 URL，重新渲染 routerView。

ok，现在只要实例化 HistoryRouter 类，调用方法就可以了！

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
      let historyRouter = new HistoryRouter(routerview) 
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

#### 基于 Vue 实现 hash 路由














首先了解：如何自定义一个vue插件？vue.install()   vue.use()
1. router-link 与 router-view 组件
2. a 标签实现跳转
3. router-link 初始化为 a 标签
4. 自定义组件，使用render方式
5. 每一个 vue 组件都是一个 vue 实例，只是根实例会有一些不一样的属性，比如$el
6. 父组件与子组件加载时的生命周期的加载顺序
7. vue.mixins 全局混入，一旦使用全局混入，将影响之后每一个创建的vue实例
8. vue.mixins 会在当前 vue 实例之前执行
9. Class 类上会有prototype属性，class类的方法，在prototype对象上可以查到
10. Object.defineProprety() 可以为一个对象定义一个新的属性

11. 路由传参




路由笔记：

主动触发
1、router-link绑定了click方法，触发history.push或者history.replace，从而触发history.transitionTo。
2、transitionTo用于处理路由转换，其中包含了updateRoute用于更新_route。
3、在beforeCreate中有劫持_route的方法，当_route变化后，触发router-view的变化。
地址
地址变化（浏览器栏直接输入地址）

1、HashHistory和HTML5History会分别监控hashchange和popstate来对路由变化作对用的处理 。
2、HashHistory和HTML5History捕获到变化后会对应执行push或replace方法，从而调用transitionTo
,剩下的就和上面主动触发一样啦。
