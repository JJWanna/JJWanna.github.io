---
title: css工作流
date: 2017-03-31 16:09:11
tags: css工作流 css预处理器 css后处理器
---

#### css预处理器

“An preprocessor is a program that processes its input to produce output that is used as input to another program ”
处理特定格式源文件到目标css的处理程序

* 预处理器变革


    body {
        left:<%= left  %>px;
        color:<%= color|highlight: 10% %>;
    }
    
    @base:#f938ab;
    box-shadow(@style,@c)when(iscolor(@c)){
        -webkit-box-shadow:@style @c;
        box-shadow:       @style @c;
    }
    box{
        color:saturate(@base,5%);
        border-color:lighten(@base,30%);
        div{.box-shadow(0 0 5px,30%)}
    }

### 预处理器常用规范
* 变量
* 混合（Mixin） Extend
* 嵌套规则
* 运算
* 函数
* Namespaves & Accessors
* Scope
* 注释

W3C标准实现

    .m-layer{
        height:var(--height);
        width:var(--width);
        background-color:blue;
    }
    .m-layer-sm{
        --height:100px;
        --width:100px;
    }
    .m-layer-bg{
        --height:200px;
        --width:200px;
    }
    
    <div class="m-layer m-layer-bg"></div>
    
### CSS后处理器
* css压缩 ClEAN-CSS
* 自动添加浏览器前缀 Autoprefixer
* CSS更加美观排序 CSScomb
* Rework 取代Styles后处理器发热
* 前后都行 PostCss


    :root{
        --red:#d33;
    }
    a{
        &:hover{
            color:color(var(--red) a(54%));
        }
    }
    
    a:hover{
        color:#dd3333;
        color:rgba(221,51,51,0.54);
    }
![](/images/170331-2.png)
![](/images/170331-1.png)
抽象语法树（Abstract Syntax Tree,AST）作为程序的一种中间表示形式
   
#### POSTCSS值得收藏的插件
* POSTCSS-CUSTOM-PROPERTIES 运行时变量
* POSTCSS-SIMPLE-VARS 与SCSS一致的变量实现
* POSTCSS-MIXINS 实现类似SASS的@MIXIN的功能
* POSTCSS-EXTEND 实现类似SASS的继承功能
* POSTCSS-IMPORT 实现类似SASS的IMPORT
* CSSNext 面向未来  CSS Grace 修复过去


    var gulp = require('gulp');
    var postcss = require('gulp-postcss');
    var autoprefixer = require('autoprefixer');
    var cssgrace  = require('cssgrace');
    var cssnext  = require('cssnext');
    gulp.task('css', function () {
      var processors = [
        autoprefixer({browsers: ['last 3 version'],
          cascade: false,
          remove: false
        }),
        cssnext(),
        cssgrace
      ];
      return gulp.src('./src/css/*.css')
        .pipe(postcss(processors))
        .pipe(gulp.dest('./dist'));
    });
    gulp.task('watch', function(){
      gulp.watch('./src/css/*.css', ['css']);
    });
    gulp.task('default', ['watch', 'css']);
    
![](/images/170331-3.png)