文章结构：
1、vue-cli 脚手架的作用，为什么存在
2、为什么要搭建属于自己的脚手架
3、搭建脚手架的过程


> 单页面应用的快速崛起，使得前端在整个开发过程中所承担的责任越来越重。

前端脚手架一个高大尚的词汇，在我的认知中，这些都是大佬们玩的东西，只能是望而却步。但其实不然，并不是因为困难使我们不敢尝试，而是因为不敢尝试所以才显得困难。俗话说：模仿是有效的学习方式，咱们就参照现有脚手架，一点一点的搭建属于自己的脚手架吧！

## Vue-Cli 脚手架

vue-cli 是小编接触的第一个脚手架，其可以快速初始化 vue 项目，在这个项目中可以直接进行开发。试想一下，如果在不使用 vue-cli 的情况下，初始化一个包含 webpack 打包模板、热重载、eslint 校验的 vue 项目需要多久，所以 vue-cli 可以节省很多的开发时间。当然，同样可以 clone 已经创建好的项目结构，来达到初始化的目的。

其实说白了，脚手架就是一个工具，本质也是从远程下载一个模板来初始化新项目。

### vue-cli 初始化项目

1) 全局安装 vue-cli

2) 初始化项目

## 搭建 xl-rookie-cli

搭建属于自己的 Vue 脚手架，方便在自主练习时，能快速初始化符合自己习惯的 Vue 开发环境。

### 1) 使用 npm init 创建 package.json

在本地的某个路径下创建一个文件夹，在文件夹中使用 npm init 创建 package.json。package.json 设置中有几个关键的字段对创建脚手架很重要。

#### bin

用来指定各个内部命令对应的可执行文件的位置。当全局安装 npm install ** -g 一个 npm 包的时候，npm 包就会执行内部 package.json 中的 bin 命令。比如：安装完 npm install vue-cli -g 后，在项目中可以通过 vue-cli dev 来启动项目。


### 2) 







搭建方式：

commander：https://github.com/tj/commander.js/blob/HEAD/Readme_zh-CN.md

用来编写指令和处理命令行的。 vue init 搭建 vue 环境

inquirer

强大的交互式命令行工具。

ora

好看的加载，下载的时候会有个转圈圈的效果

download-git-repo

下载远程模板，支持 GitHub/GitLab Bitbucket 等


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