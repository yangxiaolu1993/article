> 单页 Web 应用（single page web application，SPA），是当今网站开发技术的弄潮儿，仅靠加载单个 HTML 页面就在网站开发中占据了一席之地。很多传统网站正在或者已经转型为单页 Web 应用。单页 Web 应用网站也如雨后春笋般出现在大众眼前。前后端分离技术、MVVM 模式、前端路由、webpack 打包器也随之孕育而生。如果你是一名 Web 应用开发人员，却还没有发开或者甚至不了解单页 Web 应用，那就要加油了！  

在《SPA 路由三部曲之初体验》中我们了解实现前端路由的基础知识。现在我们就利用这些基础知识，加上前端路由的不同技术栈的使用方式，基于 Vue 技术栈自己动手实现类似 vue-router 的前端路由 myRouter。

## 原生 JS 实现路由

在实现 myRouter 之前，先用原生 JS 实现一下。

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

* init() 中主要做了2件事：
    1. window.onpopstate() 事件，用于 history.go()、history.back()、history.forword() 的监听。  
    2. 点击 &lt;a&gt 标签，浏览器 URL 更新为 href 的 URL，e.preventDefault() 阻止默认事件。将 href url 通过 pushState() 更新浏览器 URL，重新渲染 routerView。
* push() 函数，通过 history.pushState() 新增浏览器 URL，重新渲染 routerView。
* replace()函数，通过 history.replaceState() 替换浏览器 URL，重新渲染 routerView。

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

> 细心的同学应该已经发现了，在 HashRouter 类的 init() 方法中，处理了页面首次渲染的情况，但在 HistoryRouter 类中却没有。Hash 模式下，改变的是 URL 的 hash 值，浏览器请求是不携带 hash 值的，所以 http:localhost:8080/#/home 与 http:localhost:8080/#/cart，浏览器请求都是 http:localhost:8080。History 模式下，改变的则是 URL 除锚外的其他部分，所以 http:localhost:8080 与 http:localhost:8080/home 浏览器请求是不同的。这就也就可以解释在 vue-router 下，Hash 模式后端只需要将域名指向 index.html 即可，History 模式后端需要重定向，将域名下匹配不到的静态资源，返回到同一个 index.html 页面。

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

> 按照自己的开发逻辑，将 MyRouter 的基本结构拆分成2个 js 文件 install.js 与 index.js。install.js 文件用于实现 install 方法，index.js 用于实现 MyRouter 类。  

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

Vue Router 定义了2个组件：```<router-link/>```、```<router-view/>```。```<router-link/>```用来路由导航，默认会被渲染成 &lt;a&gt; 标签，通过传入 to 属性定义目标路由。```<router-view/>```用来渲染匹配到的路由组件。

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
        to: {   // 目标导航
            type: [String, Object],
            required: true
        },
        tag:{   // 定义导航标签
            type: String,
            default: 'a'
        },
        event: {  // 触发事件
            type: String,
            default: 'click'
        }
    },
    render: (createElement, {props,parent}) => {
        let toRoute = parent.$myRoute.mode == 'hash'?`#/${props.to}`:`/${props.to}`
        return createElement(props.tag,{
            attrs: {
                href: toRoute
            },
            on:{
                [event]:function(){}
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

正常渲染，没有报错，第一个使用 &lt;p&gt; 标签渲染。MyRouterLink 组件的基本实现先到这里，但还有很多疑问没有解决。渲染成了别的标签，Hash 模式该如何监听 URL 变化？改如何阻止 &lt;a&gt; 标签默认跳转？不要着急，之后会一一解答的。咱们先来按照 MyRouterLink 组件的实现思路，把 MyRouterView 组件实现。   

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
                    path = location.pathname
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

hash.js
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
export default class HashRouter {
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

![](https://storage.360buyimg.com/imgtools/bfd27755f6-5c0bf770-e284-11ea-9f1f-7bf9739df39d.gif)

页面初始渲染显示正常，点击底部导航也能正常跳转，但是为什么页面不更新呢？虽然我们通过 myRouter.current 将 hash 值变化与 MyRouterView 组件建立了联系，但没有对 myRouter.current 进行双向绑定，说白了，MyRouterView 组件并不知道 myRouter.current 的发生了改变。难道要实现双向绑定，当然不用！小编给大家安利一个 Vue 隐藏的 API：Vue.util.defineReactive()，了解过 Vue 源码的童鞋应该知道这个 API，用于定义一个对象的响应属性。那就简单了，在 MyRouter 插件初始化的时候，利用 Vue.util.defineReactive()，使 myRouter.current 得到监听。

install.js
```
MyRouter.install = function(Vue,options){

    Vue.mixin({
        beforeCreate(){
            ......
            Object.defineProperty(this,'$myRoute',{
                ....
            })
            // 新增代码 利用 Vue defineReactive 监听当前路由的变化
            Vue.util.defineReactive(this._myRouter,'current')
        }
    })
}
```
现在在来看看，MyRouterView 组件的内容有没有更新。  
![](https://storage.360buyimg.com/imgtools/680086c4fc-f2861fc0-e287-11ea-b779-5171ebe3afba.gif)

太不容易了，终于得到我们想要的效果了。HashRouter 类的 push()、replace()、go() 方法，直接 copy JSHashRouter 类的代码就实现了。

```
export default class HashRouter {
    ......
    push(params){
        window.location.href = this.getUrl(params)
    }
    replace(params){
        window.location.replace(this.getUrl(params))
    }
    go(n){
        window.history.go(n)
    }
    // 获取当前 URL 
    getUrl(path){
        let path = ''
        if(Utils.getProperty(params) == 'string'){
            path = params
        } else if(params.name || params.path){
            path = params.name?params.name:params.path
        }
        const fullPath = window.location.href
        const pos = fullPath.indexOf('#')
        const p = pos > 0?fullPath.slice(0,pos):fullPath
        return `${p}#/${path}`
    }
}

```
参照 VueRouter，动态导航方法可以是字符串，也可以是描述地址的对象，getUrl 函数处理 params 的各种情况。

HashRouter 类基本功能终于实现完了，其实知道了 HashRouter 类的思路，HistoryRouter 类实现起来就不难了。

### HistoryRouter 类

HistoryRouter 类与 Hash 类一样，同样需要 push()、replace()、go() 动态设置导航，一个自有参数接收 MyRouter 类。唯一不同的就是处理监听 URL 变化的方式不一样。

history.js
```
export default class HistoryRouter {
    constructor(router){
        this.router = router
    }
    init(){
        window.addEventListener('popstate', ()=>{
            // 路由改变
        })
    }
    push(params){}
    replace(params){}
    go(n){}

}
```
通过上面的学习我们知道，history 模式是通过 history.pushState()、history.replaceState()，配合 window.onpopstate() 实现的。同样的HistoryRouter 类的实现依然可以将原生 JS 实现的 JSHistoryRouter 类的代码直接拿过来。

history.js
```
export default class HistoryRouter {
    constructor(router){
        this.router = router
    }
    init(){
        // 监听 popstate
        window.addEventListener('popstate', ()=>{
            <!-- 导航 UI 渲染 -->
            this.createRoute(this.getLocation())
        })
    }
    push(params){
        history.pushState(null, '', params.path)
        <!-- 导航 UI 渲染 -->
        this.createRoute(params)
    }
    replace(params){
        history.replaceState(null, '', params.path)
        <!-- 导航 UI 渲染 -->
        this.createRoute(params)
    }
    go(n){window.history.go(n)}
    getLocation () {
        let path = decodeURI(window.location.pathname)
        return (path || '/') + window.location.search + window.location.hash
    }
}
```
由于 history.pushState 与 history.replaceState 对浏览器历史状态进行修改并不会触发 popstate，只有浏览器的后退、前进键才会触发 popstate 事件。所以在通过 history.pushState、history.replaceState 对历史记录进行修改、监听 popstate 时，都要根据当前 URL 进行导航 UI 渲染。除了进行 UI 渲染，还有非常重要的一点，对 router.current 的更新，只要将其更新，<my-router-view/> 组件才会更新。    

history.js
```
export default class HistoryRouter {
    ......
    createRoute(params){
        let route = {}
        if(params.name){
            route = this.router.routesMap.nameMap[params.name]
        }else{
            let path = Utils.getProperty(params) == 'String'?params:params.path
            route = this.router.routesMap.pathMap[path]
        }
        // 更新路由
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
}
```
细心的同学可能发现了问题，在原生 JS JSHistoryRouter 类中，将 &lt;a&gt; 标签的点击事件进行了改造，阻止了默认事件，使用 pushState 实现路由导航。HistoryRouter 类中为什么没有添加这个逻辑呢？不知道还记不记得 <my-router-link/> 的实现，按照现在 <my-router-link/> 的设计思路，最终渲染出来的不止是 &lt;a&gt; 标签，还可以是用户定义的 &lt;p&gt、&lt;li&gt 等等。但只有 &lt;a&gt; 标签才能修改 URL ，其他标签都做不到，没办法进行路由导航。换个思路，就算在 HistoryRouter 类中阻止了 &lt;a&gt; 标签的默认事件，也无法判断当前的 &lt;a&gt; 标签是用于路由导航呢，还是用户自己添加的呢？

综上所述，我们需要对 <my-router-link/> 组件的 render 函数进行升级改造，阻止 &lt;a&gt; 标签的默认事件，导航标签都通过 pushState 改变进行路由导航。

link.js
```
export default {
    ......
    render: (createElement, {props,parent,children}) => {    
        let toRoute = parent.$myRouter.mode == 'hash'?`#`:``
        <!-- 路由导航匹配 -->
        if( props.to.name ){
            let current = props.to.name
            toRoute += parent._myRouter.routesMap.nameMap[current].path
        } else {
            toRoute += Utils.getProperty(props.to) == 'String'?props.to:props.to.path
        }
        let on = {'click':guardEvent} 
        on[props.event] = e=>{
            // 阻止导航标签的默认事件
            guardEvent(e)
            // props.to 的值传到 router.push()
            parent.$myRouter.push(props.to)
        }
        return createElement(props.tag,{
            attrs: {
                href: toRoute
            },
            on,
        },children)
    }
};
function guardEvent(e){
    if (e.preventDefault) {
        e.preventDefault()
    }
}
```
好了，<my-router-link/> 也改造完了，咱们来证明一下，代码的正确性吧！ 

![](https://storage.360buyimg.com/imgtools/ff7a04d9d5-8f292dd0-e379-11ea-98e1-c5d2b444bf4a.gif)  

完美实现，有点小小的佩服自己！与 vue-router 相比，还有很多的功能没有实现，比如 <keep-alive/>、路由导航守卫、过渡动画等等，myRouter 插件只是实现了简单的路由导航和页面渲染。往往一件事情最难的是第一步，MyRouter 已经开了一个不错的头，实现之后的功能也不是问题。

欢迎大家期待之后《SPA 路由三部曲之进阶篇》，在这篇文章中，小编将带领大家进入 vue-router 的源码世界。相信在了解了 vue-router 的实现思路后，大家就都可以实现自己的前端路由。




