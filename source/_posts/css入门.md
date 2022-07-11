---
title: css入门
category: css
tags:
  - study
  - css
keywords: '前端基础，css，css入门'
abbrlink: 57699
date: 2020-12-20 12:36:47
updated: 2020-12-24 19:27:02
---
# CSS（层叠样式表）

> CSS（层叠样式表）用于设置和布置网页 - 例如，更改内容的字体，颜色，大小和间距，将其拆分为多个列，或添加动画和其他装饰功能。

## css语法

> 它一般由 **选择器** **属性** **值** 三部分构成,如下图

![s](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics/css-declaration-small.png)

## css引入

一共有三种方式引入css样式

1. 内联

2. 页级

3. 外联

```html
<!-- 内联css是通过标签的style属性引入,形式是"属性:值"，以";"为分割 -->
<div style="width:60px;font-size:20;">内联css</div>
```

```html
<!-- 页级css是通过style标签引入,一般放在head里，尽量避免使用，因为这样会导致文件过长难以维护 -->
<!DOCTYPE html>
<html lang="">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
     /* 通过这段代码可以使所有的div变成400*400的猛男色方块 */
     div {
      width: 400px;
      height: 400px;
      background-color: deeppink;
    }
  </style>
</head>

<body>
  <div id="app" style="font-size: 20px;"></div>
</body>
</html>
```

```html
<!-- 外联css是通过link标签或者style标签的@import引入css文件,一般也放在head里 -->
<!DOCTYPE html>
<html lang="">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <!-- link标签引入,推荐着这种方式，可利用浏览器的缓存功能缓存资源 -->
  <link rel="stylesheet" href="/path/to/your/css/file">
  <style>  
    /* @import引入 */
    @import url(path/to/your/file); 
    @import "path/to/your/file";
  </style>
</head>

<body>
  <div id="app" style="font-size: 20px;"></div>
</body>
</html>
```

> 顺带提一嘴路径的问题 通过"."开头则为相对路径,"./a"为当前目录下a文件,"../a"为上一级目录a文件,依次类推, 通过"/"开头则为绝对路径, "/"开始为项目根目录,"/a/b/c"同样顺着往下找

## css属性的简写

* css中有部分属性可以简写,一般多个属性为一种样式服务时就可以简写了,比如font ,background等

```css
background-color:#1890ff;
background-image:url('https://upload-bbs.mihoyo.com/upload/2021/07/22/215885373/5b5179ccb925c854d9e1c9b0299cf52a_7320755774728904130.jpg');
background-repeat:no-repeat;
background-attachment:fixed;
background-position:center;

/* 等同于 */
background:#1890ff url('https://upload-bbs.mihoyo.com/upload/2021/07/22/215885373/5b5179ccb925c854d9e1c9b0299cf52a_7320755774728904130.jpg') no-repeat center;
```

以下列出常用的可简写属性

1. margin (外边距 上下左右)

2. padding (内边距 上下左右)

3. font 设置文字相关(字体 大小 样式等)

4. background (背景图片 颜色 等)

5. border (边框宽度 颜色 种类)

6. outline(轮廊)

7. overflow(大小溢出的控制)

8. flex(flex元素填充规则)

9. transition(过渡)

10. animtaion(css3动画)

> 具体的简写规则与简写了哪些属性比较多，需要自己去查阅与记忆

## css单位

CSS 有几个不同的单位用于表示长度。一些设置 CSS 长度的属性有 width, margin, padding, font-size, border-width, 等。长度有一个数字和单位组成如 10px, 2em, 等。数字与单位之间不能出现空格。如果长度值为 0，则可以省略单位。对于一些 CSS 属性，长度可以是负数。有两种类型的长度单位：相对和绝对。

* 常用的相对单位
  
  * em 取决于当前元素的字体大小。一般浏览器字体大小默认为16px，则2em == 32px；
  
  * rem 取决于根元素字体大小，其他和em一样
  
  * vw 视口宽度 1vw = 浏览器视口的1%宽度
  
  * vh 视口高度 1vh = 浏览器视口的1%高度
  
  * % 百分比

* 常用的绝对单位
  
  * px 像素

## 选择器（重点）

> css通过匹配选择器来确认作用范围

#### 常用选择器

选择器预览：

![img](https://cdn.nlark.com/yuque/0/2020/png/790221/1603512848453-e8b99233-72f2-46f0-8aaa-d56ccce20912.png)

* 类选择器与ID选择器（需掌握）

通过HTML的class值选择

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
    /* 类选择器用.开头 */
    .a {
      width: 300px;
      height: 300px;
    }
    .b{
      background-color:deeppink;
    }
    /* id选择器用#开头 */
    #a{
      opacity:0.5;
    }
</style>
</head>
<body>
     <!-- 单个类 -->
     <div class="a" id="a"></div>
     <!-- 多个类空格隔开 -->
     <div class="a b"></div>
</body>
</html>
```

* 标签与属性选择器（偶尔会用）

如名字所言，依靠HTML标签或属性来选择

```css
/* [属性名] */
[title] {
  color: purple;
}
/* 也可以规定属性的值 */
[title="baidu"] {
    color: red;
}
[class="aba-aba"] {
    color: coral;
}
/* 也可以多个属性链接 */
[title="baidu"][href="https://www.baidu.com/"]
{
    color: red;
}
```

* 后代与子代选择器（需掌握）

后代选择器选中所有内部匹配元素，子代仅匹配第一层

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
    /* 后代选择器 */
    .box li {
        color: coral;            
    }
    /* 子代选择器 */
    .box>li {
        color: deeppink;            
    }
  </style>
</head>
<body>
    <div class="box">
        <li>阿巴</li>
        <p>大师我悟了</p>
        <li>阿巴</li>
        <li>阿巴</li>
        <div>
            <li>啊这</li>
            <li>啊这</li>
            <li>啊这</li>
        </div>
    </div>
</body>
</html>
```

* 兄弟选择器

选择当前相邻第一个元素

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
      /* 与p标签下面的第一个li标签 */
      p + li {
          color: coral;
      }
  </style>
</head>
<body>
  <p>大师我悟了</p>
  <li>阿巴</li>
</body>
</html>
```

* 伪类选择器（需要掌握）

* ...(太多了😭😭😭)

#### 选择器权重（需要了解）

css里并不一定是后来者居上的，选择器是有一定权重的。当我们通过不同的选择器，选中相同的元素，并且为相同的样式设置不同的值时，此时就发生了样式的冲突。

内联样式 1,0,0,0 
id选择器 0,1,0,0 
类和伪类选择器 0,0,1,0 
元素(标签)选择器 0,0,0,1 
通配选择器 0,0,0,0 
 继承的样式 没有优先级 

> 比较优先级时，需要将所有的选择器的优先级进行相加计算，最后优先级越高，则越优先显示（分组选择器是单独计算的）,选择器的累加不会超过其最大的数量级，类选择器在高也不会超过id选择器如果优先级计算后相同，此时则优先使用靠下的样式 可以在某一个样式的后边添加 !important ，则此时该样式会获取到最高的优先级

* 一般为 !important > 行内样式 > ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性

* 在这里查看所有的选择器种类

[CSS 选择器 - 学习 Web 开发 | MDN](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Selectors#%E9%80%89%E6%8B%A9%E5%99%A8%E5%8F%82%E8%80%83%E8%A1%A8)

## 层叠与继承(了解一下就行)

- 层叠

当应用两条同级别的规则到一个元素的时候，写在后面的规则生效。

```css
h1 { 
    color: red; 
}
h1 { 
    color: blue; 
}
```

```html
<h1>This is my heading.</h1>
```

你将看到蓝色的字而不是红色的

- 继承

> 有一部分css属性是可继承的,作用在父元素上的同时也会在子元素上生效。CSS 为控制继承提供了四个特殊的通用属性值。每个 css 属性都接收这些值。

1. inherit (继承父元素)

2. initial (完全默认样式,可以理解为啥都没有)

3. unset (如果可继承则等于inherit，否则为initial，也就是继承优先，否则为initial)

4. revert(恢复到浏览器的默认样式)

###### 下面列出继承相关的属性

**常用的css不可继承的属性**

* display：规定元素应该生成的框的类型

* text-decoration：规定添加到文本的装饰

* text-shadow：文本阴影效果

* white-space：空白符的处理

* 盒子模型的属性：width、height、margin 、border、padding

* 背景属性：background

* 定位属性：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index

**常用的css可继承的属性：**

* font：组合字体

* font-family：规定元素的字体系列

* font-weight：设置字体的粗细

* font-size：设置字体的尺寸

* font-style：定义字体的风格

* text-indent：文本缩进

* text-align：文本水平对齐

* line-height：行高

* color：文本颜色

* visibility：元素可见性

* 光标属性：cursor

**所有元素可以继承的**

* 1、元素可见性：visibility

* 2、光标属性：cursor

**内联元素可以继承的属性**

* 1、字体系列属性

* 2、除text-indent、text-align之外的文本系列属性

**块级元素可以继承的属性**

* text-indent、text-align

举个例子:

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
    .inherit {
      width: 300px;
      height: 300px;
    }
    .initial {
      color: deeppink;
    }
    .unset {
      font-weight: bold;
    }
    .unset div {
      font-weight: unset;
    }
    .h3 {
      font-weight: normal;
      color: blue;
      border-bottom: 1px solid grey;
    }
</style>
</head>
<body>
  <!-- inherit -->
  <div class="inherit">
    <div style="background-color: deeppink;width: inherit;height: inherit;">
      我是继承来的宽高
    </div>
  </div>
  <div class="inherit">
    <div>
      我啥也没有
    </div>
  </div>
<!-- initial -->
  <div class="initial">
    <div>猛男色字体</div>
    <div style="color:initial;">黑色字体</div>
  </div>
<!-- unset -->
  <h3 style="font-weight: unset;">正常字体</h3>
  <div class="unset">
    <div>粗体</div>
  </div>
<!-- revert -->
  <h3 class="h3" style="font-weight: revert; color: revert;">
    黑色粗体
  </h3>
  <h3 class="h3" style="font-weight: unset; color: initial;">
    正常黑色字体
  </h3>
</body>
</html>
```

## 盒模型

在css里一切皆盒子,通过box-sizing属性来改变盒子大小计算方式，一个盒子占据的空间同城和这些有关

* width height 宽高

* padding 内边距

* margin 外边距

* border 边宽

![box](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model/boxmodel-(3).png)

这里介绍常用的两种盒模型

* content-box
  
         padding + border + 自身宽高 = 所占空间

* border-box
  
       自身宽高 = 所占空间 (故内部空间需要减去padding和border)

```css
div{
    box-sizing:content-box; /* 默认的标准盒模型 */
}
div{
    box-sizing:border-box;  /* ie盒模型 */
}
```

## 文档流（需要了解）

- 网页是一个多层的结构，一层摞着一层
  
  * 通过CSS可以分别为每一层来设置样式
  
  * 作为用户来讲只能看到最顶上一层
  
  * 这些层中，最底下的一层称为文档流，文档流是网页的基础
  
  * 我们所创建的元素默认都是在文档流中进行排列
  
  * 对于我们来元素主要有两个状态
    
    * 在文档流中
    
    * 脱离文档流
  
  * 元素在文档流中有什么特点：
    
    * 块元素
      
      1. 块元素会在页面中独占一行(自上向下垂直排列)
      
      2. 默认宽度是父元素的全部（会把父元素撑满）
      
      3. 默认高度是被内容撑开（子元素）
    
    * 行内元素
      
      1. 行内元素不会独占页面的一行，只占自身的大小
      
      2. 行内元素在页面中左向右水平排列，如果一行之中不能容纳下所有的行内元素
      
      3. 则元素会换到第二行继续自左向右排列（书写习惯一致
      
      4. 行内元素的默认宽度和高度都是被内容撑开

     

## 布局与定位(重点)

#### position

- 定位是一种更加高级的布局手段,通过定位可以将元素摆放到页面的任意位置,使用position属性来设置定位

- position: static | relative | absolute | fixed | sticky 
  
  * 相对定位：
    
    * 当元素的position属性值设置为relative时则开启了元素的相对定位
    
    * 相对定位的特点:
      
      1. 元素开启相对定位以后，如果不设置偏移量元素不会发生任何的变化
      
      2. 相对定位是参照于元素在文档流中的位置进行定位的
      
      3. 相对定位会提升元素的层级
      
      4. 相对定位不会使元素脱离文档流
      
      5. 相对定位不会改变元素的性质块还是块，行内还是行内
  
  * 偏移量（offset）
    
    * 当元素开启了定位以后，可以通过偏移量来设置元素的位置
      
      1. top 距离定位参考位置上边的距离
      
      2. bottom 距离定位参考位置下边的距离
      
      3. left 距离定位参考位置左边的距离
      
      4. right 距离定位参考位置右边的距离

> 这里介绍常用的三种 relative absolute fixed

###### relative

* 相对定位，相对于其正常位置进行定位

没什么特别的,就基于原本正常位置偏移

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    div {
      width: 300px;
      height: 300px;
      position: relative;
      top: 50px;
      left: 50px;
      background-color: deeppink;
    }
  </style>
</head>

<body>
  <!-- 你会看到一个向右下移动50px的猛男色方块 -->
  <div></div>
</body>

</html>
```

###### absolute

*  绝对定位，相对于 static 定位以外的第一个父元素进行定位，若没有则相对于html

特点:

    1.开启绝对定位后，如果不设置偏移量元素的位置不会发生变化

    2.开启绝对定位后，元素会从文档流中脱离

    3.绝对定位会改变元素的性质，行内变成块，块的宽高被内容撑开

    4.绝对定位会使元素提升一个层级

    5.绝对定位元素是相对于其包含块进行定位的

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .div {
      width: 300px;
      height: 300px;
      position: relative;
      top: 50px;
      left: 50px;
      background-color: deeppink;
    }

    .div div {
      width: 30px;
      height: 30px;
      position: absolute;
      right: 0;
      bottom: 0;
      background-color: aquamarine;
    }
  </style>
</head>

<body>
  <!-- 在猛男色方块里的右下可以看到一个绿色方块 -->
  <div class="div">
    <div></div>
  </div>
</body>

</html>
```

###### fixed

* 固定定位，相对于浏览器窗口进行定位

特点: 仅靠浏览器视口定位，不会因为滚动条的滚动而改变位置

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .div {
      width: 100%;
      height: 150px;
      background-color: aquamarine;
      position: fixed;
    }

    .div2 {
      height: 500px;
      background-color: bisque;
      width: 200px;
    }

    .div3 {
      height: 500px;
      background-color: deeppink;
      width: 200px;
    }
  </style>
</head>

<body>
  <!-- 移动滚动条,绿色的盒子始终固定在头部 -->
  <div class="div"></div>
  <div class="div2"></div>
  <div class="div3"></div>

</body>

</html>
```

#### flex(!important)

> flex弹性盒子是一种用于按行或按列布局元素的一维布局方法。元素可以膨胀以填充额外的空间，收缩以适应更小的空间。

当父元素开启弹性布局(`display:flex`)时，它的子元素们沿着两个轴来布局：cross axis(交叉轴) 和 main axis(主轴)

![img](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox/flex_terms.png)

###### flex常用属性

* `flex-direction` 设置主轴方向(默认为水平) 可选值 row | row-reverse（水平反向） | column | column-reverse(竖直反向)

* `flex-wrap` 设置是否换行 可选值 wrap | nowrap | wrap-reverse

* `justify-content`规定主轴上的对齐方式

```css
justify-content: center;     /* 居中排列 */
justify-content: flex-start; /* 从行首开始排列。每行第一个元素与行首对齐，同时所有后续的元素与前一个对齐。 */
justify-content: flex-end;   /* 从行尾开始排列。每行最后一个弹性元素与行尾对齐，其他元素将与后一个对齐。 */
justify-content: left;       /* 伸缩元素一个挨一个在对齐容器得左边缘，如果属性的轴与内联轴不平行，则left的行为类似于start。 */
justify-content: right;      /* 元素以容器右边缘为基准，一个挨着一个对齐，如果属性轴与内联轴不平行，则right的行为类似于end。 */
justify-content: space-between;  /* 均匀排列每个元素首个元素放置于起点，末尾元素放置于终点 */
justify-content: space-around;  /* 均匀排列每个元素每个元素周围分配相同的空间 */
justify-content: space-evenly;  /* 均匀排列每个元素每个元素之间的间隔相等 */
justify-content: stretch;       /* 均匀排列每个元素'auto'-sized 的元素会被拉伸以适应容器的大小 */
```

* align-items 规定交叉轴上的对齐方式

```css
align-items: strtch;
align-items: center; /* 居中 */
align-items: flex-start; /* 元素向侧轴起点对齐。 */
align-items: flex-end; /* 元素向侧轴终点对齐。 */
```

* align-content: center 规定出现多根轴线时的对齐方式 仅有一根轴线时该属性无效(了解一下即可)

直接上图理解吧

justify-content

![justify-content](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071010.png)

align-items

![align-items](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071011.png)

align-content

![align-content](https://www.ruanyifeng.com/blogimg/asset/2015/bg2015071012.png)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .div {
      width: 500px;
      height: 500px;
      background-color: aquamarine;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .div2 {
      width: 100px;
      height: 100px;
      background-color: deeppink;
    }
  </style>

</head>

<body>
  <!-- flex简单居中 -->
  <div class="div">
    <div class="div2"></div>
    <div class="div2"></div>
  </div>
</body>

</html>
```

如果还不是很懂😭😭，就看下面的链接或问秦海峰学长吧

[阮一峰的网络日志](http://ruanyifeng.com/blog/2015/07/flex-grammar.html)

[菜鸟](https://www.runoob.com/w3cnote/flex-grammar.html)

#### grid

> CSS 网格是一个用于 web 的二维布局系统。利用网格，你可以把内容按照行与列的格式进行排版。另外，网格还能非常轻松地实现一些复杂的布局。

* 一个网格通常具有许多的**列（column）** 与**行（row）**，以及行与行、列与列之间的间隙，这个间隙一般被称为**沟槽（gutter）**。

![img](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids/grid.png)

与flex一样,通过父元素开启`display:grid;`来定义一个网格,与弹性盒子不同的是，在定义网格后，网页并不会马上发生变化。因为`display: grid`的声明只创建了一个只有一列的网格，所以你的子项还是会像正常布局流那样从上而下一个接一个的排布。

为了容器成为一个网格，我们需要加一些列。

```css
.wrapper {
  display: grid;
  /*  声明了三列，宽度分别为 200px 100px 200px */
  grid-template-columns: 200px 100px 200px;
  grid-gap: 5px;
  /*  声明了两行，行高分别为 50px 50px  */
  grid-template-rows: 50px 50px;
}
```

###### row-gap属性，colum-gap属性，gap属性

1. row-gap

        设置行与行之间的间距（行间距）

2. colum-gap

        设置列于列之间的间距（列间距）

3. gap属性是column-gap和row-gap的合并简写形式

###### repeat() 函数

可以简化重复的值。该函数接受两个参数，第一个参数是重复的次数，第二个参数是所要重复的值。比如上面行高都是一样的，我们可以这么去实现，实际效果是一模一样的

```css
.wrapper-1 {
  display: grid;
  grid-template-columns: 200px 100px 200px;
  grid-gap: 5px;
  /*  2行，而且行高都为 50px  */
  grid-template-rows: repeat(2, 50px);
}
```

###### auto-fill 关键字

表示自动填充，让一行（或者一列）中尽可能的容纳更多的单元格。`grid-template-columns: repeat(auto-fill, 200px)` 表示列宽是 200 px，但列的数量是不固定的，只要浏览器能够容纳得下，就可以放置元素

```css
.wrapper-2 {
  display: grid;
  grid-template-columns: repeat(auto-fill, 200px);
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

###### fr 关键字

`Grid` 布局还引入了一个另外的长度单位来帮助我们创建灵活的网格轨道。`fr` 单位代表网格容器中可用空间的一等份。`grid-template-columns: 200px 1fr 2fr` 表示第一个列宽设置为 200px，后面剩余的宽度分为两部分，宽度分别为剩余宽度的 1/3 和 2/3

```css
.wrapper-3 {
  display: grid;
  grid-template-columns: 200px 1fr 2fr;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

###### minmax() 函数

我们有时候想给网格元素一个最小和最大的尺寸，`minmax()` 函数产生一个长度范围，表示长度就在这个范围之中都可以应用到网格项目中。它接受两个参数，分别为最小值和最大值。`grid-template-columns: 1fr 1fr minmax(300px, 2fr)` 的意思是，第三个列宽最少也是要 300px，但是最大不能大于第一第二列宽的两倍。

```css
.wrapper-4 {
  display: grid;
  grid-template-columns: 1fr 1fr minmax(300px, 2fr);
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

###### auto关键字

由浏览器决定长度。通过 `auto` 关键字，我们可以轻易实现三列或者两列布局。`grid-template-columns: 100px auto 100px` 表示第一第三列为 100px，中间由浏览器决定长度

```css
.wrapper-5 {
  display: grid;
  grid-template-columns: 100px auto 100px;
  grid-gap: 5px;
  grid-auto-rows: 50px;
}
```

还有很多...

给个示例学习一下

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .main {
      width: 1200px;
      margin: 30px auto 0;
    }

    .grid-container {
      display: grid;
      grid-template-columns: 385px 180px 180px 180px 180px;
      grid-template-rows: 180px 180px 180px 180px;
      grid-column-gap: 24px;
      grid-row-gap: 24px;
    }

    .grid-item {
      border: 2px solid rgb(233, 171, 88);
      border-radius: 5px;
      background-color: rgba(233, 171, 88, .5);
    }

    .grid-item.one {
      grid-row-start: 1;
      grid-row-end: 3;
    }

    .grid-item.two {
      grid-column-start: 2;
      grid-column-end: 4;
    }

    .grid-item.three {
      grid-column-start: 4;
      grid-column-end: 6;
    }

    .grid-item.six {
      grid-column-start: 4;
      grid-column-end: 6;
    }

    .grid-item.eight {
      grid-column-start: 2;
      grid-column-end: 4;
      grid-row-start: 3;
      grid-row-end: 5;
    }

    .grid-item.ten {
      grid-column-start: 5;
      grid-column-end: 6;
      grid-row-start: 3;
      grid-row-end: 5;
    }
  </style>

</head>

<body>
  <div class="main">
    <div class="grid-container">
      <div class="grid-item one">1</div>
      <div class="grid-item two">2</div>
      <div class="grid-item three">3</div>
      <div class="grid-item four">4</div>
      <div class="grid-item five">5</div>
      <div class="grid-item six">6</div>
      <div class="grid-item seven">7</div>
      <div class="grid-item eight">8</div>
      <div class="grid-item nine">9</div>
      <div class="grid-item ten">10</div>
      <div class="grid-item eleven">11</div>
      <div class="grid-item twelve">12</div>
    </div>
  </div>

</body>

</html>
```

但是这个还是需要掌握的，就先看看下面的阮神的博客学习吧，实在不会再问秦海峰学长

[阮一峰的网络日志](http://ruanyifeng.com/blog/2015/07/flex-grammar.html)

#### float(比较少用)

* 通过浮动可以使一个元素向其父元素的左侧或右侧移动

* float : left(向左浮动) | right(向右浮动) | none(默认的不浮动)

> 注意，元素设置浮动以后,会完全从文档流中脱离，不再占用文档流的位置,所以元素下边的还在文档流中的元素会自动向上移动

浮动的特点：

1. 浮动元素会完全脱离文档流，不再占据文档流中的位置

2. 设置浮动以后元素会向父元素的左侧或右侧移动

3. 浮动元素默认不会从父元素中移出

4. 浮动元素向左或向右移动时，不会超过它前边的其他浮动元素

5. 如果浮动元素的上边是一个没有浮动的块元素，则浮动元素无法上移

6. 浮动元素不会超过它上边的浮动的兄弟元素，最多最多就是和它一样高

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
    .normal div,.float div{
    width:200px;
    height:200px;
    }
    .box1{
    background-color:green;
    }
    .box2{
    background-color:deeppink;
    }
    .float div{
        float:left;
    }
  </style>
</head>
<body>
  <div class="normal">
    <div class="box1"></div>
    <div class="box2"></div>
  </div>
  <div class="float">
    <div class="box1"></div>
    <div class="box2"></div>
  </div>
</body>
</html>
```

- 这里提一嘴高度塌陷问题

> 在浮动布局中，父元素的高度默认是被子元素撑开的， 当子元素浮动后，其会完全脱离文档流，子元素从文档流中脱离 将会无法撑起父元素的高度，导致父元素的高度丢失父元素高度丢失以后，其下的元素会自动上移，导致页面的布局混乱 所以高度塌陷是浮动布局中比较常见的一个问题，这个问题我们必须要进行处理！

###### BFC(块级格式化上下文)

- BFC是一个CSS中的一个隐含的属性，可以为一个元素开启BFC

        开启BFC该元素会变成一个独立的布局区域

- 元素开启BFC后的特点：

       1.开启BFC的元素不会被浮动元素所覆盖

       2.开启BFC的元素子元素和父元素外边距不会重叠

       3.开启BFC的元素可以包含浮动的子元素

- 可以通过一些特殊方式来开启元素的BFC：

        1、设置元素的浮动（不推荐）

        2、display 为 inline-block、table-cells、flex（不推荐）

        3、将元素的overflow设置为一个非visible的值

        4、绝对定位元素：position (absolute、fixed)

###### 清除浮动

- 如果我们不希望某个元素因为其他元素浮动的影响而改变位置， 可以通过clear属性来清除浮动元素对当前元素所产生的影响

- clear
  
  - 作用：清除浮动元素对当前元素所产生的影响
  
  - 可选值：left | right | both
    
    - left 清除左侧浮动元素对当前元素的影响
    
    - right 清除右侧浮动元素对当前元素的影响
    
    - both 清除两侧中最大影响的那侧

- 原理：

       设置清除浮动以后，浏览器会自动为元素添加一个上外边距，以使其位置不受其他元素的影响

* 一般通过两个伪元素结合或开启BFC来清除浮动

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <style>
    .clearfix::before,
    .clearfix::after {
    content: '';
    display: table;
    clear: both;
    }
    .float div{
    width:200px;
    height:200px;
    }
    .box1{
    background-color:green;
    }
    .box2{
    background-color:deeppink;
    }
    .float div{
        float:left;
    }
  </style>
</head>
<body>
  <div class="float clearfix">
    <div class="box1"></div>
    <div class="box2"></div>
  </div>
</body>
</html>
```

## css动画

在CSS里同样存在三维坐标系

![img](https://img-blog.csdnimg.cn/img_convert/afc0973be020c1b2e92db442f2a36baf.png)

#### 转换

* 在CSS中通过设置transfrom属性实现转换效果

transfrom的值主要包括:

1. translate 平移
   
   ![https://cdn.nlark.com/yuque/0/2021/png/2655286/1634823471264-c91336b3-4e0f-4019-a4db-729c56d9982d.png](https://cdn.nlark.com/yuque/0/2021/png/2655286/1634823471264-c91336b3-4e0f-4019-a4db-729c56d9982d.png)

2. rotate 旋转
   
   ![img](https://cdn.nlark.com/yuque/0/2021/png/2655286/1634823530934-8a8aaf35-5356-4f07-af11-3eb8a1f6abfd.png)

3. scale 缩放
   
   ![img](https://cdn.nlark.com/yuque/0/2021/png/2655286/1634823511781-d09a6412-c013-495a-8296-9259f4e010d0.png)

4. skew 扭曲
   
   ![img](https://cdn.nlark.com/yuque/0/2021/png/2655286/1634823570708-ac161668-0a88-46b2-b395-05ff50848c32.png)

#### 过渡

在CSS中我们可以为样式的变化添加过渡时间，无需依赖js，比如一个图片的透明度从1变化到0，我们添加了过渡效果之后就从原本的直接消失变成了淡出效果

* transion的值主要由四部分组成
  
  1. transition-property (需要过渡的css属性,比如color,opacity等)
  
  2. transition-duration (过渡效果需要消耗的时间)
  
  3. transition-timing-function (过渡效果的时间曲线,比如先快后慢，匀速等)
  
  4. transition-delay (过渡效果从何时开始)

#### 动画帧

* 在css3中提供了非常强大的功能 --animation,它可以取代许多网页动画图像和Js实现的效果

使用动画帧需要两部分

1. 使用@keyframes创建一个css3动画

2. 使用animation属性指定该元素使用哪个css3动画

```css
@keyframes myfirst
{
    from {background: red;}
    to {background: yellow;}
}

/* 它还可以具体到每一帧 */
@keyframes mysecond
{
    0%   {background: red;}
    25%  {background: yellow;}
    50%  {background: blue;}
    100% {background: green;}
}

.box1{
    animation: myfirst 5s;
    width:200px;
    height:200px
}

.box2{
    animation:mysecond 5s;
    width:200px;
    height:200px
}
```

```html
<!DOCTYPE html>
<html lang="">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
</body>
</html>
```

你将看到两个一直变色的盒子 

animation同样也是一种简写，它由以下属性组成

- animation-delay - 当动画开始时设置
- animation-direction - 设置动画是否应该在交替循环中反向播放
- animation-duration - 设置动画完成一个周期所需的秒数或毫秒数
- animation-fill-mode - 设置当动画完成或延迟时使用的样式
- animation-iteration-count - 设置动画播放的次数
- animation-name - 设置@keyframes动画的名称
- animation-play-state - 设置动画是否正在运行或暂停
- animation-timing-function - 设置动画的速度曲线
