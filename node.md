## 说明

记录在开发过程中，遇到的关于 node 的知识点。

## fs-extra

说明： Node 常用模块。是系统 fs 模块的扩展，提供了更多便利的api，并集成了 fs 模块的 api

安装： npm install fs-extra --save-dev

提供的 API：

**1、copy 复制**

```
fs.copy(sec,dest,[option],callback)
```

实例
```
const fs = require("fs-extra")
fs.copy('/tmp/myfile', '/tmp/mynewfile', function (err) {
   if (err) return console.error(err); 
   console.log("success!")
}) //拷贝文件
fs.copy('/tmp/mydir', '/tmp/mynewdir', function (err) {
   if (err) return console.error(err) 
   console.log('success!')
}) //拷贝目录
```

**2、emptyDir 清空目录**

实例
```
var fs = require('fs-extra')
//假设这个目录下有很多文件和文件夹
fs.emptyDir('/tmp/some/dir', function (err) {
  if (!err) console.log('success!')
})
```



