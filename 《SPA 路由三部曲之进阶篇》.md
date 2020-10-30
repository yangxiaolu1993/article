

 > 在《SPA 路由三部曲之初体验》、《SPA 路由三部曲之实战篇》两篇文章中，分别介绍了前端路由的基础知识、通过 MyRouter 插件实现了前端路由基本功能。相信大家已经迫不及待的想要进入 vue-router 的源码世界了。小编马上实现大家的愿望，话不多说，步入正题。

 与 VueRouter 的源码的实现思路是有一些出入的，主要是 hash 模式的实现方式，网上大部分对于 hash 路由的实现原理都是通过 window.location.hash 改变 hash 值，changehash 监听 hash 值的变化，实现路由导航。但在 VueRouter 中却不是这样的，先卖个关子，继续往下看！

## 整体结构

在项目开发之前，项目的目录结构都是确定好的，每一个文件都有自己明确的功能。源码阅读也是一样，在这之前，要尽可能的先掌握整体结构。

```
├── src
│   ├── components
│   │   ├── link.js                // <router-link/> 的实现
│   │   └── view.js                // <router-view/> 的实现
│   ├── create-matcher.js          // 根据传入的配置对象创建路由映射表
│   ├── create-route-map.js        // 根据routes配置对象创建路由映射表 ，生成 pathList/nameMap/pathMap
│   ├── history                    
│   │   ├── abstract.js            // 非浏览器 history 类
│   │   ├── base.js                // 基本的 history 类
│   │   ├── errors.js              // 错误、警告
│   │   ├── hash.js                // hash 模式的 hashhistory 类
│   │   └── html5.js               // history 模式的 history 类
│   ├── index.js                   // 入口文件 vue-router 类
│   ├── install.js                 // vue 插件 install 函数
│   └── util                       // 工具类
```
VueRouter 的目录结构是很清晰、简单的，入口文件是 src/index.js。

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


