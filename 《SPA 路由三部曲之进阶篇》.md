

 >单页 Web 应用（single page web application，SPA），是当今网站开发技术的弄潮儿，仅靠加载单个 HTML 页面就在网站开发中占据了一席之地。很多传统网站正在或者已经转型为单页 Web 引用。单页 Web 应用网站也如雨后春笋般出现在大众眼前。前后端分离技术、MVVM 模式、前端路由、webpack 打包器也随之孕育而生。如果你是一名 Web 应用开发人员，却还没有发开或者甚至不了解单页 Web 应用，那就要加油了！

 Vue-Router 是 Vue 应用的前端路由插件，其使用方法很简单，就算是刚上手的小白，给他随便说两句就能轻松使用。兵法讲究的是：知己知彼，才能百战不殆，代码也是一样的，了解了 Vue-Router 的源码，在难的问题也就迎刃而解了。咱们闲话休提，书归正传，小编将从 XXX 几方面讲解 Vue-Router 源码。

## 整体把握

Vue-Router 的源码代码量不算很多，但是内容却也不少，函数一层一层的嵌套。为了能更好的帮助大家整体把握 Vue-Router 源码，小编先从整体设计思路、项目结构、重要函数三个方面介绍 vue-router。

### 设计思路

1、 使用的 Hash 模式路由其实是 History 模式路由

阅读源码之前，小编对 vue-router 的实现存在一个错误的认知：Hash 模式下的路由是通过使用 hashChange 事件监听 URL 的 Hash 值的改变实现的。其实不然，小编并不是否定这种想法，而且这种想法是存在不完善。Vue-Router 的设计大神对 Hash 模式路由与 History 模式路由做了兼容处理。在浏览器支持 history 新特性的前提下，就算 mode 值为 hash，vue-router 内部使用的依然是 history.pushState 与 stateChange 完成的路由导航。同样的，如果浏览器不支持 history 新特性，history 模式的路由也会优雅降级为 hash 模式。

> 在 ./util/push-state.js 文件中，定义了 supportsPushState 函数，用于判断是否支持 history 的新特性。

2、VueRouter 实例化

通过 new VueRouter 创建 vueRouter 实例时，会通过深度遍历把传入的路由配置项 routes 进行映射解析，保存到 pathMap、nameMap 中，每个 Map 对应一个路由记录，即 RouteRecord。不管在哪种路由模式下，触发路由导航时，都会调用 transitionTo 方法，匹配到目标路由对应的 RouteRecord，通过 confirmTransition 方法完成导航守卫、与 history.current 重新赋值，触发 Vue.util.defineReactive 监听事件, 实现路由切换。

> 在 VueRouter.install 方法中，通过 ```Vue.util.defineReactive(this, '_route', this._router.history.current)``` 把 _route 变成响应式，即 this._router.history.current，_route 通过 Object.defineProperty 挂载到了 vue 实例的 $route 上，也就是说，在咱们经常使用的 this.$route 是响应式的。当 confirmTransition 方法改变 history.current 的值时，_route 就会重新赋值，触发 ```<router-view />``` 组件进行重新渲染。

3、```<router-link />``` 组件

<router-link /> 组件渲染时，会利用 prop to 属性，遍历 Map 里的每个路由记录 RouteRecord 找到正确的渲染组件，通过 Vue Render 函数将 <router-link/> 渲染成 prop tag 设置的标签，默认是 a 标签。<router-link /> 进行路由导航时，默认情况下，阻止了 a 标签的默认点击事件，通过添加的 click 事件完成触发导航任务。通过 prop event 传入的其他事件，也会进行与 click 有相同作用的事件绑定。

4、```<router-view />``` 组件

在 VueRouter 实例化时，this.$route 变成了响应式数据，监听 history.current 值的变化。在 $route.matched 上保存了目标路由所有父链路路由对应的路由记录 RouteRecord，包括目标路由的 RouteRecord。获取当前 <router-view /> 相对于最顶层的根 Vue 实例所嵌套的深度的层次数，在 matched 数组中找到目标路由需要渲染的组件，完成 keep-alive 组件与非 keep-alive 组件渲染。

### 项目结构

团队协作最重要的一点就是：分工明确、各司其职，一个好的项目结构也是如此，需要每一个文件都有明确的功能。所以在源码阅读前，要尽可能的先掌握项目的整体结构，才不会造成越看越晕的困境。VueRouter 的项目结构是很清晰、简单的。

```
├── src
│   ├── components
│   │   ├── link.js                // <router-link/> 的实现
│   │   └── view.js                // <router-view/> 的实现
│   ├── create-matcher.js          // 根据 routes 配置对象生成 match()、addRoutes()
│   ├── create-route-map.js        // 根据 routes 配置对象创建路由映射表 ，生成 pathList/nameMap/pathMap
│   ├── history                    
│   │   ├── abstract.js            // 非浏览器 history 类
│   │   ├── base.js                // HashHistory 类与 HTML5History 类的公共类，包括 transitionTo()、confirmTransition() 重要函数
│   │   ├── hash.js                // hash 模式下 HashHistory 类
│   │   └── html5.js               // history 模式的 HTML5History 类
│   ├── index.js                   // 入口文件 vue-router 类
│   ├── install.js                 // vue 插件调用的 install 函数
│   └── util                       // 工具类，包括 route.js、push-state.js、location.js
```
与其他单页面应用一样，Vue-Router 的入口文件是 src/index.js。

### 重要函数

在 VueRouter 类中有几个关键的函数，对掌握掌握主流程和原理有很大的帮助。

1、路由记录 RouteRecord

```
const record: RouteRecord = {
    path: normalizedPath,
    regex: compileRouteRegex(normalizedPath, pathToRegexpOptions),
    components: route.components || { default: route.component },
    instances: {},
    name,
    parent, //嵌套路由
    matchAs,
    redirect: route.redirect,
    beforeEnter: route.beforeEnter,
    meta: route.meta || {},
    props: route.props == null ? {} : route.components ? route.props : { default: route.props }
}
```
 路由记录是在 ./create-route-map.js 中的 addRouteRecord() 函数中定义的。

* regex  
 通过 path-to-regexp 生成路由正则，为了匹配嵌套路由，比如：{ path: '/my/:userId'}
* components   
保存路由中设置的组件。Router 构建选项 routes:RouteConfig 定义了两个属性用于设置组件：component 单个视图组件与 components 命名视图组件。日常开发中大多是使用 component 定义视图组件，components 命名视图组件与其最大的缺别就是路由下的组件是同级的，而不是嵌套的。在 vue-router 组件内部会将两者合并，单个视图组件赋值为 default。
* parent   
记录当前路由对应的嵌套路由中上一层父路由的路由记录对象，根路由的 parent 为 null。
```
const routes = [{
  path: '/',
  component: Home,
  children:[{
    path: 'child',
    component: Child,
  }]
}]
```
代码中所示的嵌套路由配置，在 ```/child``` 的路由记录中，parent 记录的就是 ```/```的路由记录。

2、路由对象 route，即 $route

```
const route: Route = {
    name: location.name || (record && record.name),
    meta: (record && record.meta) || {},
    path: location.path || '/',
    hash: location.hash || '',
    query,
    params: location.params || {},
    fullPath: getFullPath(location, stringifyQuery),
    matched: record ? formatMatch(record) : []
}
```
路由对象 route 与上面提到的路由记录 record 是有区别的，后者是在初始化 VueRouter 时，将每个路由按照标准整理成对象，前者是通过路由记录得到最终的 route 对象，前者依赖于后者。

route 对象是在 ./util/route.js 的 createRoute() 函数中定义的，通过 VueRouter 类的 match() 函数调用。除了 matched，了解 VueRouter 的同学对其他属性应该都不陌生。

formatMatch 函数通过深度循环遍历 record.parent，使 matched 属性记录了当前路由对应的所有嵌套路由片段的路由记录，也就是所有父路由对象都在这个数组里面，包含了当前路由的路由信息，其在路由导航守卫中其中关键的作用。路由切换时，通过对比目标路由 matched 与 当前路由 matched ，找到需要要被销毁的组件（deactivated）、要被激活的组件（activated）和需要更新的组件（updated），runQueue() 函数完成路由导航守卫。

```
const routes = [{
  path: '/home',
  component: Home,
  children:[{
    path: 'childOne',
    component: ChildOne,
  },{
    path: 'childTwo',
    component: ChildTwo,
  }]
}]
```
按照 Vue-Router 的设计思路，```/home/childOne``` 对应的 route.matched 为 ```[/home 路由记录，/childOne 路由记录]```，```/home/childTwo``` 对应的 route.matched 为 ```[/home 路由记录，/childTwo 路由记录]```。

## VueRouter 类

我们先从 VueRouter 的入口 index.js 开始解析。

![](https://img11.360buyimg.com/imagetools/jfs/t1/136305/5/7923/513653/5f433989Ec08e2e21/58f33ddbdb66e578.png)

VueRouter 的本质就是一个类，其中定义了很多的属性和方法。很多的方法与 MyRouter 是一样，大家可以参考 MyRouter 的创建过程，就不做详细介绍了，这里主要介绍 VueRouter 中核心的函数。

### this.matcher

我们可以观察到 route 对象通过 this.match() 获取，match 又是通过 this.matcher.match()，而 this.matcher 是通过 createMatcher 函数处理，createMatcher 函数做了什么事情呢？

**createMatcher()**
createMatcher 函数相关的实现都在 src/create-matcher.js中。
```
/**
 * 创建 createMatcher 
 * @param {*} routes 路由配置选项
 * @param {*} router 路由实例
 */
export function createMatcher (
  routes: Array<RouteConfig>,
  router: VueRouter
): Matcher {
  const { pathList, pathMap, nameMap } = createRouteMap(routes)
  function addRoutes (routes) {
    createRouteMap(routes, pathList, pathMap, nameMap)
  }
  function match (
    raw: RawLocation,
    currentRoute?: Route,
    redirectedFrom?: Location
  ): Route {}
    ......
  return {
    match,
    addRoutes
  }
}
```
从上面简化后的代码可以看出来，createMatcher 接收2个参数，routes 是 new VueRouter 实例化时，用户定义的路由配置，router 是 new VueRouter 返回的实例。routes 是一个定义了路由配置的数组，通过 createRouteMap 函数处理为 pathList, pathMap, nameMap。createMatcher 最终返回了一个对象 {match, addRoutes} 。也就是说 matcher 是一个对象，它对外暴露了 match 和 addRoutes 方法。

match 和 addRoutes 方法的定义都用到了 pathList, pathMap, nameMap ，那我们就先来看一下，createRouteMap 是如何定义这3个对象的。

**createRouteMap()**
createRouteMap 函数相关的实现都在 src/create-route-map.js中。

```
export function createRouteMap (
  routes: Array<RouteConfig>,
  oldPathList?: Array<string>,
  oldPathMap?: Dictionary<RouteRecord>,
  oldNameMap?: Dictionary<RouteRecord>
): {
  pathList: Array<string>,
  pathMap: Dictionary<RouteRecord>,
  nameMap: Dictionary<RouteRecord>
} {
  // pathList 被用于控制路由匹配优先级
  const pathList: Array<string> = oldPathList || []
  // 路径路由映射表
  const pathMap: Dictionary<RouteRecord> = oldPathMap || Object.create(null)
  // 路由名称路由映射表
  const nameMap: Dictionary<RouteRecord> = oldNameMap || Object.create(null)
  routes.forEach(route => {
    addRouteRecord(pathList, pathMap, nameMap, route)
  })
  // 确保通配符路由总是在最后
  for (let i = 0, l = pathList.length; i < l; i++) {
    if (pathList[i] === '*') {
      pathList.push(pathList.splice(i, 1)[0])
      l--
      i--
    }
  }
  ......
  return {
    pathList,
    pathMap,
    nameMap
  }
}
```
createRouteMap 函数主要是把用户的路由匹配选项按照一定的规则转换成几张路由映射表，后面路由切换就是依据这几个映射表，这几张路由表是很重要的。从上面的代码可以看出，createRouteMap 为每一个 route 执行 addRouteRecord 方法生成一条记录。

**addRouteRecord()**
```
function addRouteRecord (
  pathList: Array<string>,
  pathMap: Dictionary<RouteRecord>,
  nameMap: Dictionary<RouteRecord>,
  route: RouteConfig,
  parent?: RouteRecord,
  matchAs?: string
) {
    const { path, name } = route
    ......
    // 先创建一条路由记录
    const record: RouteRecord = {...}
    // 如果该 route 是嵌套路由（有子路由），循环遍历解析嵌套路由
    if (route.children) {
        ......
        route.children.forEach(child => {
            const childMatchAs = matchAs
                ? cleanPath(`${matchAs}/${child.path}`)
                : undefined
            addRouteRecord(pathList, pathMap, nameMap, child, record, childMatchAs)
        })
    }
    // 如果有多个相同的路径，只有第一个起作用，后面的全部忽略
    // 为 pathList、pathMap 添加一条记录
    if (!pathMap[record.path]) {
        pathList.push(record.path)
        pathMap[record.path] = record
    }
    // 如果 route 中设置了 name 属性，为 nameMap 添加一条记录
    // 如果有多个相同的 name，只有第一个起作用，后面的全部忽略
    if (name) {
        if (!nameMap[name]) {
            nameMap[name] = record
        } 
    }
}
```
addRouteRecord 函数，先创建一条路由记录对象。如果当前的路由记录有嵌套路由的话，就循环遍历继续创建路由记录，并按照路径和路由名称进行路由记录映射。这样所有的路由记录都被记录了。路由记录对象 RouteRecord 都记录了哪些内容：

```
const record: RouteRecord = {
    path: normalizedPath,    
    regex: compileRouteRegex(normalizedPath, pathToRegexpOptions), 
    components: route.components || { default: route.component },
    instances: {},
    name,
    parent,
    matchAs,
    redirect: route.redirect,
    beforeEnter: route.beforeEnter,
    meta: route.meta || {},
    props:
      route.props == null
        ? {}
        : route.components
          ? route.props
          : { default: route.props }
}
```

RouteRecord 是一个对象，包含了一条路由的所有信息: 路径、路由正则、组件实例、路由名称、重定向等等。

* regex：通过 path-to-regexp 生成路由正则，为了匹配嵌套路由，比如：{ path: '/my/:userId'}
* parent：嵌套路由中父路由的路由记录对象
* matchAs：嵌套路由子路由匹配标记
* matched 是当前路由记录对应的所有嵌套路劲片段的路由记录，也就是所有父路由对象都在这个数组里面，包含了当前页面的路由信息

createRouteMap 方法执行后，我们就可以得到由所有路由记录组成的 RouteRecord 树型结构。并得到 path、name 对应的路由映射。通过 path 和 name 能在 pathMap 和 nameMap 快速查到对应的 RouteRecord。

好了，我们回到 createMatcher，还记得返回值中有一个 match 函数吗？接下来看一下 match 的实现。

**match()**

```
/**
 * @param {*} raw 目标路由，字符串或对象
 * @param {*} currentRoute  当前路由
 * @param {*} redirectedFrom  重定向（可忽略）
 */
 function match (
    raw: RawLocation,
    currentRoute?: Route,
    redirectedFrom?: Location
  ): Route {
    // 将目标路由统一成标准的形式
    const location = normalizeLocation(raw, currentRoute, false, router)
    const { name } = location

    // 如果有路由名称 name, 就进行 nameMap 映射 
    // 获取到路由记录，处理路由 params 返回 _createRoute 的返回值
    if (name) {
      const record = nameMap[name]
      ......
      if (!record) return _createRoute(null, location)
      // 获取嵌套路由中动态路径的 name，如{path:"/my/:userId"}中，最终 paramNames 的值为 ”userId“
      const paramNames = record.regex.keys
        .filter(key => !key.optional)
        .map(key => key.name)
      ......
      //当前路由存在 params 参数，若 目标路由没有设置此参数 && 路由配置选项 path 中设置，则目标路由设置此 params 参数
      if (currentRoute && typeof currentRoute.params === 'object') {
        for (const key in currentRoute.params) {
          if (!(key in location.params) && paramNames.indexOf(key) > -1) {
            location.params[key] = currentRoute.params[key]
          }
        }
      }
      location.path = fillParams(record.path, location.params, `named route "${name}"`)
      return _createRoute(record, location, redirectedFrom)

    // 如果路由配置了 path，到 pathList 和 PathMap 里匹配到路由记录 
    // 如果符合 matchRoute 就返回 _createRoute 的返回值
    } else if (location.path) {
      location.params = {}
      for (let i = 0; i < pathList.length; i++) {
        const path = pathList[i]
        const record = pathMap[path]
        if (matchRoute(record.regex, location.path, location.params)) {
          return _createRoute(record, location, redirectedFrom)
        }
      }
    }
    // 没有任何匹配
    return _createRoute(null, location)
  }
```
从上面的代码可以知道，match 函数入参为：目标路由(raw)，当前路由(currentRoute)、重定向路由(redirectedFrom)，其中目标路由(raw)为必传，根据 pathList, pathMap, nameMap 映射表，匹配到正确的 RouteRecord 记录。作为 _createRoute 函数的入参，返回 _createRoute 的返回值，也就是 _createRoute 的返回值就是 match 函数的返回值。

**createRoute 创建路由对象**

_createRoute 函数很简单，_createRoute 函数根据有是否有路由重定向、路由重命名做不同的处理。其中 redirect 函数和 alias 函数最后还是调用了 _createRoute，最后都是调用了 createRoute。createRoute 函数相关的实现在 util/route.js 中

```
/**
 * @param {*} record 目标路由记录对象（非必传，null）
 * @param {*} location  目标路由对象（必传）
 * @param {*} redirectedFrom  重定向
 * @param {*} router  VueRouter 实例
 */
export function createRoute (
  record: ?RouteRecord,
  location: Location,
  redirectedFrom?: ?Location,
  router?: VueRouter
): Route {
  const stringifyQuery = router && router.options.stringifyQuery
  let query: any = location.query || {}
  ......
  // 定义路由对象，即 this.$route
  const route: Route = {
    name: location.name || (record && record.name),
    meta: (record && record.meta) || {},
    path: location.path || '/',
    hash: location.hash || '',
    query,
    params: location.params || {},
    fullPath: getFullPath(location, stringifyQuery),
    matched: record ? formatMatch(record) : []
  }
  ......
  return Object.freeze(route)
}
```
createRoute 可以根据 record 和 location 创建出来最终返回 Route 对象，即 this.$route，通过 Object.freeze(route) 使其外部不可以修改，只能访问。Route 对象中有一个非常重要的属性是 matched，它是通过 formatMatch(record) 计算的：

```
function formatMatch (record: ?RouteRecord): Array<RouteRecord> {
  const res = []
  while (record) {
    res.unshift(record)
    record = record.parent
  }
  return res
}
```
通过 record 循环向上找 parent，直到找到最外层，并把所有的 record 都 push 到一个数组中，最终返回 record 数组，这个 matched 为后面的嵌套路由渲染组件提供了重要的作用。


> matcher 的主流程就是通过 createMatcher 返回一个对象 {match, addRoutes}, addRoutes 是动态添加路由用的，平时使用频率比较低，match 很重要，返回一个路由对象，这个路由对象上记录当前路由的基本信息，以及路径匹配的路由记录，为路径切换、组件渲染提供了依据。

#### history.transitionTo()

好了，我们再次回到 VueRouter 的入口文件 index.js 文件。执行 new VueRouter() 实例化时，根据不同的 mode 配置 history 实例，接着调用了 init() 方法。我们发现不管是哪种路由模式，init 函数最后都调用了 history.transitionTo，进行路由初始化匹配。而且包括 history.push、history.repalce 最后都是通过它进行的路由切换。

history.transitionTo 函数相关实现在 history/base.js 中。

```
transitionTo (
    location: RawLocation,
    onComplete?: Function,
    onAbort?: Function
) {
    // 调用 match 方法返回目标路由 route 对象
    let route = this.router.match(location, this.current)
    // 过渡处理
    this.confirmTransition(
        route,
        () => {
            const prev = this.current
            // 更新当前路由为目标路由 this.current = route
            this.updateRoute(route)
            onComplete && onComplete(route)
            // 更新 url 地址，在浏览器支持的情况下，两种路由均通过 pushState/replaceState 来更新
            this.ensureURL()
            // 全局路由钩子
            this.router.afterHooks.forEach(hook => {
                hook && hook(route, prev)
            })
            .....
        },
        // 错误跳转处理
        err => {......}
    )
}
```
transitionTo 接收3个入参：location、onComplete、onAbort，分别是目标路由、路经切换成功的回调、路径切换失败的回调。transitionTo 函数主要做了两件事：首先根据目标路径 location 和当前的路由对象通过 this.router.match 方法去匹配到目标 route 对象。执行 confirmTransition 方法进行真正的路由切换。

```
confirmTransition (route: Route, onComplete: Function, onAbort?: Function) {
    const current = this.current
    // 错误处理
    const abort = err => {...}
    const lastRouteIndex = route.matched.length - 1
    const lastCurrentIndex = current.matched.length - 1
    // 如果目标路由与当前路由相同，直接 return
    if (
      isSameRoute(route, current) &&
      lastRouteIndex === lastCurrentIndex &&
      route.matched[lastRouteIndex] === current.matched[lastCurrentIndex]
    ) {
      this.ensureURL()
      return abort(createNavigationDuplicatedError(current, route))
    }
    
    // 通过对比路由解析出可复用的组件，需要渲染的组件，失活的组件
    const { updated, deactivated, activated } = resolveQueue(
      this.current.matched,
      route.matched
    )
    // 在异步队列中执行响应的钩子函数
    // 通过 queue 保存相应的路由钩子函数
    const queue: Array<?NavigationGuard> = [].concat(...)

    this.pending = route
    const iterator = (hook: NavigationGuard, next) => {......}
    runQueue(queue, iterator, () => {......})
  }
```
confirmTransition 函数是路由切换的核心函数。其接收3个入参：route、onComplete、onAbort，分别是：目标路由、成功匹配路由后的操作，匹配路由后的操作。confirmTransition 函数比较复杂，我们拆开来看，先看一下 resolveQueue 函数。

**resolveQueue()**

```
function resolveQueue (
  current: Array<RouteRecord>,
  next: Array<RouteRecord>
): {
  updated: Array<RouteRecord>,
  activated: Array<RouteRecord>,
  deactivated: Array<RouteRecord>
} {
  let i
  const max = Math.max(current.length, next.length)
  for (i = 0; i < max; i++) {
      // 当前路由路径和目标路由路径不同时跳出遍历
    if (current[i] !== next[i]) {
      break
    }
  }
  return {
    updated: next.slice(0, i), // 可复用的组件
    activated: next.slice(i), // 需要渲染的组件对应路由
    deactivated: current.slice(i) // 失活的组件对应路由
  }
}
```
resolveQueue 接受2个入参：current 和 next，分别为：当前路由的 matched 和目标路由 matched。在介绍 createRoute 函数时，我们着重说明了 formatMatch 函数，matched 中记录了当前子路由记录的所有父路由记录。resolveQueue 函数通过遍历对比两个路由记录数组，当有两个路由记录不一样的时候就记录这个位置 i，并终止遍历。对于目标路由(next) 从 0 到 i 和当前路由(current) 都是一样的，从 i 开始不同，目标路由(next) 从 i 之后为 activated 部分，当前路由(current) 从 i 之后为 deactivated 部分，相同部分为 updated，由 resolveQueue 处理之后就能得到路由变更需要更改的部分。紧接着根据路由的变更执行一系列的钩子函数。

```
const queue: Array<?NavigationGuard> = [].concat({
    // 失活的组件钩子
    extractLeaveGuards(deactivated),
    // 全局 beforeEach 钩子
    this.router.beforeHooks,
    // 在当前路由改变，但是该组件被复用时调用
    extractUpdateHooks(updated),
    // 需要渲染组件 enter 守卫钩子
    activated.map(m => m.beforeEnter),
    // 解析异步路由组件
    resolveAsyncComponents(activated)
})
```

到现在为止，我们已将 VueRouter 初始化、路由切换的核心代码讲解完了。路由始终会维护当前的线路，路由切换的时候会把当前线路切换到目标线路，切换过程中会执行一系列的导航守卫钩子函数，会更改 url，同样也会渲染对应的组件，切换完毕后会把目标线路更新替换当前线路，这样就会作为下一次的路径切换的依据。


