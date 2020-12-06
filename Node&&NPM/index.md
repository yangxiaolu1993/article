### 创建express环境
* 执行express server  
* cd server ->  npm install  ->  npm start ->  localhost:3000

### vue.js 、node.js、webpack、npm 之间的关系

Node 是一个基于 Chrome V8 引擎的 JavaScript 运行环境，让 JavaScript 可以运行在服务端的开发平台。是一个框架

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