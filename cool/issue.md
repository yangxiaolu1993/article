## 主要是记录在工作工程中遇到的问题与解决办法

### RSA加密解密

**技术**：jsencrypt.js
这个前端库有一个特点：公钥加密、私钥解密，没有公钥解密（只能前端公钥加密后台私钥解密）
开发中遇到的问题：后端使用私钥加密，前端需要使用公钥进行解密，返回的结果为false？
解决办法：实现双向加密，就是使用两套秘钥。
后端有两对秘钥：privateKeyA，publicKeyA，privateKeyB，publicKeyB（privateKey：私钥   publicKey：公钥）
后端拿着：privateKeyA，publicKeyB
前端拿着：publicKeyA，privateKeyB

前端加密时用publicKeyA，后端用privateKeyA解密
后端加密时用publicKeyB，前端用privateKeyB解密

这样就能保证，虽然私钥和公钥都在前端代码中，但是这两个并不是一对，就算是全部拿到，也无法成功解密

### ios的微信小程序input框问题
背景：一个页面中有手机号、验证码两个input框。
问题：输入手机号，在手机输入键盘处于弹起状态下，点击手机Home键，让小程序退到后台。在打开微信，此时，页面中的验证码无法点击，点击手机号，输入内容，会与之前的内容保持重合。
机型：目前只在ios8P、ios7P发现这个问题
解决：
这个问题可以拆分成两部分，一是：文字重合  二是：同页面的input框无法点击。第二个问题是小程序自己的问题，暂时没有办法解决。第一个问题的解决方法：

设置input框属性value值，通过bindinput方法，将输入的内容赋值到value值中。


### 内嵌在微信小程序H5页面

**1、sessionStorage**

* 在内嵌的H5中可以使用sessionStorage存储获取相关的信息。
* 当页面跳转、刷新的时候sessionStorage都会存在，当页面关闭的时候，sessionStorage中的内容将被清空。

**2、localStorage**

* 在内嵌H5中可以使用localStorage存储获取相关的信息。
* localStorage中的数值一但创建，除非使用localStorage.removeItem()，清除微信缓存、关闭、跳转均不能清空。

**总结**：小程序中的wx.setStorage()方法进行的缓存，与H5中进行的缓存互不影响，不管是sessionStorage还是localStorage。数据也不能互相使用（微信小程序中无法获取H5中的缓存，H5中也无法获取微信小程序中的缓存）

### 测试微信支付、抓取手机请求

工具：ios、whistle（73服务器）

* ios手机配置代理，手机WiFi手动设置代理，输入服务器（ip）和端口号，IP与端口号的获取在whistle中的online中，
* 只有ios可以抓取微信小程序中的请求，安卓手机微信7.0版本以上不支持
* 可能会出现，代码无法更新的情况，可能是微信自带的缓存，清除微信自带的缓存方法：我的-设置-通用-存储空间-缓存清理

### 微信小程序中路由的理解

背景：微信小程序中存在web-view页面，web-view中的某些功能需要跳转回小程序进行，在跳转回web-view。

微信小程序中的路由可以分为：navigateTo、redirectTo、navigateBack、switchTab这四个。不管是小程序中的跳转，还是web-view中的跳转，只要是在微信小程序中进行的跳转都离不开这4个。

switchTab是Tab切换的跳转，先不做记录，主要是区分：navigateTo、redirectTo、navigateBack。

在微信小程序中跳转，小程序是会记录路由的历史。可以将其想象成一个栈，当小程序页面初始化结束之后，就会将首页放到这个栈中。

注：栈的特点是：先入后出

前提：初始页为A，当A页面加载完时，A路径入栈

**navigateTo**

使用navigateTo，从A->B。B路径入栈，当前栈中按照顺序是A、B。  
总结：跳转路径入栈

**redirectTo**

接下来，我们使用redirectTo，从B->C。B路径出栈，C路径入栈，当前栈中按照顺序A、C。  
总结：当前页面会出栈，跳转页面会入栈。

**navigateBack**

接下来，我们使用navigateTo，从C->B,B->D。因为使用navigateTo跳转，只会入栈，所以当前栈中的按照顺序是：A、C、B、D。现在我们使用navigateBack进行路径跳转，设置delta为2，也就是返回到C。此时，按照D、B的顺序出栈。

总结：从当前页到返回的跳转页出栈。

#### web-view页跳转

如果当前页面是由web-view进行的页面显示，在这里一定要谨记，web-view中的路由记录与小程序中的路由记录一点关系都没有。在进行小程序与web-view进行跳转的时候，只需考虑web-view所在的小程序的页面。

在项目中的实例：

项目：微信小程序中使用web-view内嵌了H5项目。H5项目中有一个订单列表页，未支付的订单有去支付的功能。

需求：点击订单的去支付，跳转到微信小程序进行支付，支付完成后跳转回订单列表页（H5）并刷新。

解决：  

这个功能可以分两步来考虑：
1. 订单列表页（H5）跳转到微信小程序支付，在跳转回订单列表页（H5）； 
2. 跳转回订单列表页时刷新；  

先来解决第一步！

在考虑第一步时，先来梳理一下跳转路径：

打开微信小程序内嵌H5页面 - 我的 - 订单列表页 - 支付（微信） - 订单列表页





