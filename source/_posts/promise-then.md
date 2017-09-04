---
title: 'promise#then'
date: 2017-09-04 15:54:26
tags:
---

#### Promise可以写成方法链的形式


在promise里可以将任意个方法连在一起作为一个方法链（method chain）。
```$xslt
aPromise.then(function taskA(value){
    //task A
}).then(function taskB(value){
    //taskB
}).catch(function onRejected(error){
    console.log(error);
});
```
如果把在then中注册的每个回调函数成为task的话，那么我们就可以通过Promise方法链方式来编写能以taskA->taskB这种流程进行处理的逻辑了。
Promise方法链这种叫法有点长，因此简化为promise chain。
Promise之所以适合编写异步处理较多的应用，promise chain可以算得上是其中一个原因。

#### Promise chain

promise-then-catch-flow.js
```$xslt
function taskA(){
    console.log('taskA');
}
function taskB(){
    console.log('taskB');
}
function onRejected(error){
    console.log('Catch Error A or B',error);
}
function finalTask(){
    console.log('Final Task');
}
var promise=Promisr resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
    .then(finalTask);
```
