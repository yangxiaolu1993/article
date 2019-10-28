### vue 知识点记录

* * *

### .sync修饰符与upload:XXX
在vue开发中组件之间的调用是很平凡的，其中有一个场景：子组件中触发事件，通过this.$emit()c传递到父组件，父组件通过子组件传递过来的参数，进行判断赋值。而.sync修饰符的作用就是将这一步进行简写。实现类似双向绑定的功能。

**参考网址：https://www.jianshu.com/p/d42c508ea9de**

:isShow.sync="isNum"其实就是@update:isShow="bol=>isNum=bol"的语法糖。其中isNum是父组件中需要改变的值，将子组件中传过来的参数，赋值给父组件中的值。

### input[type=number]在移动端的问题

需要注意：- 、 +、  e都是输入Number的范围

#### ios

```
<input type="number"  @input="change" @blur="blur" />
```
在ios中这样的写法是不生效的，输入法弹框依然是全中文。需要添加 **pattern="[0-9]*"**

```
<input type="number"  pattern="[0-9]*" @input="change" @blur="blur" v-enter-number-new/>
```

#### 安卓

安卓手机弹出的数字键盘与ios弹出的数字键盘不同，会有+，-，@等特殊字符，也可以切换到汉字键盘。可以通过 **e.target.value = Infinity** 来控制中文的输入。
如果输入的汉字，或者的特殊字符，**e.target.value 返回的是空**。

```
   numchange(e) {
      let v = e.target.value;

      if (v > this.max) v = this.max;
      if (v < this.minNum) v = this.minNum;
      e.target.value = v;
      this.num = v;
      this.$emit("update:value", this.num);
      this.$emit("change", this.num);
    },
```