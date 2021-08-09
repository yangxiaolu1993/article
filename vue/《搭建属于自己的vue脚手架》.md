> 单页面应用在发展初期，都是从 npm init 创建一个 package.json 开始的。但是随着单页面应该的快速崛起，前端工程化的成熟，从 0 到 1 的搭建工程化的前端项目所需要的时间也越来越长，因为需要一个工具智能搭建开发环境，脚手架孕育而生。

除了通用脚手架工具 yeoman 外，很多项目、公司也会开发自己的脚手架工具，搭建项目基础结构、统一项目规范与约定、新增基础依赖包、提升开发效率，比如：vue-cli、create-react-app。那为何不开发一个属于自己的脚手架工具，方便在自主练习时，快速搭建符合自己习惯的开发环境。

废话不多说，参照 vue-cli 脚手架，一点一点的搭建属于自己的脚手架吧！

## vue-cli 脚手架

vue-cli 是小编接触的第一个脚手架，其可以快速搭建 vue 开发环境，减轻了开发者的环境配置工作。通过 npm install -g vue-cli 全局安装后，便可以在终端输入 vue -h，则会提示 vue 可执行的命令。

![vue conmmand](https://img12.360buyimg.com/imagetools/jfs/t1/186411/30/17316/169218/610dec7aEc75a8fb7/746bd401cc2e84bb.png)

不难发现，搭建 vue 环境有三个命令可选：```vue init <template> <app-name>```、 ```vue create <app-name>```、vue ui，其实用的次数多的还是前两个

1)``` vue create <app-name>```

是 Vue-cli3.x 的搭建方法，搭建的模板目前是固定的，但可以通过命令行选择进行自由配置。

![vue-cli3](https://img10.360buyimg.com/imagetools/jfs/t1/197787/16/1977/48928/610dfce2E4473ec36/41e8640ad8272682.png)

可以选择使用默认模板搭建 Vue2 或 Vue3 项目

![vue2](https://img10.360buyimg.com/imagetools/jfs/t1/178006/25/18405/375749/610dfce8Ee80e82a0/d15fa4a3da6b1501.png)

小编更偏向于选择 Manually select features (手动选择功能)，在使用默认模板搭建的项目中，没有配置 router、vuex、sass、typescript 这些依赖，还需手动配置。

![vue create select](https://img11.360buyimg.com/imagetools/jfs/t1/196108/4/16939/85992/610dfce2E4c2174ec/67559c5a87713a8e.png)

Manually select features 则可以选择 router、vuex、css、typescript 的配置，让搭建后的项目配置更加完善，符合自己的开发习惯。


2)``` vue init <template> <app-name>```

是 vue-cli2.x 的搭建方式，通过 ```vue init <template> <app-name> ``` 配置两个参数：

* ```<template>``` 搭建项目的模板名
* ```<app-name>``` 搭建项目的名字

其中，vue 官方推荐的标准项目模板是 webpack，当然也可以使用 GitHub 上的一些模板来搭建项目。与 Vue-cli3 搭建不同的是，没有生成默认架构的选项，需要根据命令行的提示配置每一项依赖。

![vue init](https://img11.360buyimg.com/imagetools/jfs/t1/186659/38/17107/88539/610dfce2E99c5b098/4b316c3d4e5a82a8.png)

试想一下，如果在不使用 vue-cli 的情况下，搭建一个包含 webpack 打包模板、热重载、eslint 校验的 vue 项目需要多久，所以 vue-cli 可以节省很多的开发时间。当然，同样可以 clone 已经创建好的项目结构，来达到搭建的目的，就像 vue init 一样从远程下载一个模板来搭建新项目。

## 搭建 xl-rookie-cli

搭建属于自己的 Vue 脚手架，方便在自主练习时，能快速搭建符合自己习惯的 Vue 开发环境。小编先把 xl-rookie-cli 的目录结构展示给出来

xl-rookie-cli
```
├── dist                                # typescript 编译后的文件
├── package.json                        # 项目基本信息
├── src                                 
│   ├── bin                            
│       └── index.ts                    # bin 执行文件
│   ├── commands                        # 命令对应的处理方法
│       ├── add.ts                      # 添加一个新的模板
│       └── create.ts                   # 新建项目命令
│   └── utils                           # 公共函数
├── template.json                       # 搭建项目模板对应的远程地址
├── README.md                           # 项目描述信息（一些方法使用的注意事项）
└── tsconfig.json                       # TypeScript 编译设置

```
其实项目结构很简单，有没有给到电脑前的你一思读下去的信心。那先来思考一下，一个基础脚手架 xl-rookie-cli 需要满足哪些功能

* 全局命令操作。与在终端输入 ```vue -h``` 一样，输入``` rookie-cli -h ```同样可以输出 rookie-cli 可执行的命令
* 搭建项目架构。符合自己开发习惯的依赖、文件目录结构等

### 1) 使用 npm init 创建 package.json

在本地的某个路径下创建一个文件夹 xl-rookie-cli，在根目录下执行 npm init 创建 package.json。

package.json
```
{
  "name": "xl-rookie-cli",
  "version": "1.3.8",
  "description": "创建属于自己的 Vue 脚手架",
  "private": false,
  "files": [
    "dist",
    "README.md",
    "package.json",
    "template.json"
  ],
  "bin": {
    "rookie-cli": "./dist/bin/index.js"
  },
  "scripts": {
    "dev": "tsc --watch --incremental"
  },
  "dependencies": {
    "chalk": "^2.4.2",
    "commander": "^4.1.1",
    "download-git-repo": "^1.1.0",
    "inquirer": "^7.3.3",
    "ora": "^3.2.0"
  },
  "keywords": [
    "cli"
  ],
  "author": "yangxiaolu",
  "license": "ISC",
  "devDependencies": {
    "@types/node": "^16.4.9"
  }
}

```
其中，package.json 设置中有几个关键的字段对开发脚手架很重要。
#### 【关键】bin 字段

它是一个命令名和本地文件名的映射，用来指定各个内部命令对应的可执行文件的位置。通过 npm install ** 安装时，若是 -g 全局安装，那可执行文件会被链接到 prefix/bin 中，若是本地安装，会被链接到当前项目 ./node_modules/.bin 中。比如：vue-cli 源码中

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

这几个字段对开发 xl-rookie-cli 功能没有关系，却是将当前脚手架发布为 NPM 依赖包的关键。只有将 xl-rookie-cli 发布，才能通过 npm install 全局安装，分享给其他小伙伴，共同成长进步。 

1) files 字段

当发布 package 包时，具体哪些文件会发布上去。

2) private 字段

private 字段对于发布 npm 包很重要。该属性主要是防止手残党执行了 npm publish，发布了不想发布的包，或者发布到不想发布的 npm 私服中。npm init 搭建时，默认是 false，一定要记得修改。

3) version 字段

version 很好理解，版本号。对于业务项目来说，没有强要求。对于 npm 包开发来说，name 和 version 共同决定了唯一一份代码。在执行 npm publish 时，当前的 version 一定是最新的，不能是已经发布的版本，否则会发布失败。

4) name 字段

package 名。对于业务项目来说意义不大，但是对于 npm 包开发来说，很重要，决定了 package 包的名称，也就是对外发布的名称，即 npm install <name> 时的名字。这里要着重说一下，npm publish 之前最好先去 npm 官网上找一下有没有同名的包。最好的测试方式就是，在命令行里面输入 npm install <你要取的名字>，如果报错，那么很好，npm 上没有跟你同名的包，你可以放心大胆地把包发布出去。如果成功下载下来了。。。那么很不幸，只能改名字了...


### 2) 配置 Typescript

顺应时代潮流，Vue3 都使用了 Typescript ，咱们可不能落后，必须搞起来。其实加入 Typescript 只需要两步就能搞定。

1) 在项目根目录新建 tsconfig.json 文件

```
{
    "compilerOptions": {
      "target": "es5",                          
      "module": "commonjs",                     
      "declaration": true,                      
      "sourceMap": true,                        
      "outDir": "./dist",    // 编译后文件的             
      "strict": true,                           
      "moduleResolution": "node",              
      "esModuleInterop": true,                  
      "forceConsistentCasingInFileNames": true, 
      "resolveJsonModule": true
    },
    "include": ["src/**/*"],
    "exclude": [
      "node_modules"
    ]
  }
  
```
其中，outDir 决定编译后的文件放到哪里，include 决定哪些文件需要进行编译，而 exclude 决定哪些文件不需要进行编译。

2) 编译 ts 文件

因为浏览器只认识 js ，不认识 ts ，所以配置好编译规则后，需要把 ts 编译为 js，下面这行代码就可以完美编译

```
 "scripts": {
   "dev": "tsc --watch --incremental"
},
```
执行 npm run dev，就可以把 ts 实时编译为 js ，放到 outDir 配置的路径中。注意，在开发脚手架的过程中，npm run dev 一定要是执行的，毕竟 bin 执行文件对应的路径是编译之后的文件。

### 3) 编辑 bin 执行文件

唠叨了这么多，终于到了 bin 执行文件，bin 执行文件对应的是 package.json 中 bin 字段对应的文件，xl-rookie-cli 中的 ./bin/index.ts。bin 执行文件中有一段很关键的代码，一定要放在文件开头

index.ts

```
#!/usr/bin/env node
console.log('hello xl-rookie-cli')

```
需要清楚的一点是，脚手架是在 node 环境下运行的。但每台电脑下的 node 的安装路径又是不同的，配置 #!/usr/bin/env node, 就是解决不同电脑终端 node 路径不同的问题，可以让系统动态的去查找 node 来执行脚本文件。不妨来试一试，在 xl-rookie-cli 下使用 node 执行编译后 index.ts 文件

```
node ./dist/bin/index.js
```

![](https://img11.360buyimg.com/imagetools/jfs/t1/203838/5/195/16578/610e22aeEa4a0925e/24df0d4a6a89bbbc.png)

成功与 xl-rookie-cli 打了一下招呼。但一直使用 node ./dist/bin/index.js 执行挺麻烦的，将此执行简化，使用 rookie-cli 代替 node ./dist/bin/index.js 执行。 执行 npm link ，node 会访问 package.json ，将 bin 字段设置的执行文件挂载到全局，这样就算是在开发阶段，也可以体验到发布后的效果。

![](https://img14.360buyimg.com/imagetools/jfs/t1/181609/18/18241/89013/610e2d24Ef6d2ebd3/ac2cdb4b8ecb7a7d.png)

编写 bin 执行文件会用到下面这几个依赖包，如果你不是很了解，可以先跳到篇尾【知识回顾】一节。

* commander
* inquirer
* ora
* download-git-repo

如果你对上面这些依赖包的内容都已经熟悉了，小编相信，实现基础脚手架是非常容易的事情，正式进入 bin 执行文件的编写。在 bin 执行文件中，添加 create 命令，来搭建一个项目。

```
import {ROOT_CLI_PATH} from '../utils/path'
import program from 'commander';
import { create } from '../commands/create'

program
    .command('create <template> <projectname> ')
    .description('搭建项目模板')
    .action(function () {
        create(program)
    })
```
其中，ROOT_CLI_PATH 函数用于定位到项目根目录。在 create 函数中，实现了搭建项目，其实思路很简单，就是在 github 中按照自己的开发习惯创建一个搭建项目模板，在 create 函数中，利用 download-git-repo 依赖包，将远程模板下载到当前目录就结束了，是不是 so easy.... 

按照这个思路，模板就可以有任意多个远程路径，在根目录下创建 template.json 文件，用于配置以有模板的路径。

template.json
```
{
    "normal":"https://github.com:yangxiaolu1993/rookie-cli#rookie-cli-template"
}
```
其中，normal 为模板名，"https://github.com:yangxiaolu1993/rookie-cli#rookie-cli-template" 为模板地址，模板地址要注意， https://github.com:yangxiaolu1993/rookie-cli 为 GitHub 的地址，rookie-cli-template 为分支名称。

好了，终于到 create 函数了。

```
const chalk = require('chalk')
const ora = require('ora')
const download = require('download-git-repo')

export async function create(program: {
	args:string[]
}) {
	// 读取根目录下的模板列表
	const tplObj = require(ROOT_CLI_PATH('template'))
	
	let templateName = program.args[0]
	let projectName = program.args[1]
	// 小小校验一下参数
	if (!tplObj[templateName]) {
		console.log(chalk.red('\n Template does not exit! \n '))
		return
	}
	if (!projectName) {
		console.log(chalk.red('\n Project should not be empty! \n '))
		return
	}

	console.log(`use rookie-cli create ${projectName}`)

	let url = tplObj[templateName]
	const spinner = ora("Downloading...");
	spinner.start();

	// 执行下载方法并传入参数
	download(
		url,
		projectName,
		{ clone: true },
		(err: any) => {
			if (err) {
				spinner.fail();
				console.log(chalk.red(`Generation failed. ${err}`))
				return
			}
			// 结束加载图标
			spinner.succeed();
			console.log(chalk.green('\n Generation completed!'))
			console.log('\n To get started')
			console.log(`\n cd ${projectName} \n`)
		}
	)
}
```

首先先校验一下，模板名是否存在与项目名是否为空，接下来就是下载模板了， download-git-repo 默认下载到当前目录下。

好了，执行 ``` rookie-cli create normal demo ```测试一下吧。

![](https://img12.360buyimg.com/imagetools/jfs/t1/196098/25/17665/99866/611099bbEf931aa3b/b60bb7fe0b0c6808.png)

ok，没有报错，而且在根目录下，成功新建了 demo 文件夹。

恭喜你，你已经成功实现了一个基础的脚手架。接下来，就将 xl-rookie-cli 依赖包发布到 GitHub 上吧。

### 4) 发布 GitHub

* 打开 npm 官网、注册 npm 账号
* 检索否有重复的包名，也就是 package.json name 字段设置的名字，如果已经存在了，很不幸只能更名了。小编就属于不幸中的一员，包名由 rookie-cli 改为了 xl-rookie-cli
* 终端执行命令 npm login，登录自己在 npm 官网注册的账号
* 登录成功后，在项目下执行 npm publish 发布包，发布之前一定要注意两点：
   a. version 版本号是不是已经存在了，否则会发布失败 
   b. 镜像一定要是 npm，不能使用淘宝镜像或者公司镜像 

发布完成之后，大概需要等 30s 左右，npm 官网才能更新。到了激动人心的时刻了，作为使用者来验证一下 xl-rookie-cli。在执行``` npm install xl-rookie-cli -g ```之前，需要先卸载开发时已经挂载的 rookie-cli，执行

```
npm unlink 
```
或

```
npm uninstall xl-rookie-cli -g
```
如果执行 ```rookie-cli -V```显示 ```zsh: command not found: rookie-cli```，或执行 ``` npm list -g ``` 显示的全局已安装的包中没有 xl-rookie-cli ，则证明删除成功。接下来，执行

```
npm install xl-rookie-cli -g
```

全局挂载 rookie-cli，执行 ``` rookie-cli -h ``` , 显示

![](https://img13.360buyimg.com/imagetools/jfs/t1/191525/5/17480/56718/6110986dEca6d2809/098149582f140a0c.png)

证明安装成功。到了激动人心的时候了，使用 xl-rookie-cli 搭建项目，执行 ``` roockie-cli  create normal demo```

![](https://img12.360buyimg.com/imagetools/jfs/t1/196098/25/17665/99866/611099bbEf931aa3b/b60bb7fe0b0c6808.png)

成功在当前目录中搭建了项目。在 xl-rookie-cli 中，小编其实还实现了 add 命令，用于添加模板

![](https://img12.360buyimg.com/imagetools/jfs/t1/177231/10/18568/134959/61109cb2Ed7884c2a/10e670ecc43f894d.png)

在这里就不赘述实现 add 命令的方式了，add 命令的实现作为检验学习成果，小编相信只要了解了 create 命令的实现，add 命令的实现电脑前的你肯定没问题。

### 知识回顾

* commander
* inquirer
* ora
* download-git-repo

1) commander (https://github.com/tj/commander.js/blob/HEAD/Readme_zh-CN.md)

用来编写指令和处理命令行，提供的 API：

* command：自定义执行的命令
* option：可选参数
* alias：用于 执行命令的别名
* description：命令描述
* action：执行命令后所执行的方法
* usage：用户使用提示
* parse：解析命令行参数，注意这个方法一定要放到最后调用

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

执行 node ./dist/bin/index.js，则会得到与执行 vue 一样的效果

![](https://img12.360buyimg.com/imagetools/jfs/t1/182650/32/18183/52819/610e26a2Efd9f0cad/8d0a5302f88eb13a.png)

还需要特别说明一下， program.args 用来获取执行命令传递的参数。比如：执行 vue init webpack demo 时，program.args 会以数组的形式存储 ['webpack','demo']

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

回忆一下，在执行 vue init webpack name 时，是不是有几个交互的问题，需要你决定版本号、文件名、描述、要不要使用 router 等等，它就是来实现这些的。

3) ora

在进行项目开发时，总是需要添加 loading，来提醒用户已经进行了交互操作，只需耐心等待。在命令行交互中，ora 就担任了此任务，它可以提供 loading 的效果。

```
const ora = require('ora')
let spinner = ora('loading ...')
spinner.start()

```

虽然不能自定义样式，但传达的意思是一致的。

![](https://img10.360buyimg.com/imagetools/jfs/t1/201689/6/178/17076/610e28caE5282ab6d/9b87733eae2d733e.png)

4) download-git-repo

下载远程模板，支持 GitHub/GitLab Bitbucket 等。

```
const download = require('download-git-repo')
download(repository, destination, options, callback)
```

其中 repository 是远程仓库地址；destination 是存放下载的文件路径，也可以直接写文件名，默认就是当前目录；options 是一些选项，比如 { clone：boolean } 表示用 http download 还是 git clone 的形式下载。

### 代码直通车

* normal 模板地址：[https://github.com:yangxiaolu1993/rookie-cli#rookie-cli-template](https://github.com:yangxiaolu1993/rookie-cli#rookie-cli-template)
* xl-rookie-cli 脚手架：[https://github.com:yangxiaolu1993/rookie-cli](https://github.com:yangxiaolu1993/rookie-cli)

## 结束语 
在我的认知中，前端脚手架一个高大尚的词汇，这些都是大佬们玩的东西，只能是望而却步。经过这次 xl-rookie-cli 开发的过程，我发现之前的一些想法是错的，有些事情并不是因为难使我们不敢尝试，而是因为不敢尝试所以才显得难。当然 xl-rookie-cli 还有很多需要完善的地方，小编也会一直持续更新下去，感兴趣的同学可以一起共建。




