搭建属于自己的 Vue 脚手架，用于在自主练习时，能快速搭建一个 Vue 环境。

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

process.argv.slice(2) 就是node的语法

在脚手架内部添加：自动化构建和动态模板