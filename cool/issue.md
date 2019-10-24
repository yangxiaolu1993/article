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