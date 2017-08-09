---
title: javascript语言精髓 闭包、作用域、原型链
date: 2017-02-11 09:36:06
tags: javascript
---

    
    

### 作用域的种类
作用域由大到小: 
* 程序级
* 文件级
* 函数级
* 块级
   
### javascript作用域
* 全局作用域 
* 函数作用域 
* 块级作用域(ES6)
    
<!--more-->       
    var global = 1;
    function doSomething(){
        var inner = 2;
        globalA = 3; 
    }
    doSomething();
    alert(global);// 1
    alert(globalA);// 3
    alert(inner);// undefined
    
    
### javascript作用域链
什么是作用域链?
在JavaScript中,函数也是对象,函数对象和其它对象一样,拥有可以通过代码访问的属性和一系列仅供JavaScript引擎访问的内部属性。其中一个内部属性是[[Scope]],由ECMA-262标准第三版定义,该内部属性包含了函数被创建的作用域中对象的集合, 这个集合被称为函数的作用域链,它决定了哪些数据能被函数访问。
        

    var test = “aaa”;
    function doSomething(){
    alert(test);
    var test = “bbb”; alert(test); //bbb
    }
    doSomething();
    alert(test);
    // aaa
    

### 变量与函数声明提前


    function foo(){
      alert(test);
      var test = “bbb”;
      alert(test);
    } foo();
    

执行顺序:
1.声明函数foo
2.调用函数foo 
3.声明变量test
4.alert(test);
5.test变量赋值为bbb 
6.alert(test);

### javascript中的this关键字

* this指向哪 ?
在Javascript中,this指向函数执行时的当前对象。 (In JavaScript, as in most object-oriented programming
languages, this is a special keyword that is used within methods to refer to the object on which a method is being invoked.)
——jQuery Fundamentals (Chapter 2), by Rebecca Murphey
* this使用场景？
1.普通函数中:
    严格模式:undefined
    非严格模式:全局对象(window)
2.构造函数中:对象的实例 
3.对象方法: 对象本身 

### call 、apply、bind
* 使用方法:
    1.fn.call(context, arg1, arg2, ..., argn); 
    2.fn.apply(context, [arg1,arg2,...,argn]); 
    3.function(){...}.bind(context)
通过这三个方法可以改变被调用函数中this指向的对象


    if(!(“userName” in window)){
     var userName=“zhengzheng.xz”;
    }
    console.log(userName);
    //undefined
    
    
    
    var obj = {
       user: “zhengzheng.xz”
       getName:function(){
          return this.user;
       }
    }
    var getNameFn = obj.getName;
    console.log(getNameFn());
    //undefined
    console.log(obj.getName());
    //zhengzheng.xz
    
    
### javascript原型与原型链
* 原型对象是什么？
在Javascript中,每定义一个对象(函数)时,对象中都会包含一些预定义的属性。其中函数对象的一个属性就是原型对象prototype。普通对象没有prototype属性,但有 __proto__属性

    
    function f1(){};
    console.log(typeof f1.prototype); //object
    console.log(typeof Function.prototype) //Function,这个特殊
    console.log(typeof Object.prototype) //object
    console.log(typeof Function.prototype.prototype)//undefined
    
* 原型对象有什么用？
面向对象开发、类的继承

        
        function Person(name){
            this.name=name;
        }
        Person.prototype.getName=function(){
            return this.name;
        }
        var xu=new Person('jiaojiao.xz');
        xu.getName(); //jiaojiao.xz
        
* 构造函数
1.使用new关键字调用的函数
2.构造函数可以实例化一个对象
3.返回值,默认返回类的实例 
4.特殊情况: 
    没有返回值
    简单数据类型 
    对象类型
    
    
     function People(name,age){
        this.name=name;
        this.age=age;
     } 
     var people=new People('Byron',26);
     
### 原型链是如何实现的？
1. 每个函数都有一个prototype的对象属性
2. 每个对象都有一个__proto__属性,该属性指向其父类的prototype对象

### 原型对象中的constructor
* 每个原型对象prototype中都有一个constructor属性,默认指向函数本身。

    
    Person.prototype.constructor===Person; //true
    Function.prototype.constructor===Function; //true
    Object.prototype.constructor===Object; //true
    Object.prototype.constructor===Function; //true
    
练习一:

    function make(num){
      return function(){
    return num; }
    }
    var arr = [make(0),make(1),make(2)]; alert(arr[0]()); // 0 alert(arr[1]()); // 1 alert(arr[2]()); // 2
    
练习二：

    var name = ‘global’;
    function A(name){
      alert(name);
      this.name = name;
      var name = ‘1’;
    }
    A.prototype.name = ‘2’;
    var a = new A(‘3’);
    alert(a.name);
    delete a.name;
    alert(a.name);
    // 3
    // 3 // 2
    
练习三：
   
    function fun(n,o){
        console.log(o);
        return {
            fun:function(m){
                return fun(m, n)
            }  
        }
    }
    var a = fun(0);
    a.fun(1); a.fun(2);
    var b = fun(0).fun(1).fun(2).fun(3);
    var c = fun(0).fun(1);
    c.fun(2); c.fun(3);