最近在开发酷兜接入plus商品项目时，遇到了一些问题，也产生了一些思考。此文章旨在记录这些问题与思考。

#### 技术背景

* 酷兜是使用web-view内嵌在京东锦礼小程序的H5项目

* 酷兜使用的前端技术栈是VUE

#### 为什么要使用内嵌H5

 ##### 避免微信小程序包的大小限制

 微信小程序官方文档给出单个小程序包的大小不能超过2M，而京东锦礼小程序是酷兜、福礼、礼品卡等多个平台的入口，每个平台都有完整的商品展品与购买流程。如果将每个平台都使用小程序原生开发的话，肯定会超过2M的。针对小程序包大小的限制，微信也给出了相应的处理办法：
 
1、将整个小程序划分成不同的子包，在构建时打包成不同的分包，用户在使用时按需加载。同时微信方也给出了分包的大小限制：

* 整个小程序所有分包大小不超过8M
* 单个分包/主包大小不能超过2M

2、使用web-view，将一些需求模块开发成H5，内嵌到小程序中。
   
第一种解决办法依然没有逃离出包的大小限制，而使用内嵌H5就能避免小程序包大小的限制，开发者的代码量可以根据项目的需求优化、添加，只需考虑性能，不用考虑包的大小。每个平台也可以创建自己的独立项目，互不影响，一举两得。
  
##### 节约微信小程序发版审核时间

微信小程序发版重新发版是需要进行微信方的审核的，根据以往提交的经验，审核的时长1小时到2天不定。虽然小程序在今年9月份的时候提供了2个小时的快速审核通道，但必须满足要求的小程序才有资格，并且每年有次数限制。这就造成了版本迭代、紧急问题修改时，都会出现不可控的时间安排。
采用内嵌H5就可以避免这种情况，只需要将修改的内容上传到服务器中，就算上线完成。

#### 内嵌H5的代理与调试

##### 代理到测试服务器（73服务器）

**工具：** 微信开发者工具、whistle

内嵌H5完全可以在chrome浏览器中调试开发，但毕竟是在微信小程序中展示的，我们该如何利用微信开发者工具进行页面调试呢？

web-view内嵌的域名必须是经过ICP备案的，在开发过程中，我们需要实时调试静态资源，不可能每修改一处，就上传到服务器。所以我们需要将线上的静态资源请求代理到本地服务。

1. 启动本地的whistle，并配置需要代理的静态资源文件
 ![图1](https://img11.360buyimg.com/imagetools/jfs/t1/73356/15/14697/63476/5dc3edf9E2a6a8615/c13402e37b3c9217.png)
2. 启动本地项目（酷兜）

3. 配置微信开发者工具

 *     设置->代理设置

     ![图1](https://img14.360buyimg.com/imagetools/jfs/t1/60773/30/14766/20561/5dc3efd9E9e1e1345/a80974c562d4d530.png)

 *     手动设置代理，填写本地的whistle的IP与端口号

    ![图2](https://img13.360buyimg.com/imagetools/jfs/t1/46058/1/15275/17867/5dc3edf9Ead58c24e/a6f1f11d09affc8d.png)

 *     设置完代理后会出现“信任”的弹框，点击“信任”即可

    ![图标](https://img11.360buyimg.com/imagetools/jfs/t1/51057/8/15190/16354/5dc3ef8eEb6fdb74c/ed66e9b08fa4e875.png)

操作到这里我们的代理就配置完成了，现在在微信开发者工具中打开H5页面，就可以在whistle中看到请求了。

![图标](https://img11.360buyimg.com/imagetools/jfs/t1/99581/21/1722/91292/5dc3edf9E0e3c0d25/9665108269dfd896.png)

##### 页面调试
使用whistle配置好代理后，在whistle中就可以实时观察到接口的请求情况。不知道大家有没有注意在微信开发者工具的控制台中，我们没有办法调试web-view组件内的页面与观察接口调用情况：

![图标](https://img14.360buyimg.com/imagetools/jfs/t1/60390/12/14842/203048/5dc50690E73e6509b/40f0f071068c552f.jpg)

除了将web-view嵌套的页面放到浏览器上调试，其实微信开发者工具提供了调试web-view组件的功能：**在 web-view 组件上通过右键 - 调试，打开 web-view 组件的调试** 
 
![图标](https://img13.360buyimg.com/imagetools/s375x600_jfs/t1/60676/22/14865/231987/5dc5068fEcc60f827/677b22bf6dc57e57.png)  

这里需要画一下重点，**需要先左键点击一下web-view组件，右键才能弹出调试**。 
  
![图标](https://img11.360buyimg.com/imagetools/jfs/t1/82992/9/14966/379408/5dc50abaE25b6a3a9/c07c81a8fd2d7111.png)  


#### 客户端缓存

在开发过程中遇到了这样一个问题：将修改的静态资源上传到73服务器后，IOS手机页面没有更新，Android手机更新了。Android手机页面是正常的就说明我们的代码是没有问题的，遇到这样的问题首先想到的就是缓存，通过观察whistle中的数据请求，也证明这个方向是正确的。

![Android数据请求](https://img13.360buyimg.com/imagetools/jfs/t1/74357/2/14849/114760/5dca1416Efacfde09/98c79e0b683dbf8c.png)

                                               Android数据请求

![IOS数据请求](https://img14.360buyimg.com/imagetools/jfs/t1/60109/24/15067/96495/5dca1417Ede98fa00/fbfbf95648c1ed4a.png)

                                                IOS数据请求

再次进入页面，发起请求时，Android会重新请求静态资源文件（css、js），而IOS不会去重新获取。到现在我们就可以百分百的肯定，造成这个问题的原因就是缓存。起初我以为是微信小程序缓存的原因，删除小程序、重新生成体验版、微信退出后台应用，真的是一波操作猛如虎呀，结果并没有什么用，IOS依然抓取不到H5的静态资源。
难道不是小程序的缓存，是微信的缓存？开始的时候，并不肯定，直到我清除了微信APP的缓存才肯定了这个结论！

**清除微信APP缓存的方式：**

**IOS**

在手机微信中找到： 我->设置->通用->存储空间->缓存清理

![](https://img13.360buyimg.com/imagetools/s375x500_jfs/t1/46905/14/15364/120711/5dc53840Ec9360e1f/48c8f7953c0bee48.jpg)

**Android**

1. 通过手机管家，清理缓存

2. 打开https://debugx5.qq.com/ ，选择底部的cookie、DNS缓存、文件缓存，清除。

**微信开发者工具**

微信开发者工具中提供了清缓存的功能：清缓存->全部清理

![](https://img10.360buyimg.com/imagetools/jfs/t1/103941/38/2062/44293/5dca1ce1E09e73a5a/5b08a3113a4c907e.png)

问题是解决了，但是作为一个程序猿就要有刨根问底的精神，我们来深度了解一下客户端缓存机制。


#### 浏览器组成

![](https://img13.360buyimg.com/imagetools/jfs/t1/106921/26/3363/101348/5ddf739fEd54a5d90/c2a1282ec575ac89.png)

* 用户界面（User Interface）：用户界面主要包括工具栏、地址栏、前进/后退按钮、书签菜单、可视化页面加载进度、智能下载处理、首选项、打印等。除了浏览器主窗口显示请求的页面之外，其他显示的部分都属于用户界面。

* 浏览器引擎（Browser Engine）：在用户界面和渲染引擎之间传送指令。

* 渲染引擎（Rendering Engine）：负责显示请求的内容。负责解析代码，并将解析后的内容显示在屏幕上。

* 网络（Networking）：用于网络调用，比如 HTTP 请求。其接口与平台无关，并为所有平台提供底层实现。

* JavaScript 解释器（JavaScript Interpreter）：用于解析和执行 JavaScript 代码。

* 用户界面后端（Display Backend）：用于绘制基本的窗口小部件，比如组合框和窗口。其公开了与平台无关的通用接口，而在底层使用操作系统的用户界面方法。

* 数据存储（Data Persistence）：数据持久层将与浏览会话相关联的各种数据存储在硬盘上。 这些数据可能是诸如：书签、工具栏设置等这样的高级数据，也可能是诸如：Cookie，安全证书、缓存等这样的低级数据。

我们一般称渲染引擎为浏览器内核，其实这个不严谨的，浏览器内核包括渲染引擎和JavaScript 解释器（JS引擎），由于JS引擎越来越独立，浏览器内核就更倾向于渲染引擎。现在主流的渲染引擎主要是：Trident、Gecko、Webkit、Blink（webkit分支内核）。

| 渲染引擎 | JS引擎 | 浏览器PC | 移动端 |
| ----  | :----: | ---- |----|
| webkit | V8 | Safri、Chrome（2013年前） | IOS平台；Android系统中的渲染引擎大部分是基于webkit做的二次开发（QQ-X5内核、UC-U3内核）|
| Gecko  | SpiderMonkey  | Firefox | - |
| Trident  | Chakra |IE | -|
| Blink | Carakan |Opera、Chrome（2013年后）| - |

虽然渲染引擎多种多样，但是他们的渲染引擎的基本工作流程是相同的，可以大致分为4步：

1. HTML解析器解析DOM树（解析为DOM树上个节点，同时解析CSS样式）

2. 渲染树结构（具有一定的视觉效果，并按照一定顺序排列在屏幕上）

3. 布局渲染树（为每个节点分配固定坐标）

4. 绘制DOM树（渲染引擎会遍历所有的节点，由UI后端绘制）

每一种渲染引擎都有自己的资源加载流程与存储规则，这也就造成了相同的代码在不同的浏览器加载时，有的使用缓存数据，有的重新加载。我们以webkit内核为例，说一下webkit加载资源的流程。

#### webkit资源加载

webkit对资源的加载可以分为3类：

* 特定资源加载器：该类资源加载器只加载某一种资源

    * HTML文本   
    * CSS样式文本 - CSSStyleSheetLoader  
    * 字体 - FontLoader
    * 图片 - ImageLoader
    * 只读资源 - RawResourceLoader
    * JavaScript文本 - ScriptLoader

* 缓存机制的资源加载器：CachedResource
    
    特定资源加载器通过共享它来读取和存入缓存资源。

    ![](https://img10.360buyimg.com/imagetools/jfs/t1/89057/9/3025/29432/5ddb7dc9E92eb1c7d/9e2e3ec54268ac9b.png)

* 通用资源加载器：ResourceLoader
    负责从文件系统或网络加载资源来获取资源的数据，同样被所有的特定资源加载器共享。

webkit一开始会通过网络（Networking）获取请求文档的内容，获取了文档内容之后，webkit开始正式工作。Webkit 的加载分为两条线路，一条是主资源（即HTML文本）的加载，另一条是子资源的加载。以加载image图片为例：

![](https://img12.360buyimg.com/imagetools/jfs/t1/56444/11/16465/46179/5ddb88e9Ee154bc3a/6587724cdc77df96.png)


webkit首先加载主资源，通过HTML解析器解析HTML，当遇到<img>标签时，会调用ImageLoader特定的图片资源加载器，加载资源，获取图片信息。首先调用cacheResourceLoader去查看浏览器缓存情况，MemoryCache或DiskCache中有相应的资源并可用，则使用缓存数据，否则通过RequestLoader调用webkit网络模块发起网络请求，返回网络资源，并通过cacheResourceLoader将数据缓存，主要是存储在memory cache（内存）或者disk cache（磁盘）中，方便再次调用。获取图片信息的这个过程是异步的。但是当遇到`<script>`标签时，HTML解析器会立即停止解析，JavaScript解释器执行脚本，直到脚本执行完毕。如果脚本执行中有HTTP请求，也会等到资源全部返回，文档才开始解析。

现在我们知道HTML解析与JS脚本执行是同步的，为了让页面能更快的渲染，数据返回的更快，HTTP请求也有相应的缓存机制。

#### HTTP缓存（也称浏览器缓存）

浏览器与服务器通信的方式为应答模式，即：浏览器发起HTTP请求-服务器响应请求，那么问题就来了，浏览器是怎样确定一个资源该不该缓存，如何去缓存呢？浏览器第一次向服务器发起一个请求后，服务器响应该请求，浏览器拿到响应的结果后，将请求结果和缓存标识存入浏览器缓存，浏览器对于缓存的处理是根据第一次请求资源时返回的响应头来确定的。

![浏览器与服务器第一次请求过程](https://img13.360buyimg.com/imagetools/jfs/t1/85206/23/2809/165146/5dd67be0E4e871ecc/0aa6ef9f2a1c0b48.png)

通过上面这个图，我们可以总结出两点：

* 浏览器每次发起请求时，先在浏览器缓存中查找该请求的缓存结果和缓存标识（读取缓存）
* 浏览器每次拿到返回的请求结果都会将结果和缓存标识存入浏览器缓存中（存入缓存）

看到这里，你可能又会产生问题：浏览器发起请求时，在浏览器缓存中查找到了该请求的缓存结果和缓存标识，之后呢？浏览器是如何判断该不该使用缓存？服务器上的数据更新了，与缓存不一致了，浏览器是如何判断的？那现在只要再理解了浏览器缓存的使用规则，我们就可以搞懂浏览器缓存存入、读取、使用整个过程。

我们现在可以肯定的一点是：浏览器发起请求后，第一步就是先去读取缓存，之后要么是使用缓存的数据，要么就是从服务器重新获取数据。那我们可以根据浏览器是否向服务器重新发起HTTP请求，将缓存过程分为两部分：强缓存和协商缓存。

重点：浏览器对于缓存的处理是根据第一次请求资源时返回的响应头来确定的。

##### 强缓存

强缓存：不向服务器发送请求，直接从浏览器缓存中读取资源。
要想实现强缓存可以通过设置HTTP Header中的Expires和Cache-Control。不知道大家有没有注意过，在chrome控制台的Network选项中可以看到有的请求返回200的状态码，并且Size显示from disk cache或from memory cache。这就是设置了强缓存的结果。

![](https://img10.360buyimg.com/imagetools/jfs/t1/103273/16/3185/18707/5ddd0ff5Ee6dd2f52/1d5d895344236fa6.png)

**Expires**

缓存过期时间，用来指定资源到期的时间，是服务器端返回的具体时间点。也就是说，Expires=max-age + 请求时间，需要和Last-modified结合使用。Expires是Web服务器响应消息头字段，在响应http请求时告诉浏览器在过期时间前浏览器可以直接从浏览器缓存取数据，而无需再次请求。

**Cache-Control**

在HTTP/1.1中，Cache-Control是最重要的规则，主要用于控制网页缓存。比如当Cache-Control:max-age=300时，则代表在这个请求正确返回时间（浏览器也会记录下来）的5分钟内再次加载资源，就会命中强缓存，在缓存中获取数据，不会向服务器发送请求。

Cache-control设置的指令：

* public：所有内容都将被缓存（客户端和代理服务器都可缓存）

* private（默认值）：所有内容只有客户端可以缓存

* no-store：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存

* max-age：max-age=xxx (xxx is numeric)表示缓存内容将在xxx秒后失效

* no-cache：客户端缓存内容，是否使用缓存则需要经过协商缓存来验证决定。

需要特别注意的是no-cache指令，我认为也是理解起来最难的一个,很容易被字面意思所误导。设置了no-cache之后，并不表示浏览器不缓存数据，只是浏览器在使用缓存数据时，需要先确认一下数据是否还跟服务器保持一致，也就是下面要聊到的协商缓存。不使用 Cache-Control的缓存控制方式做前置验证，而是使用 Etag 或者Last-Modified字段来控制缓存。

**Expires和Cache-Control两者对比**

|        | HTTP版本 |  优先级 |
|  ----  | ----  | ---- |
| Expires  | http1.0 | 低 |
| Cache-Control  | http1.1 | 高 |


##### 协商缓存

现在我们知道了，强缓存是根据是否超出某个时间或者某个时间段来决定是否缓存，不关心服务器文件是否已经更新。协商缓存就可以解决这个情况。当强缓存失效后，比如：时间过期、cache-control设置了no-cahce，浏览器就会携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程。可以通过设置HTTP Header中的ETag实现。

**ETag和If-None-Match**

Etag是服务器响应请求时，服务器会给当前资源文件生成一个唯一标识，只要资源文件不发生更新，标识就不会改变，一旦资源更新，标识就会重新生成。浏览器在下一次加载资源向服务器发送请求时，会将上一次返回的Etag值放到request header里的If-None-Match里，服务器只需要比较客户端传来的If-None-Match跟自己服务器上该资源的ETag是否一致，就能很好地判断客户端缓存的数据是否更改过。如果服务器发现ETag匹配不上，那么直接以常规GET 200回包形式将新的资源发给客户端，同时也会将新的Etag数据传给客户端；如果ETag是一致的，则直接返回304知会客户端直接使用本地缓存即可。

 ![](https://img13.360buyimg.com/imagetools/jfs/t1/102496/39/3104/11975/5ddb4734E9f2e408c/d251d6062bf7fa28.png)

协商缓存可以分为两种情况：协商缓存生效（使用缓存）、协商缓存失败（返回新的数据）

 * 协商缓存生效，返回304和Not Modified

![](https://img14.360buyimg.com/imagetools/jfs/t1/44696/11/16398/176064/5ddb40fbEf60e204b/6a32a09c4f20ce29.png)

 * 协商缓存失效，返回200和请求结果

 ![](https://img11.360buyimg.com/imagetools/jfs/t1/71648/29/16120/170210/5ddb40fbE6942e9e7/aececc76018ec2bb.png)


#### 缓存机制（三级缓存原理）

* 先在内存（from memory cache）中查找，如果有,直接加载；

* 如果内存中不存在，则在硬盘（from disk cache）中查找，如果有直接加载；

* 如果硬盘中也没有，那么就进行网络请求；

    进行网络请求时，强缓存是优先于协商缓存的，是先进行强缓存（expires和cache-control）,如果生效则直接使用缓存数据，否则进行协商缓存（Etag/if-none-match），由服务器来决定是否使用缓存数据。

* 请求获取的资源缓存到硬盘和内存。

![](https://img12.360buyimg.com/imagetools/jfs/t1/90806/22/2997/160855/5ddb49f1E1bd2ccaa/322e779601e49ac2.png)


#### 用户行为触发浏览器缓存

我们除了使用代码来影响浏览器缓存，还可以通过操作浏览器来触发浏览器缓存机制。

* 打开网页，地址栏输入地址： 因为是首次打开浏览器TAB，并没有可以使用的memory cache，先去查找 disk cache 中是否有匹配。如有则使用；如没有则发送网络请求。

* 普通刷新 (F5)：因为 浏览器的 TAB 并没有关闭，因此 memory cache 是可用的，会被优先使用。其次才是 disk cache。

* 强制刷新 (Ctrl + F5)：浏览器不使用缓存，因此发送的请求头部均带有 Cache-control: no-cache(为了兼容，还带了 Pragma: no-cache),服务器直接返回 200 和最新内容。

#### 本地存储（cookie）

本地存储主要分为：cookie、localStorage和sessionStorage。

|        | 存储大小 |  时效 | 服务器 |
|  ----  | ----  | ---- | ---- |
| cookie  | 每个浏览器不同，大概4K | 设置的过期时间之前一直有效 | 客户端与服务器端通过cookie传递数据 |
| localStorage  | 永久存储，每个域名推荐5MB | 持久存储，除非主动删除 | 与服务器无关，仅在本地存储|
| sessionStorage  | 没有限制 | 当前浏览器窗口关闭自动删除 |与服务器无关，仅在本地存储 |

localStorage和sessionStorage是前端程序猿很熟悉的两个本地存储，就不聊了，咱们今天来简单聊聊浏览器中的cookie数据与服务器之间是如何沟通的！可以简单概况为：

1.假设当前域名下还是没有 Cookie 值；

2.浏览器发送了一个请求给服务器(这个请求是没带上 Cookie 的)；

3.服务器在响应头的 set-cookie 字段中设置 Cookie 值并发送给浏览器(当然也可以不设置)；

4.浏览器将 Cookie 保存下来；

5.之后的每一次请求，浏览器都会带上这些 Cookie，发送给服务器

光说不练假把式，让我们来看一个简单的Demo，我们现在页面中发送几个请求：

![](https://img10.360buyimg.com/imagetools/jfs/t1/103896/7/3174/41245/5ddceccdEc8aaab87/fb2eda7c9499e942.png)

在chrome控制台中，我们可以通过Cookies项很直观的查看getCartProductNums请求携带的cookie值与响应返回的cookie值。按照我们上面的cookie流程分析，getCartProductNums请求之后的请求的request cookies中应该会有jxi-m-sid字段，但事实并不是这样

![](https://img12.360buyimg.com/imagetools/jfs/t1/60604/28/16222/40697/5ddcf250E2319e2a2/6063b6a09dde3472.png)

queryAddress请求的request cookies中并没有jxi-m-sid字段，这是为什么呢？我们来看一下这些请求的waterfall（瀑布流）

![](https://img14.360buyimg.com/imagetools/jfs/t1/85291/19/3136/25892/5ddcf2d7E56ea75f2/4df45e177f8eeeaa.png)

getCartProductNums请求与queryAddress请求是同时的，queryAddress请求之前，getCartProductNums请求并没有结束，浏览器也没有将jxi-m-sid字段保存下来。queryForTab请求是在请求结束之后进行的，request cookies中就会包括jxi-m-sid字段

![](https://img13.360buyimg.com/imagetools/jfs/t1/85163/19/3135/43876/5ddcf470E72615582/78fed39fcec75629.png)

那接下来，我们在queryForTab请求使用document.cookie在cookie中添加几个字段：

```
document.cookie = `loginName=${escape(param.loginName)};domain=.jd.com;path=/;`;
document.cookie = `enterpriseName=${escape(param.enterpriseName)};domain=.jd.com;path=/;`;
```

![](https://img14.360buyimg.com/imagetools/jfs/t1/105752/5/3145/48571/5ddcf5ecE592177f8/b673846b7f498d28.png)

刷新页面，发起请求时，浏览器也会将请求之前手动存入的cookie值发送到服务端。











当然微信使用缓存中的数据，是有一定的好处的，可以缩短网页请求资源的距离，减少延迟，并且由于缓存文件可以重复利用，还可以减少带宽，降低网络负荷。缓存也算是性能优化中简单高效的一种优化方式了。
