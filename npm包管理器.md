## 说明

用于记录在使用 npm 的过程中，遇到了一个些 npm 包

### npm install ora

作用：ora 包用于显示加载中的效果，类似于前端页面的loading效果

方式： 主要用在终端的显示中

使用：

```
const ora = require('ora')
const spinner = ora('Loading unicons').start()

setTimeout(()=>{
    spinner.color = 'yellow';
    spinner.text = 'loading rainbows'
})

```

官方文档：https://www.npmjs.com/package/ora

