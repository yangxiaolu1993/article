本文由 JDC 前端&大客福利场景业务部尹桂兰共同撰写。

> 窗外日光弹指过，席间花影坐前移。2019 年以飞一般的速度逝去，2020 年在新冠病毒的扰乱下不期而遇。病毒是无情的，它限制了人们的脚步，足不出户，但是这并不能阻止我们思想前进的脚步。2019 年对于负责大客户 - 酷兜项目的童鞋来说是异常艰难的一年，需要同时维护 H5 外接版、内嵌微信小程序两套代码。幸运的是我们有相当靠谱的团队，凭借整个团队（产品、后台、测试）的紧密协作，一路披荆斩棘，完成了 10+ 个版本的迭代和几十个日常优化。

从 2018 年 11 月酷兜上线以来，经历了试运营、市场推广探索，重启调整，再次投入市场正式运营的过程。2019 年酷兜经历了多达 13 次的大小版本迭代，业务侧计划 2020 年大力推广酷兜，不断加大提升酷兜营销能力，完善产品体验，达到市场预期。于是酷兜新版被寄托着很大期望。

![](https://img14.360buyimg.com/imagetools/jfs/t1/115185/23/2918/329926/5ea508afEc5e93e2c/84c80fae1c7363f2.png) 

### 什么是酷兜 

首先请允许我介绍一下酷兜，一张图概括
  ![](https://img14.360buyimg.com/imagetools/jfs/t1/113676/26/2049/565417/5e9ef366Ee564b3ce/ef3b067048154ebe.png) 

酷兜是京东为优质大型企业客户专门打造的“ 0 预算”员工内购福利平台。酷兜内购商城通过整合包括热销商品、品牌折扣、优选精品、生活服务等优质资源，以内部惠购的方式为企业客户的员工提供福礼特权“酷”体验。 到底“酷”在哪呢？  

* 每日更新上万款基于京东商城精选的优质自营商品
* 覆盖 7 大消费场景、时尚穿搭、食品酒水、个人美妆、母婴童装等 29 个京东主营类目  
* 提高性价比，平均商品折扣在 7.4 折哟
* 支持基于锦礼平台小程序或外部系统对接环境下使用，外接版可打通联合登录账号
* 完备的物流与售后服务，拥有全球领先的中小件、大件、冷藏冷冻仓配一体化物流设施
* 支持微信支付，匹配主流在线支付通路
* 以三级类目维度针对 SKU 进行限购处理，最大限度降低分销的风控风险 

说了这么多，有没有好奇酷兜的庐山真面目呢，请看！
![](https://img13.360buyimg.com/imagetools/jfs/t1/101767/10/19487/835066/5e9efaecE918ce00a/cd303314c376580d.png)
当看到 3 折、5 折商品，且平均折扣力度达到 7.4 折的时候，我相信很多童鞋都会有心痒痒的感觉啦，想加入到酷兜的“酷”体验中。俗话说：心动不如行动，如果有合作的企业客户想通过“ 0 ”福利预算来进一步提升员工的福礼待遇，酷兜是一个不错的选择呦！   

好了，广告宣传部分到此结束，接下来我们将从前端架构、重构性能优化、技术拓展三方面阐述酷兜重构优化！

![](https://img11.360buyimg.com/imagetools/jfs/t1/119386/18/1514/136981/5ea4ec87Ea37e51c6/cdba41833470ae3c.png)
### 前端架构
#### 重构的起因
酷兜在 2020 年要搭建完备的内购价格体系，整合今日上新、折扣榜等营销模块，实现系统自动整合商品为员工推荐全网最低内购商品等一系列产品目标。
  
但现实情况是，酷兜前端框架是 2018 年 10 月创建，使用的技术比较老旧。在这一年多的时间里，需求迭代、功能优化、bug 修改、开发人员的更换造成代码冗余严重，逻辑不清晰、开发联调困难。看似简单的需求修改，往往内含玄机，牵一发而动全身。前端技术日新月异，底层框架版本过低，不单单增加了开发人员的兼容性处理，而且无法实现流畅的交互效果，用户体验不佳。   

在几次的版本迭代中，产品都会提出疑问，一个小小的需求，为什么需要这么长的开发时间。说实话，有些需求、bug 在我们看来也是分分钟搞定的事情，可以在酷兜中，就说不准了，一处的改动可能会影响到多处的状态，一个 bug 的解决是以产生多个 bug 为代价的。这简直成为了 bugfest (臭虫集会)。

![](https://img11.360buyimg.com/imagetools/jfs/t1/119636/18/1485/25700/5ea4ebffE0091f0d9/ca7d673b7810bcd6.jpg)  

面对这一系列的问题，也为了更好的满足 2020 年酷兜快速迭代更新的需求以及性能要求，提升用户体验。酷兜团队成员一致认同，前端重构走起！

> 作为负责酷兜的前端开发人员，允许我在这里小小的 happy 一下，终于可以我的代码我做主了。

#### 前端架构优化
随着技术的发展，前端承担的业务越来越多，项目也越来越变得像大型工程了，而且越来越复杂了，需要处理好组员之间的协作，也需要做好业务分块、去耦合来降低维护成本，并且还要保持高效率开发。工程目录结构的优化是能达到这个目的的一种方式。一般而言，不论多页面工程还是单页面应用，或二者都有，目录结构大致都是以下三种方式：  
* 类型分组（文件类型/业务类型等进行分组）
* 模块分块（页面模块/业务模块等进行分块）
* 类型分组与模块分块的结合   

此次重构，采用的是 Vue2 + [ NutUI ](http://nutui.jd.com) + TypeScript + [ Gaea ](https://github.com/jdf2e/Gaea4) 技术栈，在目录结构优化方式上，选择第二种：模块分块，先来看一下整体目录结构。

```
├── .bin                                # Webpack 配置文件
├── build                               # 打包文件
├── node_modules                        # 依赖的模块包（NutUI、postcss-plugin-px2rem）
├── package.json                        # 项目基本信息
├── src                                 # 项目的核心组件
│   ├── asset                           # 资源文件（css、image）
│   ├── component                       # 公共组件
│   ├── config                          # 环境配置文件（evn.ts）
│   ├── icons                           # 存放 svg 格式图标
│   ├── services                        # HTTP 请求配置（HttpClient.ts、GoodsApiService.ts）
│   ├── store                           # 状态管理（vuex）
│   ├── view                            # 根据业务场景开发的组件
│   ├── util                            # 公共方法(util.ts、imgSet.ts、appHelper.ts)
│   ├── app.vue                         # 根组件
│   ├── app.ts                          # 入口文件
│   ├── router.ts                       # 页面路由
│   ├── index.html                      # 主页模板
│   ├── vue-shim-extend.d.ts            # 扩展 Vue 全局类型声明
│   └── vue-shim.d.ts                   # TypeScript 支持 *.vue 文件配置
├── static                              # 静态资源（ico图标、vendor.dll.js）
├── README.md                           # 项目描述信息（一些方法使用的注意事项）
└── tsconfig.json                       # TypeScript 编译设置

```
有没有感觉这样的工程目录结构很清晰（小小的自恋一下）。其实，工程目录结构的设计并没有完成采用模块分块的方式，在 asset 资源文件中，按照文件类型分成了 css、image 文件。虽然对整体目录结构做了调整，但是每个工作空间下的目录结构开发人员可以自由发挥，我们也约定了创建文件的几点规范  
* 在开发业务场景组件中，按照路由划分，该文件与文件夹名称保持一致
* 在状态管理、请求配置中，按照业务模块划分
* 就近原则，多处使用的组件放到 component 文件夹中，此路由紧耦合的子组件，放到本文件夹中
* /src 外的文件不应该被引入

这样的目录结构有什么优势呢？

* 工程目录中的文件夹都有明确的功能，成员之间能很简单快捷的知道某个页面或某个功能块有哪些文件
* 成员之间按照分配的模块开发，避免了代码上的冲突、合并
* 可以根据路由名称，快速定位到文件

好了，有了这样的目录结构，再也不用担心开发时找不到文件了。

### 前端性能优化
酷兜的此次重构，前端大大小小的优化点我们大概总结了 14 条：

* 借鉴 NutUI 组件库中 Icon 图标的开发方式，使用 SVG 图标
* 使用 postcss-plugin-px2rem 插件完成 px 与 rem 的转换，实现移动端的自适应（ dpr 计算）
* 采用按需加载的方式，提升加载速度
* 商品图片展示使用 NutUI 组件的 Lazyload （图片懒加载），减轻服务器的压力
* 采用 RSA 双向加密方式，保证了加密属性的安全性
* ...

看到这几点有没有调起大家的胃口，来张大图满足一下大家！

![](https://img12.360buyimg.com/imagetools/jfs/t1/109646/12/14342/227267/5ea6448bE45bce171/d76e6ff3ab7ec1a7.png)


#### NutUI 2.0 组件库  
![](https://img13.360buyimg.com/imagetools/jfs/t1/113353/4/3054/16562/5ea63a5aE4fa91dda/a1cfd3a29846cf1d.png)  

> 一个聪明的前端工程师，为了能够快速完成版本迭代，除了提高自己的开发效率外，还要懂得会运用工具。选择一款合适的组件库，能大大提升前端的开发效率。    

此次重构，我们依然选用 NutUI 组件库，将 1.x 版本升级到 2.x 。放弃将 NutUI 组件库放到本地的方法，直接使用 NPM 上的最新包，方便实时更新组件。 
 
NutUI 组件库是一套京东风格的轻量级移动端 Vue 组件库。通过 [ JDRD 前端团队 ](https://github.com/jdf2e/nutui) 2 年多的迭代升级，目前有 50+ 京东移动端项目使用，外部使用项目达 30+ 项目。 GitHub 上得到 1.8k 的 star，NPM 下载量超过 12k。能够提供大量的可复用的 Vue 基础组件，极大的缩短重构时间，为之后快速迭代酷兜奠定了基础。   

 **NutUI 2.x 采用全新架构**

与 1.x 版本相比，NutUI 2.x 紧跟时代潮流，基于全新的架构开发：
* 基于 Webpack4.0 开发，拥有更快的构建速度，输出更小的 bundle 文件
* 一次性构建出多种类型的 bundle，兼容各种主流模块化场景和非模块化引用场景
* 基于 Babel7 实现了 Polyfill 的智能加载，无须额外引入 Babel-polyfill 文件也可兼容低版本浏览器
* 集成 Carefree 方案，大幅提升开发环境的真机调试效率
* 示例页面 PWA 加持，支持离线缓存和创建主屏图标
* 接入持续化集成和自动化测试，提升代码可靠性
* 支持自动生成新组件模板
* ...

**高效率** 

现在开源的 UI 组件库琳琅满目，以 MintUI、Vant 为例，在更新速度方面与 NutUI 做一下对比：

![](https://img11.360buyimg.com/imagetools/jfs/t1/117758/40/3282/32597/5ea796f1E5f97b7e1/3376d253193d7fc9.png)

从更新时间和维护团队来说，NutUI 组件库是不是更胜一筹。开发组件时，研发人员都是尽可能让组件适用于任何的业务场景，较高的更新时间可以说明该组件在日常维护中，不用担心在开发过程中，bug 的反馈无人理会，导致失去使用组件库的价值。 

在酷兜重构过程中，购物车商品左滑删除功能，商品列表使用的是 scroller 组件，左滑删除使用的是 leftslip 组件  

![](https://img11.360buyimg.com/imagetools/s375x750_jfs/t1/110782/10/4775/330521/5e9f1f7cE353469a1/18592b7534a56d75.png)

在苹果手机上使用的非常流畅，但是在安卓手机上，左滑商品无法出现删除按钮。原因是 NutUI 库中的 scroller 组件与 leftslip 组件有兼容问题。问题及时反馈以后，这两个组件的开发人员快速响应，及时作出修改。几天之后 NutUI 也发布了新的版本，丝毫没有影响酷兜的开发进度。

**高复用率**

选择一个适合的组件库还有更重要的一点就是组件库的使用率，从酷兜需要的功能组件的使用率来说，NutUI 无疑是最佳的选择。

![](https://img12.360buyimg.com/imagetools/jfs/t1/115833/34/3253/38869/5ea796f1Ebb9395ef/31dd999bbde18943.png)

**支持 TypeScript**

选择 NutUI 组件库最重要的一点是，NutUI 2.x 开始支持 TypeScript 。这对缩短酷兜重构起到了至关重要的一点。

> 🔔 想要了解更多关于 NutUI 2.0 的特性，可以戳这里哟！[http://nutui.jd.com](http://NutUI.jd.com "NutUI 2.0")

#### Gaea 脚手架自动化升级  
[Gaea](https://github.com/jdf2e/Gaea4) 脚手架，是我们团队自主开发的一套 Vue 技术栈构建工具，基于 Node.js、Webpack 模版工程等的 Vue 技术栈的整套解决方案，包含了开发、调试、打包上线完整的工作流程。极大的提高了工作效率。是不是觉得这些话有些空，到底做了哪些升级呢？那就来点干货吧！
* 新增 HappyPack。HappyPack 与 thread-loader 结合，实现多线程编译，加快编译速度，但是需要注意 thread-loader 不可以和 mini-css-extract-plugin 结合使用
* 新增 progress-bar-webpack-plugin 编译进度条。
* 新增 cache-loader。在开发环境编译时，使用模块编译缓存，加快编译速度
* 新增 webpack-bundle-analyzer。能让开发者清晰的看到项目各模块的大小
* 新增 webpack-build-notifier 。webpack 构建完成，能够像咚咚那样，弹出构建结果
* 去掉 uglifyjs-webpack-plugin 。Webpack 版本由 3.x 升级到了 4.x , JS 压缩，webpack4 中内置，不需要单独引入
* ...

更多的特性、优化升级，我们在这里就不赘述了。为了让 Gaea 能更有效的提升开发效率，我们对 Gaea 进行了小小的改动。

开发、测试、上线往往需要不同的环境，我们不能使用线上环境进行修改、操作的。现阶段，在开发、测试阶段，前端通过判断请求地址上是否有 debug 参数，来进行环境的切换。看似小小的举动，费不了多大的事，但是这对于开发、测试来说，已经相当繁琐了。不光 “京东锦礼” 小程序为了配合预发环境，在请求路径上添加 debug 参数，就连前端开发时要时刻注意 debug 参数，后台研发在生成免密登录串时，也需要注意 debug 的存在。真真的牵一发而动全身。就在我们面对这个问题，没有更好的解决办法时， Gaea 脚手架的更新为我们提供了思路。

新版本的 Gaea ，Webpack 版本有 3.x 升级到了 4.x ，修改了 Webpack 配置文件，采用多命令 dev、build、upload 自动化配置请求 API。那我们为何不根据执行自动化配置的命令，来决定使用环境呢？有了新的思路，那就马上行动起来吧！  
```
// config / evn.ts
switch (process.env.NODE_ENV) {
  case 'development':
  case 'upload':
    config.baseUrl = 'https://xxxx-fy.jd.com' // 开发环境 => npm run dev 或 npm run upload
    break
  case 'production':
    config.baseUrl = 'https://xxxx.jd.com' // 线上环境 => npm run build
    break
}
```
通过上面的配置，在结合 API Service 整合，统一配置 HTTP 请求环境，完美的解决了这一问题，开发、测试再也不用担心 debug 参数困扰，或者是搞错线上环境。
#### 封装 AppHelper Hybrid 多端交互类，定制 JS API 接入文档

酷兜当前项目已经支持跨平台多端（微信小程序、第三方 APP 内嵌、H5 ），作为一个可被外接的 H5 ，原生APP API 是必然少不了的，为了良好的用户体验 ，我们需要第三方接入者来严格按照我们的 API 标准开发。
首先我们先思考一下，为什么要有 API ，举一个常见的场景：打开一个新页面，不同端是如何处理

* 微信小程序 内部开发可控
```
wx.miniProgram.navigateTo({
    url: `/pages/xxx?url=` + encodeURIComponent(url)
})
```

* H5 内部开发可控  
```
window.open(url)
```
* 原生 APP ios 或者 android | 第三方 APP 内嵌 开发不可控

这个地方就要详细讲解一下，首先我们的 H5 页面是要被第三方 APP 进行内嵌 WebView 打开，  首先作为前端的我们不知道每个第三方 APP 具体语法调用，那么就需要定制一套 JS API 来约定好调用规则，由前端发起，主动调用 JS 发送至 原生 APP 端，大致思路就是，
1. 先确认当前 WebView 是否支持 kudou API , 通过查看 navigator.userAgent 来确认，navigator.userAgent 的值原生 APP 可以进行自定义设置
2. 区分不同端 android 、ios 分别调用 `callApp.postMessage` 或 `webkit.messageHandlers.callApp.postMessage`
3. postMessage 统一发送约定值 Json 字符串 { name : ' 唯一key，对应不同功能 ' , data : ' 任意参数,根据 key 自定义调整' }
4. 客户端各自收到 json 进行 name 键值匹配，做对应逻辑处理

知道了基本调用，那么我们在想一想，多平台肯定有对应不同的调用方式，那么此处可以简单运用一个工厂模式来处理此逻辑，废话不多说上代码，大家细品一下
1. 创建抽象 APP 类，定制具体功能方法
2. 创建实现类（小程序、原生 APP、H5 ）
3. 创建代工厂类（对外暴露具体方法），初始化时，根据当前场景实例化对应类

![](https://img14.360buyimg.com/imagetools/jfs/t1/112290/20/2282/116958/5ea067e3E7bf26ff6/ceec61ec97614eb4.png)

``` TypeScript
// 枚举值功能key值
enum KudouAppSdkType {
    NewWebView = "newWebView", /** 打开新webview页面 */   
    CloseWebView = "closeWebView", /** 关闭当前webview */   
    OpenLogin = "openLogin", /** 调起登录页 */
    SetTitle = "setTitle" /** 设置webview标题 */
}
class NewWebViewParams {
    constructor(url: string = "", title: string = "") {
        this.url = url;
    }
    url: string = "";
    title: string = "";
}
/** 抽象类 APP 提供具体功能 API */
abstract class App {
    abstract newWebView(data: NewWebViewParams): void; /** 打开新页面 */   
    abstract closeWebView(): void; /** 关闭当前 webview 页面 */   
    abstract openLogin(): void; /** 打开登录页 */   
    abstract setTitle(title: string): void; /** 设置 webview 标题 */
}
/** 方法实现类-小程序 */
class Miniprogram extends App {
    setTitle(title: string): void { }// 微信小程序 自动读取当前 document title
    newWebView(data: NewWebViewParams): void {
        wx.miniProgram.navigateTo({
            url: `/pages/xxx?url=` + encodeURIComponent(data.url)
        })
    }
    closeWebView(): void {
        wx.miniProgram.reLaunch({
            url: `/pages/index/index`
        });
    }
    openLogin(): void {
        wx.miniProgram.redirectTo({
            url: `/pages/login/login?clear=${true}`
        });
    }
    ...
}
/** 方法实现类-原生APP */
class NativeApp extends App {
    executed(name: KudouAppSdkType, data: any = {}) {
        let params = { name, data }
        let str = JSON.stringify(params) // 调用 app 参数输出
        const _window: any = window
        let _userAgent = navigator.userAgent //app userAgent 输出
        if (_userAgent.indexOf('kudou/android') !== -1) { // 调用 android
            try { _window.callApp.postMessage(str) } catch (error) { alert('android error :' + JSON.stringify(error) + 'post android str :' + str) }
        } else if (_userAgent.indexOf('kudou/ios') !== -1) { // 调用 ios
            try { _window.webkit.messageHandlers.callApp.postMessage(str) } catch (error) { alert('ios error :' + JSON.stringify(error) + 'post ios str :' + str) }
        }
    }
    setTitle(title: string): void {
        this.executed(KudouAppSdkType.SetTitle, { title })
    }
    newWebView(data: NewWebViewParams): void {
        this.executed(KudouAppSdkType.NewWebView, data)
    }
    closeWebView(): void {
        this.executed(KudouAppSdkType.CloseWebView)
    }
    openLogin(): void {
        this.executed(KudouAppSdkType.OpenLogin)
    }
    ...
}
/** 方法实现类 - H5 */
class H5 extends App {
    setTitle(title: string): void {
        window.document.title = title;
    }
    newWebView(data: NewWebViewParams): void {
        window.open(data.url)
    }
    closeWebView(): void {
        window.close();
        window.history.back();
    }
    openLogin(): void {
        // code ...
    }
}
/** 代工厂 AppHelper 类 根据不同场景实现对应类 */
export class AppHelper {
    koudApp: App;
    constructor() {
        if (this.isNativeApp) {
            this.koudApp = new NativeApp(); // 原生 APP 场景
        } else if (this.isWeChatMiniprogram) { // 小程序场景
            this.koudApp = new Miniprogram();
        } else {
            this.koudApp = new H5(); // H5 场景
        }
    }
    // 检查是否为原生 APP
    get isNativeApp() {
        return window.navigator.userAgent.indexOf('kudou') !== -1;
    }
    // 检查是否为微信小程序
    get isWeChatMiniprogram() {
        const ua = navigator.userAgent.toLowerCase().match(/MicroMessenger/gi)
        return window.__wxjs_environment === 'miniprogram' || ua && ua[0] === 'micromessenger';
    }
    newWebViewPage(params: NewWebViewParams) {
        if (params.url) {
            this.koudApp.newWebView(params);
        }
    }
    // 设置标题
    setTitle(title: string) {
        this.koudApp.setTitle(title);
    }
    // 令牌失效，调用登录
    loginout() {
        this.koudApp.openLogin()
    }
}
export default {
    install: function (vm) {
        vm.prototype.$appHelper = new AppHelper()
    },
    AppHelper: new AppHelper()
}
```

#### 请求接口 API Service 模块化
酷兜应用程序中，后端是通过判断请求头中携带的 cookie 值是否正确，来返回请求信息的。那也就是说，每一次数据请求，都需要在请求头上添加 cookie 字段，酷兜中的接口请求有 50 多个，如果每个都添加，或者是未来的某一天，后端要前端配合修改请求头。天啊！这简直是场噩梦，没有技术含量不说，还容易出错。   

为了解决这一问题，我们将 HTTP 请求统一配置，生成 HttpClient Class 类，对外暴露 post 、 get 方法。并对后台返回的错误数据进行统一处理，重新定义返回状态码，避免后端状态码多样性，即使后端状态码做了修改，也不影响前端代码的正确运行。

开发人员都是“偷懒”的，能用一行代码解决的问题，坚决不用两行。
![](https://img10.360buyimg.com/imagetools/jfs/t1/116230/30/3104/37120/5ea63483Ec60b8df2/4803185a1c0cc12c.jpg) 


对于 HTTP 请求我们还是不满足，在组件中我们调用 HttpClient Class 类进行数据请求时，我们依然要回到请求接口的模块文件，查看入参，或者是查看 swagger 文档，如何才能一目了然呢？灵光一现，决定采用 Class Params 对象方式约束入参，从编译方式上进行约束。我们以添加购物车请求为例：  
```
// 购物车业务模块 API Service 类
class CartApiService {
    addCart(params: AddCartParams) {
        return this.httpClient.post('/kudou/cart/addToCart', params);
    }
}
// AddCartParams
class AddCartParams {
    num: number = 1; /** 加入商品数量:默认值为1 */
    skuId: string = "" /** 主站商品ID */
}
```
在 addCart 方法中，我们通过 TypeScript 的方式，约束了 '/kudou/cart/addToCart' 接口的参数是一个对象。这样的方式为什么会在调用的时候直接看到参数信息呢？这就需要 VScode 编辑器的配合了。通过模块的方式引入 AddCartParams 类，将鼠标悬停在类上，就可以看到啦！  

![](https://storage.360buyimg.com/imgtools/985b95b920-76cce540-8449-11ea-96a7-35c8ff271808.gif)   

是不是很方便，不但避免了参数类型的不一致，出现 bug ，也节省了查找方法的时间，提高开发效率！

> 注：在 VS code 的编辑器中，当鼠标移动到某些文本之后，稍作片刻就会出现一个悬停提示窗口，这个窗口里会显示跟鼠标下文本相关的信息。如果想要查看对象就具体信息，需要按下 Cmd 键（ Windows 上是 Ctrl ）。

#### JDUntify 埋点模块 
酷兜 2.0 版本中，使用京东自主研发的子午线添加 PV/UV 埋点，便于查看酷兜的流量数据与转化率。在产品提供的埋点列表中，就发现每个点击事件，都有唯一的事件 ID。举个列子：   
![](https://img11.360buyimg.com/imagetools/s375x750_jfs/t1/105210/26/19576/414355/5e9fe072Efb214632/53b13a4c1fb07b8a.png) 
 
首页的分类 tab 有 8 个，根据产品提供的埋点列表，切换不同的分类页签时，需要上报不同的事件 ID 。是不是觉得有不合理之处，如何添加了分类页签，那前端就需要对这个新的标签添加埋点事件 ID 。为什么不能通过传递分类页签的名称或 ID 进行上报呢？经过一番研究，发现子午线暂时不支持这个想法，那我们改如何优化呢？   

我们将所有的埋点事件 ID 通过枚举类的形式，强约束埋点事件 ID 值，避免人为修改造成多少字母导致的问题。封装 JDUnify 类，并注册到 Vue 全局变量中，通过 point('枚举类型')，进行上报。通过这样的调整，首页切换分类页签上报的代码就简答、清爽多了，下面代码示例演示给大家看一下。  

`JDUnify 封装对象示例`
``` TypeScript
declare var MPing: any
export enum PointType {
    event_jinli_Kudou_MyInfo = "event_jinli_Kudou_MyInfo", /** 我的 */
    ...
    event_jinli_Kudou_BacktoTop = "event_jinli_Kudou_BacktoTop" /** 返回顶部 */
}
export class JDUnify {
    // jd 子午线埋点
    point(eventId: PointType | string, enventInfo?: any) {
        try {
            let click = new MPing.inputs.Click(eventId);
            click = Object.assign(click, enventInfo)
            click.updateEventSeries();
            new MPing().send(click);
        } catch (e) { }
    }
}
export default {
    install: function (vm) {
        vm.prototype.$JDUnify = new JDUnify()
    },
    JDUnify: new JDUnify()
}
```

`项目使用示例` 按需设置 枚举属性
```
// 点击"我的"页面埋点
this.$JDUnify.point(PointType.event_jinli_Kudou_MyInfo);
....
// 点击"返回顶部"埋点
this.$JDUnify.point(PointType.event_jinli_Kudou_BacktoTop);
```

只要切换分类标签时，字符串拼接就可以了，就算以后再添加分类页签，只要把事件 ID 放到同一的文件中就行。不仅优化了代码的逻辑、还为之后需求的变更提前做了准备。优秀 ~~

#### TypeScript

从 2018 年 Vue 开始重写到 Vue3.0 源码的公布，前几天 beta 版本的发布，前端的同学一直在期待着 Vue3.0 的问世，我相信这个时间应该不会很长了。为了能让酷兜兼容之后的 Vue 3.0，也为了简化了开发的方式、提高代码的可读性与可维护性，我们在项目重构时添加了 TypeScript。

我们来看一下 2016 年 - 2018 年 ES6 与 TypeScript 的调查表：

![ES6](https://img11.360buyimg.com/imagetools/jfs/t1/106624/36/19271/46970/5e9d2103E58066390/8ae16f4ca0b154d8.png)   

ES6 不同年份调查结果

![TypeScript](https://img11.360buyimg.com/imagetools/jfs/t1/115487/8/1880/55691/5e9d2103E2307999c/f2e9766b9def4ed1.png)

TypeScript 不同年份调查结果

我们不难发现，ES6 的发展很平缓，TypeScript 的使用人数虽然没有 ES6 多，但发展趋势很明显，一直处于上升趋势。2016 年到 2018 年两年的时间，TypeScript 愿意再次使用的用户比例从 20.8％增加到了 46.7％。从 2019-01-01 到 2020-04-19 TypeScript 的下载量已经超过了 Vue 的下载量。 

![TS 下载量](https://img10.360buyimg.com/imagetools/jfs/t1/113149/9/1885/16476/5e9d3a0dE30ed5232/5f8010aeb66ffd52.png)
![Vue 下载量](https://img11.360buyimg.com/imagetools/jfs/t1/86833/19/19516/15273/5e9d39a1Eb1d5a13b/d3747249b7eaf8ea.png)

使用 TypeScript 开发是势在必行的。那使用 TypeScript 能为酷兜重构做出哪些贡献呢！  
**1、减少了注释**   

在实际开发中，阅读、使用其他开发者代码的情况是避免不了的，想要快速掌握代码逻辑、使用规则，清晰的代码注释就显得尤为重要了。在上文的 “请求接口 API Service 模块化” 这个小节，细心的同学可能就注意到这个了。
![查看类型](https://storage.360buyimg.com/imgtools/3ebaa053d8-bf159260-8120-11ea-bda4-45e44354cb16.gif)

结合 VScode 编译工具，我们可以很清楚的了解：baseParams 对象的 key 值有哪些，每个 key 的类型是什么，初始值是什么，对于程序猿来说是不是比任何注释都清楚明白。也节省了在多人开发的过程中，大家沟通、查找对象定义的时间。  

**2、减少 bug**   

当定义好一个函数时，虽然在注释上明确标注了参数的类型，但是多人开发过程中，很多人都不会去注意参数的类型。在 JavaScript 中不同的类型有不同的方法，比如：一串数字的长度，string 类型可以使用 length() 方法，但 number 类型就没有 length() 方法。若定义的方法中对参数使用的 length() 方法，传入的是 number 类型，bug 就诞生了。

不知道大家有没有注意过 toFixed() 这个方法，将 number 类型的数字按照银行家舍入规则保留小数位数，重点来了，得到的值是 string 类型。一不注意这样的类型转换，就会出现 bug，但在 TS 中因为定义了字段的类型，若字段赋值了其他的类型，就会出现错误提示，提前干掉这个 bug。

![](https://img14.360buyimg.com/imagetools/jfs/t1/102561/29/19316/55950/5e9c431cEe5e9e0b4/6e2c38f961258ae6.png)

不光在编写时有错误提示，在 TS 编译时，也会报错提示，虽然 TS 的编译错误并不影响项目的运行，但为保证线上不出现 bug，还是需要仔细看看滴！

![](https://img13.360buyimg.com/imagetools/jfs/t1/99310/26/19169/39502/5e9c4483E23e63f53/21529d59e297d369.png)

> TypeScript 与 Vue 的结合，由于需要为变量、方法、类添加类型，这对于刚刚接触 TypeScript 的前端同学来说，无疑是增加了开发时间。从长远考虑， TypeScript 不仅方便了成员之间的沟通协作，避免因类型转换产生的 bug ，还增强了整个工程的健壮性，为之后的版本迭代打基础。所以我觉得 TypeScript 开发有利有弊，需要根据实际业务情况来决定是否使用，但随着技术的发展，我建议还是使用 TypeScript 进行工程开发。


### 技术拓展
> TypeScript 与 Vue 结合，我们也是第一次使用，为了方面之后的开发人员能够避免这些问题，我们准备了一些技术干货。如何你是前端开发工程师，对这一章节你没准很感兴趣。打起精神，我们继续 ~~

虽然 TypeScript 和 ES6 的语法有很高的相似度，其实这完全是两种不同的东西。ES6 只是 JavaScript 的语言规范，而TypeScript 是 Microsoft 开发和维护的一种面向对象的编程语言，与 JavaScript 是两种脚本语言。只不过 TypeScript 中可以使用 JavaScript 中所有的代码和编码概念。但是不管是用 ES6 语法进行开发，还是使用 TypeScript 进行开发，最终都是需要转换成浏览器识别的 JavaScript 语言。

**1、类型检查**

JavaScript 是弱类型语言，而弱类型语言特点之一就是没有严格的类型定义，定义变量时都是用统一的 var 或 const 关键字，这样虽然不会影响代码的运行，但是运行时隐含的类型转换，也会损耗性能。而 TypeScript 则增加了类型和接口等概念来定义变量的类型，避免浏览器运行时类型转换。

**2、编译过程**

 JavaScript 从另一个角度分类，也可以成为解释型语言，与编译型语言相对。无需编译，只要嵌入 HTML 代码中，就能由浏览器加载解释执行。而 TypeScript，在嵌入 HTML 代码之前，通过编译进行类型注解对静态类型的检查，保证变量类型一致，并转换成 JavaScript 代码，保证浏览器加载解释执行。

#### TypeScript 在 Vue 中的应用

细心的同学在阅读目录结构优化那一小节的时候，可能注意到了，比起常见的 Vue 工程目录文件，此工程目录多了 tsconfig.json 、vue-shim.d.ts 、vue-shim-extend.d.ts 三个文件。   

**1、tsconfig.json**  

  这个文件指定了用来编译这个项目的根文件和编译规则，与 .babelrc 文件的功能类似。在这个文件中可以设置哪些文件需要 TypeScript 编译（ include ），哪些文件不需要（ exclude ）、是否启用装饰等。

**2、vue-shim.d.ts**      

由于 TypeScript 默认并不支持 *.vue 后缀的文件，所以在 Vue 项目中引入的时候需要创建一个 vue-shim.d.ts 文件，放在项目根目录下，使用 TypeScript declare 声明一个模块，告诉 TypeScript 需要处理 *.vue 文件。

```
declare module "*.vue" {
    import Vue from "vue";
    export default Vue;
}
```

这里需要敲一下黑板，在 TypeScript + Vue 框架中，代码中导入 *.vue 文件的时候，需要写上 .vue 后缀。因为 TypeScript 默认只识别 *.ts 文件，不识别 *.vue 文件。

**3、vue-shim-extend.d.ts**  

用于补充在 Vue 中定义的全局变量的接口。在 node_modules 中的 vue/types/vue.d.ts 文件中声明了 Vue 中的接口，在开发过程中，通过 ***.install(vue) 挂载到 Vue.prototype 上的全局变量，在 vue-shim-extend.d.ts 中定义接口，才能在组件中使用。

```
// 在组件中可以直接使用 this.$toast  
declare module 'vue/types/vue' {
    interface Vue { 
        $toast: any
    }
}
```

Vue 与 TypeScript 结合除了目录结构的变化之外，写法上也有很大的不同：

![](https://img14.360buyimg.com/imagetools/jfs/t1/116584/38/2325/244416/5ea13097E64fb6550/3614865e4769e4db.png)

说白了，Vue 与 TypeScript 的结合，其实就是采用 TypeScript 中的装饰器对 Vue 的事件、方法进行了封装，让 Vue 组件语法在结合了 TypeScript 语法之后更加扁平化，容易理解。通过上面的对比，我就可以看出，虽然写法上做了调整，但是依然有 Vue 的影子。只要是了装饰器的工作原理，Vue 结合 TypeScript 开发就不成问题了。
#### 装饰器
> 装饰器是 ES2016 stage-2 的一个草案，但是在 babel 的支持下，已被广泛使用。毕竟是草案，就是说还没有正式发布，TypeScript 官网中装饰器虽然有明确的文档说明，但是也是一项实验性特性。 一下关于装饰器的知识点、结论是以 TypeScript 装饰器为基础哟！  

官网给的定义：装饰器是一种特殊类型的声明，它能够被附加到类声明，方法，访问符，属性或参数上。 装饰器使用 @expression 这种形式，expression 求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。需要注意的两点：   
* 装饰器只能用于类和类的方法，不能用于函数，因为存在函数提升  
* 装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。也就是说，装饰器本质就是编译时执行的函数。

个人理解：装饰器就是在代码外层包了一层处理逻辑，去掉装饰器，代码依然可以正常运行。就好比：我们在水龙头外面的起泡器，安装以后，起泡器会在水里添加很多的泡泡，但说白了起泡器对水龙头是否正常工作一点儿影响都没有，卸掉了起泡器，水龙头照样工作。这里的起泡器就可以看成装饰器。
```
// person.ts
class Person {
    name: string;
    age: number;
    constructor() {
        this.name = 'yugo';
        this.age = 12;
        console.log('年龄：' + this.age);
    }
}
const P = new Person() // 输出 ‘hello Girl！’
```
我们定义了 Person 这个类，正常输出了 ‘hello Girl！’。现在我们我们为 Person 类添加一个装饰器 addAge 。

```
// addAge 装饰器工厂
function addAge(args: number) {
    return function (target: Function) { //真正的装饰器
        target.prototype.age = args;
    };
}
```
```
// person.ts
@addAge(10)
class Person {
    name: string;
    age: number;
    constructor() {
        this.name = 'yugo';
        this.age = 12
        console.log('年龄：'+this.age)
    }
}
const P = new Person() // 输出 年龄：12
```
@addAge 装饰器的作用是为类的 age 属性赋值。运行 person.ts 输出的结果确不是我们期待的结果，为什么 age 的值不是 10 ？原因就是上面提到的，装饰器本质是编译时执行的函数。运行 person.ts 并实例化 Person 对象，属于运行阶段，所以输出的是 12 ，那我们怎样输出 10 呢？

```
// person.ts
@addAge(10)
class Person {
    name: string;
    age: number;
    constructor() {
        this.name = 'yugo';
        // this.age = 12
        console.log('年龄：'+this.age)
    }
}  
const P1 = new Person()  // 输出 年龄：10
```
将 constructor 中对 age 的赋值注释，如果不使用装饰器，应该输出 “ 年龄： undefined ”，但现在输出的是 “ 年龄：10 ”。这也就验证了：装饰器是执行在编译阶段的函数，装饰器只是在代码外层添加了一层逻辑，去掉装饰器，代码依然可以正常运行。

#### 装饰器源码解析 ####   
Vue 官方推荐的是 vue-class-component 装饰器，TypeScript 官网给出的 Vue demo 使用的是 vue-property-decorator 装饰器，虽然 vue-property-decorator 装饰器是对 vue-class-component 装饰器的扩展，但是这两个插件的使用方式还是有区别的，在酷兜重构中，使用的是 vue-property-decorator 装饰器。  

装饰器可以分为 4 类：类装饰器、方法装饰器、属性装饰器、参数装饰器。在这里就不一一赘述每种装饰器的源码了，以属性装饰器 @Prop 为例，来聊聊装饰器是如何将 Vue 与 TypeScript 结合起来的。  

属性装饰器声明在一个属性声明之前（紧靠着属性声明）。属性装饰器表达式会在运行时当做函数被调用，参数 2 个参数：
* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
* 成员的名字   

分别来看一下：Vue 子组件定义父组件传过来的值的方法
```
// TypeScript
@Prop({ type: Boolean, default: false }) readonly value:boolean = false
```
```
// Vue
props:{ value: { type: Boolean, default: false}}
```
其实通过 @Prop 装饰器，最终获得的就是 Vue 定义属性方式。我们不妨在 node_modules 中找到 vue-property-decorator/vue-property-decorator.js，阅读一下 @Prop 的实现方式。其实最关键的就三个函数：

```
// vue-class-component.js 创建装饰器
function createDecorator(factory) {
    return function (target, key, index) { 
      var Ctor = typeof target === 'function' ? target : target.constructor;
      if (!Ctor.__decorators__) {
        Ctor.__decorators__ = [];
      }
      if (typeof index !== 'number') {
        index = undefined;
      }
      Ctor.__decorators__.push(function (options) {
        return factory(options, key, index);
      });
    };
  }
```
```
// vue-property-component.js 添加类属性
function applyMetadata(options, target, key) {
    if (!Array.isArray(options) && typeof options !== 'function' && typeof options.type === 'undefined'){
        var type = Reflect.getMetadata('design:type', target, key);
        if (type !== Object) {
            options.type = type;
        }
    }
}
```   
```
// vue-property-component.js 定义 prop 装饰器
function Prop(options) {
    if (options === void 0) { options = {}; }
    return function (target, key) {   
        applyMetadata(options, target, key);
        createDecorator(function (componentOptions, k) {
            ;
            (componentOptions.props || (componentOptions.props = {}))[k] = options;
        })(target, key);
    };
}

```
编译 TypeScript 文件，执行 @Prop 装饰器，就是在执行 prop 函数。  

**1、applyMetadata(options, target, key) 函数** 

传入 applyMetadata() 函数的参数为 { type: Boolean, default: false }，if 判断 
```
 !Array.isArray(options) , typeof options !== 'function' , typeof options.type === 'undefined' 
```
得到 false 。也就是说，调用 applyMetadata() 函数没有得到任何结果。

    
**2、createDecorator(factory) 函数**

我们把调用 createDecorator() ，改造写一下，方便阅读。

```
function fac = function (componentOptions, k) {
    (componentOptions.props || (componentOptions.props = {}))[k] = options;
}
createDecorator(fac)(target, key)
```
是不是清晰很多。通过阅读 createDecorator() 这个函数你会发现，到最后就是在执行 fac 函数，参数为 target、key。那就简单了，只要读懂   
``` 
(componentOptions.props || (componentOptions.props = {}))[k] = options 
```
这段代码就行了。  这段代码的意思是：为类属性 props 对象添加 k 值，将其简化就是；   

``` 
props: { k : options }
```
对用到例子中，传入参数，得到的结果就是：
``` 
props: { value : { type: Boolean, default: false} } 
``` 
是不是与在 Vue 中定义是一模一样的。说了这多这么多，细心的人可能发现了， @Prop 装饰器其实就是将 TypeScript 的写法，转换成了 Vue 写法。知道了 @Prop 装饰器的实现方法，如果大家感兴趣可以，可以阅读其他装饰器的实现方式，思路与 @Prop 装饰器大同小异。


### 总结

转眼间 2020 年，已经过去三分之一。我们在拼尽全力向前跑的时候，别忘了放慢脚步，回头看看，为之后的爆发积蓄能量。酷兜重构历时 3 个月，但是“漫长”的等待是值得的。2020 年 3 月 31 号顺利上线后，我们收到了很好的用户反馈。

虽然文章马上就要画上句号了，但对于酷兜的重构之路远远没有结束，依然存在很多的挑战。为了优化用户体验、提升开发效率，我们需要每天以饱满的热情迎接早上的朝阳，不断的提升自己的技术水平。让我们与酷兜一起成长吧，也祝愿大客户业务组的业绩蒸蒸日上，加油！！！

PS：如何大家对文章中的某个点感兴趣，欢迎咚咚打扰 ~~ 

![](https://img14.360buyimg.com/imagetools/jfs/t1/112167/12/3172/33801/5ea63a5aE6709fc79/e054981515897810.png) 