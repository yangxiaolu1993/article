> 随着小游戏的不断发展，Cocos Creator 成了开发者进行游戏开发的宠儿。Cocos Creator 作为一个优秀的游戏引擎框架，同时提供了优秀的编辑器 Cocos Creator ，对图像渲染，UI 系统和动画系统都有良好的 API 进行支持，同时支持构建发布在多个平台。且 Cocos Creator 将整套手机页游解决方案整合在了编辑器工具里，无需在多个软件之间穿梭，只要打开编辑器，各种一键式的自动化流程就能花最少的时间精力，解决代码编辑的许多问题。

### 问题直击 

咱们废话不多说，直接看问题。

Cocos Creator 提供了 Mask 遮罩组件，其用于规定子节点可渲染的范围，带有 Mask 组件的节点会使用该节点的约束框（也就是 属性检查器 中 Node 组件的 Size 规定的范围）创建一个渲染遮罩，该节点的所有子节点都会依据这个遮罩进行裁剪，遮罩范围外的将不会渲染。通过这个组件可以只能将子节点裁剪成多边形或者是椭圆，并不能实现与拼图碎片一样的有序但不规则形状。

那么，在 Cocos Creator 中如何将图片进行自定义裁剪，实现凹凸有秩的拼图碎片形状，先来看一下成图。

![](https://img10.360buyimg.com/imagetools/jfs/t1/173862/38/1750/4738674/6066ecc3E948e0f93/bf9326afd8f95cf6.png)


### 核心思路  
解决此问题的基本思路是：对裁剪功能进行扩展应用，使用 Cocos Creator 提供的 API，绘制 Mask 路径，对基础裁剪形状进行自定义，实现不规则图形的裁剪。

![](https://img11.360buyimg.com/imagetools/jfs/t1/209842/20/1859/108274/614d3743E83b6856c/35930968399d111f.png)

1. 使用 Cocos Creator 创建项目
2. 子元素使用 Mask 组件进行遮罩、裁剪
3. 将 cc.Mask 中的 _graphics 函数原始内容清空，逻辑重写。
4. 使用 lintTo()、bezierCurveTo() 重新绘制裁剪路径


### 详细解析  
每个碎片有4个边，每一边可以分为3个部分：直线、曲线、直线。其中直线部分的长度是一致的，曲线部分又可以分为3中情况：凹、凸、直线，所以拼图碎片是有规律的不规则形状。 

![](https://img12.360buyimg.com/imagetools/jfs/t1/204329/7/8015/37594/614d3762E4c625fc3/5288868d3e2e5c96.png)

1. 使用 Cocos Creator 官网提供的工具、步骤创建项目  
2. 根据官网文档操作，新建场景，在层级管理器新建空节点（clip），并添加 Mask 组件，在该节点下，新建 Sprite 节点，用于放置需要被裁剪的图片（空节点下，可以根据需求放置任意节点，都会被裁剪）。最后将改空节点（clip）设置成预制资源 clipRrefab。   
3. 在该项目资源管理器中新建脚本文件 stage.js ，层级管理器中新建空节点（stage），并在该节点下引入 stage.js 脚本以及 clipRrefab 预制资源。  
4. 在 stage.js 脚本中，调用预制资源，并将 cc.Mask 下的 _graphics 所有路径清除。   

![](https://img13.360buyimg.com/imagetools/jfs/t1/198708/30/9770/44205/614d3eb0Ed15b84fd/8ee14360e4482871.png)

5. graphics 重新绘制拼图碎片裁剪路径，依次按照上、右、下、左的顺利进行。首先设置基础绘制字段：

|  字段   | 默认值  | 说明 | 
|  ----  | ----  | ----  |
| start  | {x:0,y:0} | 裁剪路径的起始点 |
|w|	150|	碎片的宽度|
|h|	150|	碎片的高度|
|r	|25	|碎片单边曲线的距离的一半|
|circleStatus|	[0,0,1,1]|	描述碎片每边曲线的状态：0（直线）1（凸）-1（凹），依次对应上、右、下、左|
|offset|	32|	三阶贝塞尔曲线两个控制点的偏移量，决定曲线的弧度|
|space|	0	|每个碎片之间的空隙（碎片的宽高包括空隙的距离）|

6. 根据基础绘制字段，配合上面拼图碎片示意图，得到以下值：  

    a) 碎片的实际宽度：chip.w = chip.w - chip.space （高度同理）  

    b) 碎片单边（上、下）直线的长度 lineW= chip.w / 2 - chip.r ，若是凸曲线，需要再减去 chip.space，即 lineHN = chip.h / 2 - chip.r + chip.space（高度同理）  
7. 利用 graphics.moveTo(start.x, start.y) 把路径移动到画布指定点，首先绘制‘上边‘，分别绘制碎片‘上边’的3个部分  
    a) 直线  
    利用 graphics.lineTo 绘制直线。起始点为基础绘制字段start 的值，直线终点为
    ```
    graphics.lineTo(start.x + (circleStatus[0] == 1 ? lineWN : lineW), start.y)  
    ```
    b) 曲线  
    若 circleStatus 设置的是 0，即直线，则不用绘制此部分，若是曲线，则利用 graphics.bezierCurveTo 三次贝塞尔曲线绘制。绘制的起始点即 a) 步骤的终点，终点则是：（起始点(start.x) + 碎片的宽度(chip.w)-直线部分的长度（linW/lineWN），start.y）。  
 
    ![](https://img10.360buyimg.com/imagetools/jfs/t1/204589/16/7057/63687/614d3eb0E13e72fd1/7895aa931cf3f669.png)

    其中 CPS1 与 CPS2 为绘制三次贝塞尔曲线需要 2 个控制点，这 2 个控制点需在同一方向。偏移量 chip.offset 决定的曲线的凹凸状态。  

   ![](https://img11.360buyimg.com/imagetools/jfs/t1/209455/21/1901/23555/614d3762E80eadadb/6a802fffc03091a0.png)  

   c)直线  

   依然是利用 graphics.lineTo 绘制直线，单边最后一部分的终点即为（起始点（start.x）+碎片的宽度（chip.w），start.y）  

8. 参照步骤 7 的思路即可完成右、下、左的路径绘制。  
9. 将绘制的路径闭合，绘制、填充。   


