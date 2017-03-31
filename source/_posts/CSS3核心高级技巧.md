---
title: CSS3核心高级技巧
date: 2017-03-31 11:32:46
tags:  css3
---

* 早期的双飞翼布局+CSS HACK
* 基于移动端的PX与REM转换兼容方案
* 弹性盒模型与Reset的选择
* 自制的ICON-FONT与常用字体排版
* CSS代码检测与团队项目规范
* CSS绘制特殊图形 高级技巧
* CSS高效动画 WorkFlow与分层（详细讲解）
<!--more-->
#### 双飞翼布局+CSS HACK
* position
* float 
* 负边距
* 等高
* 盒子模型
* 清除浮动 

<!--more-->
#### IE6经典bug
>* IE6怪异解析之padding与border算入宽高 
 原因：未加文档声明造成非盒模型解析 
 解决方法：加入文档声明<!doctype html> 
 
>* **IE6在块元素、左右浮动、设定maring时造成margin双倍（双边距）** 
解决方法：display:inline 

>* 以下三种其实是同一种bug，其实也不算是个bug，举个例子：父标签高度20，子标签11，垂直居中，20-11=9，9要分给文字的上面与下面，怎么分？IE6就会与其它的不同，所以，尽量避免。 
1）**字体大小为奇数之边框高度少1px**
解决方法：字体大小设置为偶数或line-height为偶数 
2）**line-height，文本垂直居中差1px** 
解决方法：padding-top代替line-height居中，或line-height加1或减1 
3）**与父标签的宽度的奇偶不同的居中造成1px的偏离**
解决方法：如果父标签是奇数宽度，则子标签也用奇数宽度;如果是父标签偶数宽度，则子标签也用偶数宽度  
  
>* **内部盒模型超出父级时，父级被撑大** 
解决方法：父标签使用overflow:hidden 

>* **line-height默认行高bug**
解决方法：line-height设值 

>* **行标签之间会有一小段空白**
解决方法：float或结构并排(可读性差，不建议)

>* **标签高度无法小于19px**
解决方法：overflow:hidden;

>* **左浮元素 margin-bottom 失效**
解决方法：显示设置高度 or 父标签设置 padding-bottom代替子标签的margin-bottom 再放个标签让父标签浮动，子标签
margin-bototm，即（margin-bottom与float不同时作用于一个标签）

>* **img于块元素中，底边多处空白**
解决方法：父级设置overflow:hidden;或img{display:block;或 img:-5px;

>* **li之间会有间距**
解决方法：float:left;

>* **块元素中又文字及右浮动的元素，航元素换行**
解决方法：将行元素于块元素内的文字前

>* **position下的left,bottom错位** 
解决方法：为父级（relative层）设置宽高或者添加 *zoom:1

>* **子级中有设置position,则父级overflow失效**
解决方法：为父级设置position:relative

### 基于移动端的PX与REM转换兼容方案
* different size  different DPR
* 目前的设计稿一般式640 750 1125 ，一般要先均分成100份，（简荣芳vh,vm）750/10=75px; div宽是240px*120px  css
的书写改为3.2rem * 1.6rem。配合响应式修改html根的大小。
* 字体不建议使用rem,data-dpr属性动态设置字体大小。屏幕变大放更多文字，或者屏幕更大放更多的字。
* 神奇的padding/margin-top等比例缩放间距。

### 弹性盒模型与Reset的选择
* flex模型
* *的杀伤力太大
* reset.css重置 Normalize.css修复 Neat.css融合
* html{box-sizing:border-box;}
   :before,X:after{box-sizing:inherit;}

### ICON-FONT与常用字体排版
* no-images时代，不超过纯色为2的图像
* 宋体非宋体 黑体非黑体 windows下的宋体叫中易宋体 SimSun,Mac是华文宋体STSong。Windows下的黑体叫中易黑体SimHei,
Mac华文黑体STHeiti。
* 不要只写中文字体名，保证西文字体在中文字体前面。Mac-linux-windows
* 切忌不要直接使用设计师psd的设计font-family，关键时刻再去启动font-face(Typo.css,Entry.css,Type.css)
* font-family : sans-serif;系统默认，字体多个单词组成加引号。

### CSS代码检测团队项目规范
1.不要使用多个class选择元素，如a.foo.boo,这在ie6及以下不能正确解析
2.移除空的css规则，如a{}
3.正确使用显示属性，如display:inline不要和width,height,float,margin,padding同时使用
display:inline-block不要和float同时使用等
4.避免过多的浮动，当浮动次数超过十次时，会显示警告
5.避免使用过多的字号，当字号声明超过十种时，显示警告
6.避免使用过多的web字体，当使用超过五次时，显示警告
7.避免使用id作为样式选择器
8.标题元素只定义一次
9.使用width:100%时要小心
10.属性值为0时不要写单位
11.各浏览器专属的css属性要有规范，例如.foo{-moz-border-radius:5px;border-radius:5px}
12.避免使用看起来像正则表达式的css3选择器
13.遵守合模型规则


### CSS绘制高级技巧
* border && border-radius 造就万千可能
* linear-gradient();radial-gradient();
渐变轴：每个色标的推进以圆心为中心同心圆扩散。
