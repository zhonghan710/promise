关于Promise:
* 什么是 Promise？
Promise 是异步编程的一种解决方案，比传统的异步解决方案【回调函数】和【事件】更合理、更强大。现已被 ES6 纳入进规范中。
Promise是一个方案，用来解决多层回调嵌套的解决方案

Promise 的常规写法:
```
new Promise(请求1)
    .then(请求2(请求结果1))
    .then(请求3(请求结果2))
    .then(请求4(请求结果3))
    .then(请求5(请求结果4))
    .catch(处理异常(异常信息))
```
Promise 的写法更为直观，并且能够在外层捕获异步函数的异常信息

* Promise 解决的痛点是什么？
回调地狱
需要根据前一个网络请求的结果，再去执行下一个网络请求，代码大概如下：
```
请求1(function(请求结果1){
    请求2(function(请求结果2){
        请求3(function(请求结果3){
            请求4(function(请求结果4){
                请求5(function(请求结果5){
                    请求6(function(请求结果3){
                        ...
                    })
                })
            })
        })
    })
})
```
回调地狱带来的负面作用有以下几点：
1、代码臃肿;
2、可读性差;
3、耦合度过高，可维护性差;
4、代码复用性差;
5、容易滋生 bug;
6、只能在回调里处理异常;
为了能使用一种更加友好的代码组织方式，解决异步嵌套的问题：
```
let 请求结果1 = 请求1();
let 请求结果2 = 请求2(请求结果1);
let 请求结果3 = 请求3(请求结果2);
let 请求结果4 = 请求2(请求结果3);
let 请求结果5 = 请求3(请求结果4);
```

* Promise 的业界实现都有哪些？
业界著名的 Q 和 bluebird，bluebird 甚至号称运行最快的类库
3、Promise 解决的痛点还有其他方法可以解决吗？如果有，请列举。
4、Promise 如何使用？

* Promise 常用的方法，方法的作用？
1、race
多个promise 任务同时执行，只返回最先执行完的 Promise 任务的结果；
2、all
多个promise 任务同时执行，返回所有promise 任务的执行结果；

6、Promise 在事件循环中的执行过程是怎样的？
8、能不能手写一个 Promise 的polyfill。
Promise方法你最常用什么写法？

* 构造函数传入的参数是什么类型？
它接收的参数是一个匿名函数，任何情况下，它里面的js最先执行。
这个匿名函数也有二个参数：
1、resolve，完成，操作成功时调用。
2、reject，失败，操作失败时调用。

* 传入的该函数是会立刻执行的吗？
会在本次new 操作立刻执行。
第一次resolve就确定了自己是成功还是失败。第二次没用了。添加reject也改变不了
* 若调用了两次resolve方法会怎么样？
只执行第一次resolve

* 发生异常会怎么样？
promise的原理？jquery的ajax返回的是promise对象吗？(百度面试)
promise 只有2个状态，成功和失败，怎么让一个函数无论成功和失败都能被调用？
Promise.all() 是干什么用的，怎么用？

* 初始化Promise对象的方法
new Promise(fn)
Promise.resolve(fn)

```
/*
*end
*nextTick
*then
*setImmediate
*
*process.nextTick 和 promise.then 都属于 microtask
* 而 setImmediate 属于 macrotask
* 在事件循环的 check 阶段执行
* 事件循环的每个阶段（macrotask）之间都会执行 microtask，事件循环的开始会先执行一次 microtask
*
 */

process.nextTick(()=>{
    console.log('nextTick');
});
Promise.resolve().then(()=>{
    console.log('then');
});

setImmediate(()=>{
    console.log('setImmediate');
});
console.log('end');
```
Promise扩展之事件循环机制：
macrotasks:
* setTimeout
* setInterval
* setImmediate
* requestAnimationFrame
* I/O
* UI rendering

microtasks:
* process.nextTick
* Promises
* Object.observe
* MutationObserver

任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。
同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；
异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。


执行优先级的问题 microtasks > macrotasks

在每一次事件循环中，macrotask 只会提取一个执行，而 microtask 会一直提取，直到 microtasks 队列清空。
而事件循环每次只会入栈一个 macrotask ，主线程执行完该任务后又会先检查 microtasks 队列并完成里面的所有任务后再执行 macrotask

```
setImmediate(function(){
    console.log(1);
},0);
setTimeout(function(){
    console.log(2);
},0);
new Promise(function(resolve){
    console.log(3);
    resolve();
    console.log(4);
}).then(function(){
    console.log(5);
});
console.log(6);
process.nextTick(function(){
    console.log(7);
});
console.log(8);

//3 4 6 8 7 5 2 1
```