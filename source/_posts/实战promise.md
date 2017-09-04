---
title: 实战promise
date: 2017-09-04 14:13:33
tags:
---
### Promise.resolve
一般情况我们会使用new Promise()来创建promise对象，但除此之外我们也可使用其他方法。
在这里，我们将会学习如何使用 Promise.resolve和Promise.reject这两个方法。

### new Promise的快捷方式
静态方法Promise.resolve(value)可以认为是new Promise()方法的快捷方式。
比如Promise.resolve(42);可以认为是以下代码的语法糖。
```$xslt
new Promise(function(resolve){
    resolve(42);
})
```

<!-- more -->
在这段代码中的resolve(42)；会让这个promise对象立即进入确定(即resolved）状态，并将42传递给后面then里所指定的onFulfilled函数。

方法Promise.resolve(value);的返回值也是一个promise对象，所以我们可以像下面那样接着对其返回值进行.then调用。
```$xslt
Promise.resolve(42).then(function(value){
    console.log(value);
});
```
Promise.resolve作为new Promise()的快捷方式，在进行promise对象的初始化或者编写测试代码的时候都非常方便。

### Thenable
Promise.resolve方法另一个作用就是将thenable对象转换为promise对象。
ES6 Promise里提到了Thenable这个概念，简单来说它就是一个非常类似promise的东西。
就像我们有时称具有.length方法的非数组对象为Array like一样，thenable指的是一个具有.then方法的对象。

这种将thenable对象转换为promise对象的机制要求thenable对象所拥有的then方法应该和Promise所拥有的then方法具有同样的功能
和处理过程，在将thenable对象转换为promise对象的时候，还会巧妙利用thenable对象原来具有的then方法。

到底什么样的对象能算是thenable的呢，最简单的例子就是JQuery.ajax()，它的返回值就是thenable的。

因为JQuery.ajax()的返回值是jqXHR Object对象，这个对象具有.then方法。

```$xslt
$.ajax('/json/comment.json'); //=>拥有'.then'方法的对象
```

这个thenable的对象可以使用Promise.resolve来转换为一个promise对象。
编程了promise对象的话，就能直接使用then或者catch等这些在ES6 Promise里定义的方法了。

### 将 thenable对象转换promise对象
```$xslt
var promise = Promise.resolve($ajax('/json/comment.json')); //=>peomise对象
promise.then(function(value){
    console.log(value);
})
```

#### jQuery 和 thenable

jquery.ajax()返回的值是一个具有.then方法的jqXHR Object对象，这个对象继承了来自Deferred Object的方法和属性。

但是Deferred Object并没有遵循Promises/A+或 ES6 Promise标准，所以即使看上去这个对象转换成了一个Promise对象，
但是会出现缺失部分信息的问题。

这个问题的根源在于jquery的Deferred Object的then方法机制与promise不同。

所以我们应该注意，即使一个对象具有.then方法，也不一定就能作为ES6 Promise对象使用。

Promise.resolve只使用了共同的方法then,提供了在不同的类库之间进行promise对象互相转换的功能。

这种转换为thenable的功能在之前是通过使用Promise.cast来完成的，从它的名字我们也不难想象它的功能是什么。
除了在编写使用Promise的类库等软件时需要对Thenable有所了解之外，通常作为end-user使用的时候，我们可能不会用到此功能。

总结：
Promise.resolve方法，可以认为它的作用就是将传递给它的参数填充(Fulfilled)到Promise对象后并返回这个promise对象。
此外，promise的很多处理内部也是使用了Promise.resolve算法将值转换为promise对象后再进行处理的。


### Promise.reject
Promise.reject(error)是何Promise.resolve(value)类似的静态方法，是new Promise()方法的快捷方式。
比如Promise.reject(new Error('出错了'))就是下面代码的语法糖形式。
```$xslt
new Promise(function(resolve,reject){
    reject(new Error('出错了'));
});
```

这段代码的功能是调用该promise对象通过then指定的onRejected函数，并将错误（Error）对象传递给这个onRejected函数。
```$xslt
Promise.reject(new Error('BOOM')).catch(function(error){
    console.log(error);
})
```
它和Promise.resolve(value)的不同之处在于promise内调用的函数时候reject而不是resolve,这在编写测试代码或者进行debug时，说不定会用的上。



### Promise只能进行异步操作吗

在使用Promise.resolve(value)等方法的时候，如果promise对象立刻就能进入resolve状态的话，那么你是不是觉得.then里面指定的方法就是同步调用的呢？

实际上，.then中指定的方法调用异步进行的。
```$xslt
var promise = new Promise(function(resolve){
    console.log('inner promise'); //1
    resolve(42);
});
promise.then(function(value){
    console.log(value); //3
});
console.log('outer promise'); //2
```
执行上面的代码会输出下面的log,从这些log我们清楚地知道了上面的代码的执行顺序。
```$xslt
inner promise //1
outer promise //2
42            //3
```
由于JavaScript代码会按照文件的从上到下的顺序执行,所以最开始 <1> 会执行,然 后是 被执行。这时候 promise 对象的已经变为确定状态,FulFilled被设置 为了 。
下面的代码 promise.then 注册了 <3> 这个回调函数,这是本专栏的焦点问题。
由于 promise.then 执行的时候promise对象已经是确定状态,从程序上说对回调函数进
行同步调用也是行得通的。
但是即使在调用 promise.then 注册回调函数的时候promise对象已经是确定的状 态,Promise也会以异步的方式调用该回调函数,这是在Promise设计上的规定方针。
因此 <2> 会最先被调用,最后才会调用回调函数 <3> 。 为什么要对明明可以以同步方式进行调用的函数,非要使用异步的调用方式呢?

### 同步调用和异步调用同事存在导致的混乱

其实在Promise之外也存在这个问题，这里我们以一般的使用情况来考虑此问题。
这个问题的本质是接收回调函数的函数，会根据具体的执行情况，可以选择是以同步还是异步的方式对回调函数进行调用。
下面我们以onReady(fn）为例进行说明，这个函数会接收一个回调函数进行处理。

mixed-onready.js
```$xslt
function onReady(fn){
    var readyState=document.readyStaye;
    if(readyState==='interactive' || readyStaye==='complete'){
        fn();
    }else{
        window.addEventListener('DOMContentLoaded',fn);
    }
}
onReady(function(){
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');
```
mixed-onready.js会根据执行时DOM是否已经装载完毕来决定是对回调函数进行同步调用还是异步调用。
如果在调用onReady之前DOM已经载入的话，对回调函数进行同步调用
如果在调用onReady之前DOM还没有载入的话，通过注册DOMContentLoaded事件监听器来对回调函数进行异步调用
因此，如果这段代码在原文件中出现的位置不同，在控制台上打印的log消息顺序也会不同。
为了解决这个问题，我们可以选择统一使用异步调用的方式。

async-onready.js
```$xslt
function onReady(fn){
    var readyState=document.readyState;
    if(readyState==='interactive'||readyState==='complete'){
        setTimeout(fn,0);
    }else{
        window.addEventListener('DOMContentLoaded',fn);
    }
}
onReady(function(){
    console.log('DOM fully loaded parsed');
});
console.log('==Starting==');

```

#### 不要对异步回调函数进行同步调用
- 绝对不能对异步回调函数（即使在数据已经就绪）进行同步调用。
- 如果对异步回调函数进行同步调用的话，处理顺序可能会与预期不符，可能带来意料之外的后果。
- 对异步毁掉函数进行同步调用，还可能导致栈溢出或异常处理错误等问题
- 如果想在将来某时刻调用异步回调函数的话，可以使用setTimeout等一部API。

前面我们看到的promise.then也属于此类，为了避免上述中同时使用同步、异步调用可能引起的混乱问题，Promise在规范上规定Promise只能使用异步调用方式。
最后，如果将上面的onReady函数用Promise重写的话，代码如下：

onready-as-promise.js
```$xslt
function onReadyPromise(){
    return new Promise(function(resolve,reject){
        var readyState= document.readyState;
        if(readyState==='interactive'||readyState==='complete'){
            resolve();
        }else{
            window.addEventListener('DOMContentLoaded',resolve);
        }
     });
}
onReadyPromise().then(function(){
    console.log('DOM fully loaded and parsed');
});
console.log('==Starting==');
```

由于Promise保证了每次调用都以异步方式进行，所以我们在实际编码中不需要调用setTimeout 来自己实现异步调用。







