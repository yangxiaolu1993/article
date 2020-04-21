
##### 标题
 * TS 与 Vue 的完美结合
 * 为酷兜锦上添花 - TS 在 vue 中的实战
 * 

对于开发者来说，bug与新需求都不是最大的难题，在其他人员的代码上进行二次开发可能是最头疼的一件事情。主要是因为不能按照自己的思路、逻辑进行开发，需要先了解他人的思路、逻辑，并在此基础上进行二次开发。对于酷兜来说，2020年是关键的一年，但是酷兜的代码经过多次的版本迭代、功能优化，代码已经变的冗余严重、逻辑不清楚，再加上底层架构版本过低，兼容性不加，用户体验不佳。为了更好的满足2020年酷兜快速迭代更新的需求以及性能要求，提高用户体验。开发人员与产品一致决定进行前端的重构。  

* 项目目录结构
* 使用NutUI组件库
* 技术栈中增加 Typescript 
* 布局使用100%

### 目录结构
一个好的目录结构，能使多人开发有序的进行，也方便管理、扩展。

```
├── .bin                                # Webpack 配置文件
├── build                               # 打包文件
├── node_modules                        # 依赖的模块包（nutui、postcss-plugin-px2rem）
├── package.json                        # 项目基本信息
├── src                                 # 项目的核心组件
│   ├── asset                           # 资源文件（css、image）
│   ├── component                       # 公共组件
│   ├── config                          # 环境配置文件（evn.ts）
│   ├── icons                           # 存放 svg 格式图标
│   ├── services                        # HTTP 请求配置（HttpClient.ts、GoodsApiService.ts）
│   ├── store                           # 状态管理（vuex）
│   ├── view                            # 核心组件
│   ├── util                            # 公共方法(util.ts、imgSet.ts、appHelper.ts)
│   ├── app.vue                         # 根组件
│   ├── app.ts                          # 入口文件
│   ├── router.ts                       # 页面路由
│   ├── index.html                      # 主页模板
│   ├── vue-shim-extend.d.ts            # 扩展 vue 全局类型声明
│   └── vue-shim.d.ts                   # typescript 支持 *.vue 文件配置
├── static                              # 静态资源（ico图标、vendor.dll.js）
├── README.md                           # 项目描述信息（一些方法使用的注意事项）
└── tsconfig.json                       # typescript 编译设置

```
有没有感觉这样的项目结构很清晰（小小的自恋一下），每个文件、文件夹都有明确的功能，并采用模块化的开发方式，在asset、view、store文件中我们均按照页面功能区分了模块文件。  
大家可能注意到了， typescript 重构的项目，比起常用的 vue 应用程序目录结构，多了 tsconfig.json 、vue-shim.d.ts 、vue-shim-extend.d.ts 三个文件。

* tsconfig.json  

    这个文件指定了用来编译这个项目的根文件和编译规则，与 .babelrc 文件的功能类似。在这个文件中可以设置哪些文件需要 typescript 编译（include），哪些文件不需要（exclude）、是否启用装饰等。

* vue-shim.d.ts   

    由于 TypeScript 默认并不支持 *.vue 后缀的文件，所以在 vue 项目中引入的时候需要创建一个 vue-shim.d.ts 文件，放在项目根目录下，使用 typescript declare 声明一个模块，告诉 typescript 需要处理 *.vue 文件。   

    ```
    declare module "*.vue" {
        import Vue from "vue";
        export default Vue;
    }
    ```
    这里需要敲一下黑板，在 typescript + vue 框架中，代码中导入 *.vue 文件的时候，需要写上 .vue 后缀。因为 TypeScript 默认只识别 *.ts 文件，不识别 *.vue 文件
* vue-shim-extend.d.ts  

    用于补充在 vue 中定义的全局变量的接口。在 node_modules 中的 vue/types/vue.d.ts 文件中声明了 vue 中的接口，在开发过程中，通过 ***.install(vue) 挂载到 Vue.prototype 上的全局变量，在 vue-shim-extend.d.ts 中定义接口，才能在组件中使用。

    ```
    // 在组件中可以直接使用 this.$toast

    declare module 'vue/types/vue' {
        interface Vue { 
            $toast: any
        }
    }
    ```

### NutUI组件库
在JQuery横行霸道的时候，UI视觉库称为框架，随着技术的不断更新、发展，使用Vue、React单页面开发越来越流行，逐渐替代了JQuery开发方式。单页面开发是以组件为主，所以UI框架也就改名为UI组件库。  
对于前端开发引入UI组件库，不仅能够减少自己的代码量，提高开发效率，而且组件库经过多次的用户反馈，版本迭代，功能已经很完善，能有效的减少bug与兼容性的出现。但是现在开源的UI组件库琳琅满目，选择一款合适的UI组件库尤为重要。  
酷兜以vue为技术栈的移动端项目。选择一款合适的ui组件成了重构的第一件事。现在流行的开源的移动端UI组件库主要有mint-ui、vant、nutui等，到底哪个组件库更合适酷兜。先来对比一下这几个组件库更新时间。

|        | 团队 |  GitHub最后更新时间 | 简介 | 
|  ----  | ----  | ---- | ---- | 
| mint-ui  | 饿了么团队开发、维护 | 2018年1月16号 | |
| vant | 有赞团队开发，维护 | 2020年4月5号 |  Vant是一个轻量、可靠的移动端 Vue 组件库  |
| nutui | 京东团队开发、维护 | 2020年4月3号 |  一套京东风格的轻量级移动端Vue组件库  |  

任何一个开发人员都不敢肯定自己写的组件可以适用于任何的业务场景，较高的更新时间可以说明该组件在日常维护中，不用担心在开发过程中，组件bug与解答无人理会，导致失去使用组件库的价值。从更新时间和维护团队来说，vant和nutui在这4个组件库中更胜一筹。

|    功能    | mint-ui |  vant |  nutui |
|  ----  | ----  | ---- |  ---- |
| 上拉加载、下拉刷新 | √ |  × | √|
| Dialog对话框  |  √ |  √ | √| 
| Toast 吐司  |  √ |  √  | √|
| 回到顶部  |  × |  × | √|
| 左滑删除  |  × |  √  | √| 
| 上传  |  × |  √ | √|
| Popup 弹出层  |  √ |  √  | √|
| Stepper 步进器  |  × |  √  | √| 
| 图片懒加载  |  √ |  ×  | √| 
| 时间轴  |  × |  √ | √|
| 搜索栏  |  √ |  √ | √|
| 商品价格  |  × |  × |  √|
| 徽标  |  √ |  × |  √|

选择一个适合的组件库还有更重要的一点就是组件库的使用率，从酷兜需要的功能组件的使用率来说，nutui无疑是最佳的选择。

当然选择nutui组件库并不单单是因为更新频率与使用率，还有最重要的一点就是nutui组件库兼容了vue+typescript的开发方式。现在typescript开发逐渐成为主流，而且vue3.0也是使用typescript开发，这就更加肯定了，酷兜使用nutui组件库进行重构。

### Typescript
从2018年vue开始重写到vue3.0源码的公布，大家一直期待vue3.0的正式发布，在这两年的时间里，我们听到最多的信息可能就是vue3.0是使用了typescript进行重构。为了更好的兼容之后的vue版本，也为了增加代码的可读性与可维护性，在酷兜V2.2版本我们加入的了typescript。  
我们来看一下 2016年-2018 年 ES6 与 typescript 的调查表：

![ES6](https://img11.360buyimg.com/imagetools/jfs/t1/106624/36/19271/46970/5e9d2103E58066390/8ae16f4ca0b154d8.png)

ES6 不同年份调查结果

![typescript](https://img11.360buyimg.com/imagetools/jfs/t1/115487/8/1880/55691/5e9d2103E2307999c/f2e9766b9def4ed1.png)

Typescript 不同年份调查结果

我们可以清楚的看到，ES6的发展很平缓，Typescript 的使用人数虽然没有 ES6 多，但发展趋势很明显，一直处于上升趋势。2016年到2018年两年的时间，Typescript 愿意再次使用的用户比例从 20.8％增加到了 46.7％。从 2019-01-01 到 2020-04-19 typescript 的下载量已经超过了 vue 的下载量。 

![TS 下载量](https://img10.360buyimg.com/imagetools/jfs/t1/113149/9/1885/16476/5e9d3a0dE30ed5232/5f8010aeb66ffd52.png)
![vue 下载量](https://img11.360buyimg.com/imagetools/jfs/t1/86833/19/19516/15273/5e9d39a1Eb1d5a13b/d3747249b7eaf8ea.png)

使用 typescript 进行应用程序开发是势在必行的。那使用 typescript 开发应用程序到底有什么好处呢？

刚刚开始接触typescript时，我的第一反应就是，为什么要在项目中加typescript，有啥用，除了增加一个成本，好像没啥用。而且typescript和ES6的语法有很高的相似度，感觉完全没有必要使用typescript，其实不然，ES6 只是 JavaScript 的语言规范，而typescript 是 Microsoft 开发和维护的一种面向对象的编程语言，与JavaScript是两种脚本语言。只不过typescript中可以使用JavaScript中所有的代码和编码概念。但是不管是用ES6语法进行开发，还是使用Typescript进行开发，最终都是需要转换成浏览器识别的JavaScript语言。

* 类型检查  
    JavaScript是弱类型语言，而弱类型语言特点之一就是没有严格的类型定义，定义变量时都是用统一的var或const关键字，这样虽然不会影响代码的运行，但是运行时隐含的类型转换，也会损耗性能。而typescript则增加了类型和接口等概念来定义变量的类型，避免浏览器运行时类型转换。
* 编译过程
    JavaScript从另一个角度分类，也可以成为解释型语言，与编译型语言相对。无需编译，只要嵌入HTML代码中，就能由浏览器加载解释执行。而typescript，在嵌入HTML代码之前，通过编译进行类型注解对静态类型的检查，保证变量类型一致，并转换成JavaScript代码，保证浏览器加载解释执行。
    
说了这么多Typescript开发的好处，那在酷兜重构中typescript对我开发有哪些帮助呢！

#### 1、减少了注释
在实际开发中，阅读、使用其他开发者代码的情况是避免不了的，想要快速掌握代码逻辑、使用规则，清晰的代码注释就显得尤为重要了。  

![查看类型](https://storage.360buyimg.com/imgtools/3ebaa053d8-bf159260-8120-11ea-bda4-45e44354cb16.gif)  

结合 VScode 编译工具，我们可以很清楚的了解：baseParams 对象的 key 值有哪些，每个 key 的类型是什么，初始值是什么，对于程序猿来说是不是比任何注释都好用。也节省了在多人开发的过程中，大家沟通、查找对象定义的时间。

注：在 VS code 的编辑器中，当鼠标移动到某些文本之后，稍作片刻就会出现一个悬停提示窗口，这个窗口里会显示跟鼠标下文本相关的信息。如果想要查看对象就具体信息，需要按下 Cmd 键（Windows 上是 Ctrl）。

#### 2、减少bug
当定义好一个函数时，虽然在注释上明确标注了参数的类型，但是多人开发过程中，很多人都不会去注意参数的类型。在 JavaScript 中不同的类型有不同的方法，比如：一串数字的长度，string 类型可以使用length() 方法，但 number 类型就没有 length() 方法。若定义的方法中对参数使用的 length() 方法，传入的是 number 类型，bug就诞生了。

不知道大家有没有注意过 toFixed() 这个方法，将 number 类型的数字按照银行家舍入规则保留小数位数，重点来了，得到的值是 string 类型。一不注意这样的类型转换，就会出现bug，但在 TS 中因为定义了字段的类型，若字段赋值了其他的类型，就会出现错误提示。

```
const a = 123.4864356
let b = 123
b = b + a.toFixed(1)
console.log(b) //123123.5
```
正如上面的列子，我们期待得到的是246.5，最终得到123123.5，原因就是 a.toFixed(1) 得到的是字符串类型。如果使用 TS 的话，我们就可以提前搞定这个bug。

![](https://img14.360buyimg.com/imagetools/jfs/t1/102561/29/19316/55950/5e9c431cEe5e9e0b4/6e2c38f961258ae6.png)

我们会发现，当鼠标移动到 b 变量的时候，会提示类型赋值错误。不光在编写时有错误提示，在 TS 编译时，也会报错提示，虽然 TS 的编译错误并不影响项目的运行，但不保证不出现bug哟，还是仔细看看滴！

![](https://img13.360buyimg.com/imagetools/jfs/t1/99310/26/19169/39502/5e9c4483E23e63f53/21529d59e297d369.png)

### typescript 在 vue 中的应用

vue 应用程序开发，了解 vue 生命周期是不可或缺的。那结合 Typescript 之后，开发有哪些不同呢？主要是采用 Typescript 中的装饰器对 vue 组件进行了封装，让 Vue 组件语法在结合了 TypeScript 语法之后更加扁平化。vue 官方推荐使用 vue-class-component 装饰器。

|    装饰器    | 装饰器分类 | 与 vue 对比 |  说明 | 用法 |
|     ----    |    ----    |----    |  ---- |  --- |
| @component  | 类装饰器 |components:{} |  声明子组件 |@Component({ components: { Price } }) |
| @Prop       | 属性装饰器     |props:[]      |  接受来自父组件的数据 |@Prop() private msg!：string <br/>注：! 表示确定msg有值 |
| @Watch      | 方法装饰器    |watch:{}      |  监控数据是否改变 | @Watch('show') <br/>onValChange(newVal:string,oldVal:string){}|
| @Emit       | 方法装饰器    |vm.$emit('事件名') |  子组件触发父组件的自定义事件 |@Emit('input') <br/>handleInput(){} |
| @Model      | 属性装饰器    |v-model |  双向绑定 |  @Model ('change', {type: Boolean})  checked!: boolean; |
| Mixins      | 参数装饰器    |mixins: [] |  混入 |  1. @Component({mixins: [myMixins]})；<br/>2. class kudou extends Mixins(myMixins)  |
| get         | 访问器装饰器    |computed:{} |  计算属性 |  get Name(){} |

通过上面的对比，虽然 vue 结合了 Typescript 之后，写法上做了调整，但是依然有 vue 组件的影子，理解起来并不难。只要是了装饰器的工作原理，vue 结合 Typescript 开发就不成问题了。

#### 装饰器

装饰器是 ES2016 stage-2 的一个草案，但是在 babel 的支持下，已被广泛使用。毕竟是草案，就是说还没有正式发布，Typescript 官网中装饰器虽然有明确的文档说明，但是也是一项实验性特性。 一下关于装饰器的知识点、结论是以 Typescript 装饰器为基础哟！  
官网给的定义：装饰器是一种特殊类型的声明，它能够被附加到类声明，方法， 访问符，属性或参数上。 装饰器使用 @expression这种形式，expression求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。需要注意的两点：   

* 装饰器只能用于类和类的方法，不能用于函数，因为存在函数提升  
* 装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。也就是说，装饰器本质就是编译时执行的函数。

个人理解：装饰器就是在代码外层包了一层处理逻辑，去掉装饰器，代码依然可以正常运行。就好比：我们在水龙头外面的起泡器，安装以后，起泡器会在水里添加很多的泡泡，但说白了起泡器对水龙头是否正常工作一点儿影响都没有，卸掉了起泡器，水龙头照样工作。这里的起泡器就可以看成装饰器。

```
// person.ts

class Person {
    name: string;
    age: number;
    constructor() {
        this.name = 'yugo';
        this.age = 12;
        console.log('年龄：' + this.age);
    }
}
const P = new Person() // 输出 ‘hello Girl！’

```
我们定义了 Person 这个类，正常输出了 ‘hello Girl！’。现在我们我们为 Person 类添加一个装饰器 addAge 。

```
// addAge 装饰器工厂

function addAge(args: number) {
    return function (target: Function) { //真正的装饰器
        target.prototype.age = args;
    };
}
```
```
// person.ts

@addAge(10)
class Person {
    name: string;
    age: number;
    constructor() {
        this.name = 'yugo';
        this.age = 12
        console.log('年龄：'+this.age)
    }
}
const P = new Person() // 输出 年龄：12

```
@addAge 装饰器的作用是为类的 age 属性赋值。运行 person.ts 输出的结果确不是我们期待的结果，为什么 age 的值不是10？原因就是上面提到的，装饰器本质是编译时执行的函数。运行 person.ts 并实例化 Person 对象，属于运行阶段，所以输出的是12，那我们怎样输出10呢？

```
// person.ts

@addAge(10)
class Person {
    name: string;
    age: number;
    constructor() {
        this.name = 'yugo';
        // this.age = 12
        console.log('年龄：'+this.age)
    }
}  
const P1 = new Person()  // 输出 年龄：10
```
将 constructor 中对 age 的赋值注释，如果不使用装饰器，应该输出 “ 年龄： undefined ”，但现在输出的是 “ 年龄：10 ”。这也就验证了：装饰器是执行在编译阶段的函数，装饰器只是在代码外层添加了一层逻辑，去掉装饰器，代码依然可以正常运行。

**装饰器在 vue 中的应用**    
vue 官方推荐的是 vue-class-component 装饰器，Typescript 官网给出的 vue demo 使用的是 vue-property-decorator 装饰器，虽然 vue-property-decorator 装饰器是对 vue-class-component 装饰器的扩展，但是这两个插件的使用方式还是有区别的，在酷兜重构中，使用的是 vue-property-decorator 装饰器。  
装饰器可以分为4类：类装饰器、方法装饰器、属性装饰器、参数装饰器。以属性装饰器 @Prop 为例，来聊聊装饰器是如何将 vue 与 Typescript 结合起来的。  
属性装饰器声明在一个属性声明之前（紧靠着属性声明）。属性装饰器表达式会在运行时当做函数被调用，参数2个参数：
* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
* 成员的名字

分别来看一下：vue 子组件定义父组件传过来的值的方法

```
// Typescript

@Prop({ type: Boolean, default: false }) readonly value:boolean = false

```

```
// vue

props:{ value: { type: Boolean, default: false}}
```

其实通过 @Prop 装饰器，最终获得的就是 vue 定义属性方式。我们不妨在 node_modules 中找到 vue-property-decorator/vue-property-decorator.js，阅读一下 @Prop 的实现方式。其实最关键的就三个函数：

```
// vue-class-component.js 创建装饰器

function createDecorator(factory) {
    return function (target, key, index) { 
      var Ctor = typeof target === 'function' ? target : target.constructor;

      if (!Ctor.__decorators__) {
        Ctor.__decorators__ = [];
      }

      if (typeof index !== 'number') {
        index = undefined;
      }

      Ctor.__decorators__.push(function (options) {
        return factory(options, key, index);
      });
    };
  }
```

```
// vue-property-component.js 添加类属性
function applyMetadata(options, target, key) {
    if (!Array.isArray(options) && typeof options !== 'function' && typeof options.type === 'undefined'){
        var type = Reflect.getMetadata('design:type', target, key);
        if (type !== Object) {
            options.type = type;
        }
    }
}
```

```
// vue-property-component.js 定义 prop 装饰器
function Prop(options) {
    if (options === void 0) { options = {}; }
    return function (target, key) {   
        applyMetadata(options, target, key);
        createDecorator(function (componentOptions, k) {
            ;
            (componentOptions.props || (componentOptions.props = {}))[k] = options;
        })(target, key);
    };
}

```

编译 Typescript 文件，执行 @Prop 装饰器，就是在执行 prop 函数。  
* applyMetadata(options, target, key) 函数 
    传入 applyMetadata() 函数的参数为 { type: Boolean, default: false }，if 判断 ``` !Array.isArray(options) , typeof options !== 'function' , typeof options.type === 'undefined' ``` 得到 false 。也就是说，调用 applyMetadata() 函数没有得到任何结果。
* createDecorator(factory) 函数  
     我们把调用 createDecorator() ，改造写一下，方便阅读。

     ```
        function fac = function (componentOptions, k) {
            (componentOptions.props || (componentOptions.props = {}))[k] = options;
        }
        createDecorator(fac)(target, key)
     ```
     是不是清晰很多。通过阅读 createDecorator() 这个函数你会发现，到最后就是在执行 fac 函数，参数为 target、key。那就简单了，只要读懂 
     ``` 
     (componentOptions.props || (componentOptions.props = {}))[k] = options 
     ``` 
     这段代码就行了。  这段代码的意思是：为类属性 props 对象添加 k 值，将其简化就是；  
     ``` 
     props: { k : options }
     ```
     对用到例子中，传入参数，得到的结果就是：
     ``` 
     props: { value : { type: Boolean, default: false} } 
     ``` 
     是不是与在 vue 中定义是一模一样的。说了这多这么多，细心的人可能发现了， @Prop 装饰器其实就是将 Typescript 的写法，转换成了 vue 写法。知道了 @Prop 装饰器的实现方法，如果大家感兴趣可以，可以阅读其他装饰器的实现方式，与 @Prop 是一样的。

### 布局使用100%
说了这么多关于 Typescript 的知识点，换换脑子，咱们来说说页面布局。在酷兜重构中，vue 页面整体布局采用 height:100%、body fixed定位处理。

#### iPhone X 底部适配
