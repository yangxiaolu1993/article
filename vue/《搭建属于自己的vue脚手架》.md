文章结构：
1、vue-cli 脚手架的作用，为什么存在
2、为什么要搭建属于自己的脚手架
3、搭建脚手架的过程


> 单页面应用在发展初期，都是从 npm init 创建一个 package.json 开始的。但是随着单页面应该的快速崛起，前端工程化的成熟，从 0 到 1 的初始化工程化的前端项目所需要的时间也越来越长，因为需要一个工具将整个项目初始化，脚手架孕育而生。

除了通用脚手架工具 yeoman 外，很多项目、公司也会开发自己的脚手架工具，初始化项目基础结构、统一项目规范与约定、新增基础依赖包、提升开发效率，比如：vue-cli、create-react-app。为何不开发一个属于自己的脚手架工具，方便在自主练习时，能快速初始化符合自己习惯的开发环境。

废话不多说，参照 vue-cli 脚手架，一点一点的搭建属于自己的脚手架吧！

## Vue-Cli 脚手架

vue-cli 是小编接触的第一个脚手架，其可以快速初始化 vue 项目，在这个项目中可以直接进行开发。通过 npm install -g vue-cli 全局安装后，便可以在终端输入 vue ，则会提示可 vue 执行的命令。

![vue conmmand](https://img12.360buyimg.com/imagetools/jfs/t1/186411/30/17316/169218/610dec7aEc75a8fb7/746bd401cc2e84bb.png)

不难发现，初始化 vue 项目有三个命令可选：```vue init <template> <app-name>```、 ```vue create <app-name>```、vue ui，其实用的次数多的还是前两个

1)``` vue create <app-name>```

是 Vue-cli3.x 的初始化方法，初始化的模板目前是固定的，可以通过命令行选择进行自由配置。

![vue-cli3](https://img10.360buyimg.com/imagetools/jfs/t1/197787/16/1977/48928/610dfce2E4473ec36/41e8640ad8272682.png)

可以选择使用默认模板初始化 Vue2 或 Vue3 项目

![vue2](https://img10.360buyimg.com/imagetools/jfs/t1/178006/25/18405/375749/610dfce8Ee80e82a0/d15fa4a3da6b1501.png)

小编更偏向于选择 Manually select features (手动选择功能)，在使用默认模板初始化的项目中，没有配置 router、vuex、sass、typescript 经常用到的这些配置。

![vue create select](https://img11.360buyimg.com/imagetools/jfs/t1/196108/4/16939/85992/610dfce2E4c2174ec/67559c5a87713a8e.png)

Manually select features 则可以选择 router、vuex、css、typescript 的配置，让初始化后的项目配置更加完善，符合自己的开发习惯。


2)``` vue init <template> <app-name>```

是 vue-cli2.x 的初始化方式，通过 ```vue init <template> <app-name> ``` 配置两个参数：

* ```<template>``` 初始化项目的模板名
* ```<app-name>``` 初始化项目的名字

其中，vue 官方推荐的标准项目模板是 webpack，当然也可以使用 GitHub 上的一些模板来初始化项目。与 Vue-cli3 初始化不同的是，没有生成默认架构的选项，需要根据命令行的提示配置每一项依赖。

![vue init](https://img11.360buyimg.com/imagetools/jfs/t1/186659/38/17107/88539/610dfce2E99c5b098/4b316c3d4e5a82a8.png)

试想一下，如果在不使用 vue-cli 的情况下，初始化一个包含 webpack 打包模板、热重载、eslint 校验的 vue 项目需要多久，所以 vue-cli 可以节省很多的开发时间。当然，同样可以 clone 已经创建好的项目结构，来达到初始化的目的，就像 vue init 一样从远程下载一个模板来初始化新项目。

## 搭建 xl-rookie-cli

搭建属于自己的 Vue 脚手架，方便在自主练习时，能快速初始化符合自己习惯的 Vue 开发环境。首先得先明确，xl-rookie-cli 需要满足哪些功能

* 全局命令操作。与在终端输入 vue 一样，输入 rookie-cli 同样可以输出 rookie-cli 可执行的命令
* 初始化项目架构。符合自己开发习惯的依赖、文件目录结构等

在初始阶段，先把基础的脚手架功能实现。

### 1) 使用 npm init 创建 package.json

在本地的某个路径下创建一个文件夹，在文件夹中使用 npm init 创建 package.json。package.json 设置中有几个关键的字段对创建脚手架很重要。

#### 【关键】bin 字段

它是一个命令名和本地文件名的映射，用来指定各个内部命令对应的可执行文件的位置。通过 npm install ** 安装时，若是全局安装，那可执行文件会被链接到 prefix/bin 中，若是本地安装，会被链接到当前项目 ./node_modules/.bin 中。比如：vue-cli 源码中

vue-cli

```
"bin": {
    "vue": "bin/vue.js"
},
```

npm install vue -g 全局安装完以后，在终端输入 vue 命令则会出现 vue 相关的命令介绍，其实输入 vue 命令后，执行的便是 "bin/vue.js" 文件中的内容。根据上面的思路，设置 xl-rookie-cli 中的 bin 字段

xl-rookie-cli 

```
"bin": {
    "rookie-cli": "./dist/bin/index.js"
},
```

#### 【重要】name、private、files、version、

这几个字段对开发 xl-rookie-cli 功能没有关系，却是将当前脚手架发布为 NPM 依赖包的关键。只有将 xl-rookie-cli 发布，通过 npm install 全局安装，当然也可以分享给其他人，共同成长进步。 如果没有发布到官网的计划，可以直接跳过。


1) files 字段

当发布 package 包时，具体哪些文件会发布上去。

```
"files": [
    "dist",
    "README.md",
    "package.json",
    "template.json"
],
```

2) private 字段

private 字段对于发布 npm 包很重要。该属性主要是防止手残党执行了 npm publish，发布了不想发布的包，或者发布到不想发布的 npm 私服中。npm init 初始化时，默认是 false，一定要记得修改。

```
"private": true
```

3) version 字段

version 很好理解，版本号。对于业务项目来说，没有强要求。对于 npm 包开发来说，name 和 version 共同决定了唯一一份代码。在执行 npm publish 时，当前的 version 一定是最新的，不能是已经发布的版本，否则会发布失败。

4) name 字段

package 名。对于业务项目来说意义不大，但是对于 npm 包开发来说，很重要，决定了 package 包的名称，也就是对外发布的名称，即 npm install <name> 时的名字。这里要着重说一下，npm publish 之前最好先去 npm 官网上找一下有没有同名的包。最好的测试方式就是，在命令行里面输入 npm install <你要取的名字>，如果报错，那么很好，npm 上没有跟你同名的包，你可以放心大胆地把包发布出去。如果成功下载下来了。。。那么很不幸，只能改名字了...

#### 【关键】main 字段（待定）

main 代表的则是入口文件，main 属性主要使用在引用或者开发某个依赖包的时候需要此属性的支持，不然工程中无法用 import 导入依赖包；
不使用main属性的话我们可能需要这样写引用：require("some-module/dist/app.js")

xl-rookie-cli 包发布之后主要是通过命令名和本地文件名之间的映射来完成，说白了就是 bin 字段控制，main 字段在 xl-rookie-cli 包中是不是可以不设置。


### 2) 编辑 bin 执行文件

bin 执行文件对应的是 package.json 中 bin 字段对应的文件，xl-rookie-cli 目录结构的 ./bin/index.ts。bin 执行文件中有一段很关键的代码，一定要放在文件开头

index.ts

```
#!/usr/bin/env node
console.log('hello xl-rookie-cli')

```
首选需要清楚的一点是，脚手架是在 node 环境下运行的。但每个终端下的 node 的安装路径又是不同的，配置 #!/usr/bin/env node, 就是解决了不同的终端 node 路径不同的问题，可以让系统动态的去查找 node 来执行脚本文件。不妨来试一试，在 xl-rookie-cli 项目下使用 node 执行 index.ts 文件

![](https://img11.360buyimg.com/imagetools/jfs/t1/203838/5/195/16578/610e22aeEa4a0925e/24df0d4a6a89bbbc.png)

成功与 xl-rookie-cli 打了一下招呼。在完善 bin 执行文件之前，先来了解一下下面这些依赖包，如果已经融会贯通可以直接跳过。

* commander
* inquirer
* ora
* download-git-repo

1) commander (https://github.com/tj/commander.js/blob/HEAD/Readme_zh-CN.md)

用来编写指令和处理命令行。

```
const program = require("commander");
program
    .version('1.0.0')
    .usage('<command> [options]')
    .command('delete', 'delete a template') // 设置可执行命令
    .action(()=>{
        // 回调函数
        console.log('start delete template')
    })

// 解析命令行参数
program.parse(process.argv)

```

执行 node ./src/bin/index.ts，则会得到与执行 vue 一样的效果

![](https://img12.360buyimg.com/imagetools/jfs/t1/182650/32/18183/52819/610e26a2Efd9f0cad/8d0a5302f88eb13a.png)

还需要特别说明一下， program.args 用来获取命令传递的参数。比如：执行 vue init webpack demo 时，program.args 会以数组的形式存储 ['webpack','demo']

2) inquirer

强大的交互式命令行工具。

```
const inquirer = require('inquirer');
inquirer
    .prompt([
    // 一些交互式的问题
    ])
    .then(answers => {
    // 回调函数，answers 就是用户输入的内容，是个对象
    });
```

回忆一下，在使用 vue init webpack name 时，是不是有几个交互的问题，需要你决定版本号、文件名、描述、要不要使用 router 等等，它就是来实现这些的。

3) ora

在进行项目开发时，总是需要添加 loading，来提醒用户已经进行了交互操作，只需耐心等待。在命令行交互中，ora 就担任了此任务，它可以提供 loading 的效果。

```
const ora = require('ora')
let spinner = ora('loading ...')
spinner.start()

```

虽然不能自定义样式，但传达的意思是一致的

![](https://img10.360buyimg.com/imagetools/jfs/t1/201689/6/178/17076/610e28caE5282ab6d/9b87733eae2d733e.png)

4) download-git-repo

下载远程模板，支持 GitHub/GitLab Bitbucket 等。

```
const download = require('download-git-repo')
download(repository, destination, options, callback)
```

其中 repository 是远程仓库地址；destination 是存放下载的文件路径，也可以直接写文件名，默认就是当前目录；options 是一些选项，比如 { clone：boolean } 表示用 http download 还是 git clone 的形式下载。

如果你对上面这些依赖包的内容都已经熟悉了，小编相信，实现基础脚手架是非常容易的事情。好了，回到之前的话题，继续编写 bin 执行文件中的内容。在 bin 执行文件中，添加 create 命令，来初始化一个完整的项目。

```
import {ROOT_CLI_PATH} from '../utils/path'
import program from 'commander';
import { create } from '../commands/create'

program
    .command('create <template> <projectname> ')
    .description('初始化项目模板')
    .action(function () {
        create(program)
    })
```
其中，ROOT_CLI_PATH 函数用于定位到项目根目录。

<!-- !()() -->
 
完美输出，但是一直使用 node ./dist/bin/index.js 执行挺麻烦的，将此执行简化，使用 rookie-cli 代替 node ./dist/bin/index.js 执行。 执行 npm link ，node 会访问 package.json ，将 bin 字段设置的执行文件挂载到全局，这样就算是在开发阶段，也可以体验到发布后的效果。

![](https://img14.360buyimg.com/imagetools/jfs/t1/181609/18/18241/89013/610e2d24Ef6d2ebd3/ac2cdb4b8ecb7a7d.png)

接下来就要编写 create 方法了，在 create() 中定义了实现了创建项目的方法。首先先来想一下，

## 结束语
在我的认知中，前端脚手架一个高大尚的词汇，这些都是大佬们玩的东西，只能是望而却步。但其实不然，并不是因为困难使我们不敢尝试，而是因为不敢尝试所以才显得困难。




目录搭建

使用 npm init 创建 package.json

package.json 中的字段介绍

bin：用来指定各个内部命令对应的可执行文件的位置。

当全局安装 npm install ** -g 一个npm包的时候，npm包就会执行内部package.json中的bin。比如：安装完 npm install vue-cli -g 后，在项目中可以通过 vue-cli dev 来启动项目

问题：为什么通过 bin 指定的内部命令可以在项目内使用？？？

在 bin 执行文件中：#!/usr/bin/env node  的作用是让使用 node 进行脚本的解释程序

npm link 命令，可以实现将 文件 映射到全局

process.argv.slice(2) 就是node的语法

在脚手架内部添加：自动化构建和动态模板

自定义搭建的脚手架需要完成：
1、创建项目模板，生成项目基本文件，组织文件结构
2、安装项目所需要的依赖

搭建自定义脚手架的步骤：
1、把脚本映射为命令。在终端输入 vue 有对应的命令显示。使用 commander 
2、下载模板



npm 包发布

1、打开npm官网
2、注册npm账号
3、检索所否有重复的包名
4、将package.json中的name改为发布到npm上的包名
5、执行 npm login
6、登录成功后，在项目下执行 npm publish 发布包


react,vue 脚手架都是用的 lerna 分包，vue-cli源码中用到的依赖包

参考文件：https://segmentfault.com/a/1190000020019921

vue.js 是 vue-cli 的入口文件：

chalk：相等于console的时候加上颜色  
semver:规范版本号  
didyoumean:有点儿类似于纠正输出错误，用户输出错误命令，联想给出正确提示  
slash：用于转换 windows 反斜杠路径转换为正斜杠路径，即 \ 转换为 /  
minimist：解析命令行参数  process.argv  
commander:命令行库  
download-git-repo：直接 git clone 下载代码


执行命令行 vue create <project-name> -options  ,实际执行的是 vue-cli 中的 create.js

create.js 用到的依赖包

inquirer:重要，命令行交互


const loadRemotePreset = require('./util/loadRemotePreset') 这行代码用于加载远程资源

generator.js (主要文件是在这里创建出来的)
await this.resolveFiles() //这里是获取模板文件
await writeFileTree(this.context, this.files, initialFiles) // 真正的写文件file write

vue-cli-service 脚手架创建的vue工程模板文件的存储位置：

vue-cli/packages/@vue/cli-service/generator/template/src