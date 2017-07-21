#Promise
Promise是一种封闭和组合未来值的易于复制的机制。
``` javascript
function foo(x) {
	//可能耗时的工作
	//构造一个listener事件通知处理对象来返回
	return listener;
}
var evt = foo(4);
evt.on("completion",function() {
	//可以进行下一步了
});
evt.on("failure", function() {
	//出错处理
});
//让bar(..)侦听foo(..)的完成
bar(evt);
//让baz(..)侦听foo(..)的完成
baz(evt);
```
事件侦听对象evt就是Promise的一个模拟。
在基于Promise的方法中，前面的代码片段会让foo(..)创建并返回一个Promise实例，而这个Promise会被传递到bar(..)和baz(..)。
``` javascript
function foo(x) {
	//耗时工作
	//构造并返回一个promise
	return new Promise(function(resolve,reject) {
		//最终调用resolve(..)或者reject(..)
		///这是这个promise的决议回调
	}）；
}
var p = foo(4);
bar(p);
baz(p);
```
new Promise(function(..){..})的传入函数会立即执行（不会像then(..)中的回调一样异步延迟），它有两个参数resolve和reject为promise的决议函数。如果Promise的创建过程中或在查看其决议结果过程中的任何时间地点上出现了一个javascript异常错误，那这个异常就会被捕捉，并且会使这个Promise被拒绝。
Promise.resolve(..)提供了可信任的Promise的封闭工具
``` javascript
//不要只是这么做
foo(42)
.then(function(v){
	console.log(v);
}）；
//而要这么做
Promise.resolve(foo(42))
.then(function(v){
	console.log(v);;
});
```
Promise 对象用于一个异步操作的最终完成（或失败）及其结果值的表示。(简单点说就是处理异步请求。。我们经常会做些承诺，如果我赢了你就嫁给我，如果输了我就嫁给你之类的诺言。这就是promise的中文含义。一个诺言，一个成功，一个失败。)

Promise非常好，它们解决了我们因只用回调的代码而备受困扰的控制反转问题，而它们并没有摈弃回调，只是把回调的安排转交给了一个位于我们和其他工具之间的可信任的中介机制。
Promise 对象是一个代理对象（代理一个值），被代理的值在Promise对象创建时可能是未知的。它允许你为异步操作的成功和失败分别绑定相应的处理方法（handlers ）。 这让异步方法可以像同步方法那样返回值，但并不是立即返回最终执行结果，而是一个能代表未来出现的结果的promise对象

一个 Promise有以下几种状态:

pending: 初始状态，不是成功或失败状态。
fulfilled: 意味着操作成功完成。
rejected: 意味着操作失败。
pending 状态的 Promise 对象可能触发fulfilled 状态并传递一个值给相应的状态处理方法，也可能触发失败状态（rejected）并传递失败信息。当其中任一种情况出现时，Promise 对象的 then 方法绑定的处理方法（handlers ）就会被调用（then方法包含两个参数：onfulfilled 和 onrejected，它们都是 Function 类型。当Promise状态为fulfilled时，调用 then 的 onfulfilled 方法，当Promise状态为rejected时，调用 then 的 onrejected 方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争）。

#生成器迭代器
把生成器看作一个值的生产者，通过迭代器接口的next()调用一次取出一个值。
``` javascript
function foo(x, y) {
	ajax("http://someurl.com?x=" + x +"&y=" + y,
	function(err,data) {
		if(err) {
			it.throw( err );
		}
		else {
			it.next(data);
		}
	}
	)
}
function *main() {
	try {
		var text = yield foo(11, 13);
		console.log(text);
	}
	catch (err) {
		console.log(err);
	}
}
var it = main();
//启动生成器
it.next();
```
#生成器+Promise
``` javascript
function foo(x,y) {
	return request(
		"http://someurl.com?x=" + x +"&y=" + y,
	);
}
function *main() {
	try {
		var text = yield foo(11, 13);
		console.log(text);
	}
	catch (err) {
		console.log(err);
	}
}
var it = main();
var p = it.next().value;
//等待promise p决议
p.then(
	function(text) {
		it.next(text);
	},
	function(err) {
		it.throw(err);
	}
);
```
获得Promise和生成器最大效用的最自然的方法就是yield出来一个promise，然后通过这个promise来控制生成器的迭代器。
