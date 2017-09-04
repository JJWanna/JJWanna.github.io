---
title: promise详解
date: 2017-09-04 10:00:00
tags:
---
### 什么是promise?
Promise是抽象异步处理对象以及对其进行各种操作的组件。
Promise最初被提出是在E语言中，它是基于并列/并行处理设计的一种编程语言。
现在Javascript也拥有了这种特性，这就是要介绍的javascript Promise。
如果说到基于javascript的异步处理，大多数都会想到利用回调函数。
使用了回调函数的异步处理：


```$xslt
getAsync("fileA.txt",function(error,result){
    if(error){ //取得失败时的处理
        throw error;
    }
    
    //取得成功时的处理
    
});
```

<!-- more -->

传给回调函数的参数为（error对象，执行结果）组合。
Node.js等则规定在javascript的回调函数的第一个参数为Error的对象，这也是它的一个惯例。

像上面这样基于回调函数的异步处理如果统一参数使用规则的话，写法也会很明了。
但是，这也仅是编码规约而已，即使采用不同的写法依然不会出错。

而promise则是把类似的异步处理对象和处理规则进行规范化，并按照采用统一的接口来编写，
而采取规定方法之外的写法都会出错。

下面使用了Promise进行异步处理的一个例子

```$xslt
var promise = getAsyncPromise("fileA.txt");
promise.then(function(result){
    //获取文件内容成功时的处理
}).catch(function(error){
    //获取文件内容失败时的处理
});
```
返回promise对象

我们可以向这个预设了抽象化异步处理的promise对象，注册这个promise对象执行成功时和失败时相应的回调函数。

这和回调函数方式相比有哪些不同之处呢? 在使用promise进行一步处理的时候,我们 必须按照接口规定的方法编写处理代码。

也就是说,除promise对象规定的方法(这里的 then 或 catch )以外的方法都是不可以使 用的, 而不会像回调函数方式那样可以自己自由的定义回调函数的参数,而必须严格 遵守固定、统一的编程方式来编写代码。

这样,基于Promise的统一接口的做法, 就可以形成基于接口的各种各样的异步处理模式。
所以,promise的功能是可以将复杂的异步处理轻松地进行模式化, 这也可以说得上是 使用promise的理由之一。

### Promise简介
在es6 Promise标准中定义的API还不是很多。
目前大致有以下三种类型

#### Constructor
Promise类似于XMLHttpRequest，从构造函数Promise来创建一个新promise对象作为接口。
要想创建一个promise对象，可以使用new来调用Promise的构造器来进行实例化。

```
var promise =new Promise(function(resolve,reject){
    //异步处理
    //处理结束后、调用resolve或reject
})
```

#### Instance Method
对通过new生成的promise对象为了设置其值在resolve(成功)/reject（失败）时调用的回调函数可以使用
promise.then()实例方法。


```$xslt
promise.then(onFulfilled,onRejected)
```

resolve(成功)时
    onFulfilled会被调用
reject(失败)时
    onRejected会被调用
onFulfilled、onRejected两个都为可选参数。

promise.then成功和失败时都可以使用。另外在想对异常进行处理时可以采用
promise.then(undefined,onRejected)这种方式，只指定reject时的回调函数即可。
不过这种情况下promise.catch(onRejected)应该是个更好的选择。

```$xslt
promise.catch(onRejected)

```


#### Static Method
像promise这样全局对象还拥有一些静态方法。
包括promise.all()还有Promise.resolve()等在内，主要都是一些对Promise进行操作的辅助方法。

### Promise workflow

promise-workflow.js

```
function asyncFunction(){
    return new Promise(function(resolve,reject){
        setTimeout(function(){
            resolve('Async Hello world');
        },16);
    })   
}

asyncFunction().then(function(value){
    console.log(value);  //=> 'Async Hello world'
}).catch(function(error){
    console.log(error);
})
```

new Promise构造器之后，会返回一个promise对象
为Promise对象用设置 .then调用返回值时的回调函数。
asyncFunction 这个函数会返回promise对象，对于这个promised对象，我们调用它的then方法
来设置resolve的回调函数，catch方法来设置发生错误时的回调函数。

该promise对象会在setTimeout之后的16ms时被resolve，这时then的回调函数会被调用，并输出'Async Hello world'。
在这种情况下catch的回调函数并不会被执行（因为promise返回了resolve）,不过如果运行环境
没用提供setTimeout函数的话，那么上面代码在执行中就会产生异常，在catch中设置的回调函数就会被执行。

像promise.then(onFulFilled,onRejected)的方法声明一样，如果不使用catch方法的话，如下所示的代码也能完成相同的工作。
```$xslt
asyncFunction().then(function(value){
    console.log(value);
},function(error){
    console.log(error);
});
```

### Promise的状态

用new Promise实例化的promise对象有以下三个状态。


"has-resolution"-Fulfilled
    resolve(成功)时。此时会调用onFulfilled
    
"has-rejection"-Rejected
    reject(失败)时。此时会调用onRejected
    
"unresolved"-Pending
    既不是resolve也不是reject状态。也就是promise对象刚被创建后的初始化状态等
    
Promise对象的状态，从pending转换为Fulfilled或Rejected之后，这个promise对象的状态就不会再发生任何变化。

promise与event等不同，在.then后执行的函数可以肯定地说只会被调用一次。
另外，Fulfilled和Rejected这两个中的任一状态都可以表示为settled(不变的)。

Settled
    resolve(成功)或 reject(失败)
    
    从Pending和Settled的对称关系来看,Promise状态的种类/迁移是非常简单易懂的。 当promise的对象状态发生变化时,用 .then 来定义只会被调用一次的函数。
    
### 编写Promise代码

#### 创建promise对象

创建promise对象的流程如下所示。
1. new Promise(fn)返回一个promise对象
2. 在fn 中指定异步等处理

   - 处理结果正常的话，调用resolve(处理结果值)
   - 处理结果错误的话，调用reject(error对象)
    

我们的任务是用Promise来通过异步处理方式来获取XMLHttpRequest(XHR)的数据。

#### 创建XHR的promise对象
首先，创建一个用Promise把XHR处理包装起来的名为getURL的函数。

xhr-promise.js

```$xslt
function getURL(URL){
    return new Promise(function(resolve,reject){
        var req=new XMLHttpRequest();
        req.open('GET',URL,true);
        req.onload=function(){
            if(req.status===200){
                resolve(req.responseText);
            }else{
                reject(new Error(req.statusText));
            }
         };
         req.onerror=function(){
            reject(new Error(req.statusText));
         };
         req.send();
    });
    
}

//运行示例
var URL="http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});
```
getURL只有在通过XHR取得结果状态为200时才会调用resolve-也就是只有数据取得成功时，而其他情况（取得失败）
时则会调用reject方法。
resolve(req.responseText)在response的内容中加入了参数。
resolve方法的参数并内有特别的规则。基本上把要传给回调函数参数放进去就可以了。（then方法可以接收到这个参数值）
熟悉nondejs的人，经常会在写回调函数时将callback(error,response)的第一个参数设为error对象，而在Promise中resolve/reject则担当了这个职责（处理正常和异常的情况)，
所以在resolve方法中只传一个response参数是没有问题的。


#### reject函数

XHR中onerror事件触发的时候就是发生错误时，所以理所当然调用reject。
发生错误时要像这样reject(new Error(req.statusText));,创建一个Error对象后再将具体的值传进去。
传给reject的参数也没有什么特殊的限制，一般只要是error对象(获取继承自error对象)就可以。

传给reject的参数，其中一般是包含了reject原因的error对象。本次因为状态值不等于200而被reject,
所以reject中放入的是statusText。（这个参数的值可以被then方法的第二个参数或者catch方法中使用）


### 编写promise对象处理方法
让我们在实际中使用以下刚才创建的返回promise对象的函数

```$xslt
getURL('http://example.com/'); =>返回promise对象
```


如果promises overview中做的简单介绍一样，promise对象拥有几个实例方法，我们使用这些
实例方法来为promise对象创建依赖于promise的具体状态，并且只会被执行一次的回调函数。

为promise对象添加处理方法主要有以下两种：
- promise对象被resolve时的处理 （onFulfilled）
- promise对象被reject时的处理（onRejected）

### promise value flow
首页，我们来尝试一下为getURL通信成功并取到值时添加的处理函数。
此时所谓的通信成功，指的就是在被resolve后，promise对象变为Fulfilled状态。
被resolve后的处理，可以在.then方法中传入想要的调用的函数。
```$xslt
var URL="http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
});

```

为了方便理解我们把函数命名为onFulfilled
getURL函数中的resolve(req.responseText);会将promise对象变为resolve(Fulfilled)状态，同时使用其
值调用onFulfilled函数。
不过目前我们还没有对其中可能发生的错误做任何处理，接下来，我们就来为getURL函数添加发生错误时的异常处理。
此时，发生错误，指的也就是reject后promise对象变为Rejected状态。

被reject后的处理，可以在 .then 的第二个参数 或者是在 .catch 方法中设置想要调用的 函数。


把下面reject时的处理加入到刚才的代码，如下所示。

```$xslt
var URL="http://httpbin.org/status/500";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
  console.error(error);  
})
```
服务端返回的状态码为500
为了方便理解函数被命名为onRejected
在getURL的处理中发生任何异常，或者被明确reject的情况下，该异常原因（Error对象）会作为
.catch方法的参数被调用。
其实.catch只是promise.then(undefined,onRejected)的别名而已。如下代码也可以完成同样的功能。

```$xslt
getURL(URL).then(onFulfilled,onRejected);

```
onFulfilled,onRejected是和刚才相同的函数
一般来说，使用.catch来将resolve和reject处理分开来写是比较推荐的做法,这两者的区别会在then
和 catch的区别中再做详细介绍。


总结：

- 用new Promise方法创建promise对象

- 用 .then 或 .catch添加promise对象的处理函数
















