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
>* IE6在块元素、左右浮动、设定maring时造成margin双倍（双边距） 
解决方法：display:inline 
>* 以下三种其实是同一种bug，其实也不算是个bug，举个例子：父标签高度20，子标签11，垂直居中，20-11=9，9要分给文字的上面与下面，怎么分？IE6就会与其它的不同，所以，尽量避免。 
1）**字体大小为奇数之边框高度少1px**
解决方法：字体大小设置为偶数或line-height为偶数 
2）**line-height，文本垂直居中差1px** 
解决方法：padding-top代替line-height居中，或line-height加1或减1 
3）**与父标签的宽度的奇偶不同的居中造成1px的偏离**
解决方法：如果父标签是奇数宽度，则子标签也用奇数宽度;如果是父标签偶数宽度，则子标签也用偶数宽度    
4）**内部盒模型超出父级时，父级被撑大**
 解决方法：父标签使用overflow:hidden 
5）**line-height默认行高bug** 
 解决方法：line-height设值 
6）**行标签之间会有一小段空白** 
 解决方法：float或结构并排(可读性差，不建议) 
7）**标签高度无法小于19px**  
解决方法：overflow: hidden; 
8）**左浮元素margin-bottom失效**
 解决方法：显示设置高度 or 父标签设置_padding-bottom代替子标签的margin-bottom or 再放个标签让父标签浮动，子标签 margin- bottom，即(margin-bottom与float不同时作用于一个标签) 
9）**img于块元素中，底边多出空白**
 解决方法：父级设置overflow: hidden; 或 img { display: block; } 或 _margin: -5px;  