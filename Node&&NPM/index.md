### 创建express环境
* 执行express server  
* cd server ->  npm install  ->  npm start ->  localhost:3000

### vue.js 、node.js、webpack、npm 之间的关系

Node 是一个基于 Chrome V8 引擎的 JavaScript 运行环境，让 JavaScript 可以运行在服务端的开发平台。

Npm 是基于 NodeJs 的包管理工具。使 JavaScript 可以共享于全世界各种模块中。

webpack 是基于 NodeJs 的前端项目部署打包工具。将 .vue 文件转化为浏览器认识的 Html、js、css 语言。

vue 是一个库，而不是框架


说明：NodeJS 是运行环境，npm、webpack 都是基于此环境实现的。执行命令 npm init 初始化创建 package.json 文件，在 package.json 中设置各种的依赖包

npm init 实际是初始化一个包，npm run 运行此包。

npm run 执行的是 package.json 中定义的 scripts 对象。执行的是脚本。

### package.json

执行命令 npm init ，会生成 package.json 文件，用于配置 npm 所依赖包。

属性
{
    name: package 名。对于业务项目来说意义不大，但是对于 npm 包开发来说，很重要，决定了 package 包的名称，也就是对外发布的名称。
    version：package 版本号。对于业务项目来说，比较随意。对于 npm 包开发来说，name 和 version 决定了唯一一份代码。
    description：package 描述。开发 npm 包时很重要，可以简单的向使用者说明包的作用。
    keywords:关键词。npm 包开发很关键，方便使用者快速检索。字符串数组
    homepage: npm 包主页。可以理解为 npm 的 git 地址。
    bugs:{
        url:git 地址
        email：邮箱地址
    }    方便使用者提出使用过程中的问题
    licence:开源协议？
    author：作者
    contributors：参与者
    main:npm 包开发很重要，是代码的入口。当想修改 node_modules 中某个组件库时，首先就要看到这个main，在去修改代码。
    browser:如果客户端使用该包，使用该属性代替main
    bin:用来指定各个内部命令对应的可执行文件的位置
    man:用来指定当前模块的man文档的位置
    files：包含在项目中的文件(夹)数组，可以声明一个.gitignore来忽略部分文件
    repository:对于组件库很重要，告诉使用者在哪里可以找到你的代码，即代码库地址（git）
    scripts:指定运行脚本命令的npm命令缩写。默认值：{start:node server.js}
    config:用于添加命令行的环境变量
    dependencies:生产环境的项目依赖
    devDependencies:开发环境的项目依赖
    peerDependencies:项目的依赖和包组件的依赖是同一个模块，但是不同的版本，此时就会出现问题，为了避免这种情况，设置的版本号会在安装依赖时同时被安装。
    bundledDependencies:定义一个数组，会在发布时将定义的模块一起打包。
    engines:指定项目所依赖的 node 环境、npm版本等。
    engineStrict:
    preferGlobal:
    private:如果设置为 true，npm publish 发布包时会失败。默认值：true
    publishConfig:
}

package 包开发重要的参数：name/version/description/main/scripts/private

业务代码和包代码

### npm 包命令

npm命令集： mac安装路径： /usr/local/lib/node_modules/

npm list -g 查看安装模块全局
npm list -g --depth 0 只查看一级的
npm init 初始化项目


### node.js 、 V8 引擎、JavaScript

JavaScript 引擎是一个专门处理 JavaScript 脚本的虚拟机，一般会附带在网页浏览器之中。

什么是虚拟机啊？
虚拟机，值计算机科学中的体系结构里，是指一种特殊的软件，可以在计算机平台和终端用户之间创建一种环境，而终端用户则是基于这个软件所创建的环境来操作软件。

简单的说，JavaScript 引擎就是能够“读懂”JavaScript 代码，并准确的给出代码运行结果的一段程序。比如： ``` let a = 1+2``` JavaScript 引擎的作用就是解析这段代码，并将 a 的值变为 3。对于静态语言来说（java、c++），处理上述这些事情的叫编译器，相应的对于JavaScript这样的动态语言则叫解释器。

JavaScript 属于解释器还是编译器难以界定。Chrome 浏览器的引擎 V8 

JavaScript 解析引擎与 ECMAScript 之间的关系？
JavaScript 解析引擎是一段程序，而我们写的 JavaScript 代码也是程序，如何让代码解析（读懂）代码呢？这就需要定义规则，也就是 ES5、ES6 定义的规则、标准。

浏览器是网页运行的平台，IE、火狐、Chrome、Safari、Opera 成为 5大浏览器。浏览器内核分为2部分：渲染引擎和JavaScript 引擎。

浏览器内核：Chrome （blink，其实是webkit的分支）、Safari（webkit）、火狐（Gecko），浏览器内核一般指的是渲染引擎
JavaScript 引擎：前端接触最多的就是 V8 引擎。

Chrome V8 引擎，简称 V8 ，是由 谷歌 Chromium 项目团队开发，应用在 Chrome 和基于 chromium 浏览器上的

JavaScript 引擎的执行过程大致是：

源代码 --> 抽象语法树（AST） -->  字节码  -->  JIT （即使编译器）--> 本地代码（V8引擎没有中间字节码）。

V8 引擎在执行 JavaScript的过程中，主要有2个阶段：编译和运行

V8 最初旨在提高 Web 浏览器中 JavaScript 执行的性能。为了提升速度，V8 将 JavaScript 代码转换为 更高效的机器代码，而不是使用解释器。它通过实现 JIT（即时）编译器将JavaScript代码编译成机器代码，就像许多现代JavaScript引擎（如SpiderMonkey或Rhino（Mozilla））所做的那样。与V8的主要区别在于它不会产生字节码或任何中间代码。


Node 是基于 Chrome V8 引擎开发的能够使 JavaScript 在服务器运行的运行时环境。一方面，提供了多种可调用的 API，如读写文件、网络请求、系统信息的等。另一方面，因为CPU执行的是机器码，还负责将JavaScript代码解释成机器指令序列执行，这部分工作由V8引擎完成。

想要真正做到 JavaScript 全栈，路漫漫其修远兮



参考文献：https://blog.csdn.net/acoolgiser/article/details/83313009
        https://blog.csdn.net/weixin_34112181/article/details/88834880

