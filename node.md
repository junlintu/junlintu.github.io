## Node.js模块和包管理
Node.js框架使用模块和包来组织管理，参照CommonJS标准。

- 核心模块 最底层是Google V8 JavaScript引擎，之上是基于C/C++语言实现的核心模块、并提供向上的接口，在最上层用JavaScript语言对这些接口进行封装、再向外提供给用户使用这些核心模块。在Node.js框架安装好后，这些核心模块以编译好的二进制形式作为框架原生的组合部分存在，require()即使遇到与模块同名的文件也会直接返回编译好的核心模块。
- 加载文件模块 exports是对module.exports的一个引用，如果想输出诸如构造函数这样的单个项目，必须使用mocule.exports = MyConstructor;
使用require()方法引用文件模块时，如果按文件名没有查找到，那么会添加.js和.json后缀尝试加载，如果还没有加.node加载，其中.node则是通过C/C++进行编写的插件，最终会被解析为编译后的插件模块，由dlopen方法进行加载。
- 从node_modules文件夹中加载 如果模块名不是一个本地模块，也没有以'/','../'或是'./'这样的相对路径开头，Node.js框架会往上一级级直顶层目录位置。
- 模块的循环调用问题
``` javascript
// ch02.module-conflict-main.js
console.log();
console.log('main starting');
var a = require('./ch02.module-conflict-a.js');
var b = require('./ch02.module-conflict-b.js');
console.log('in main, a.done=%j,b.done=%j',a.done, b.done);
// ch02.module-conflict-a
console.log('a starting');
exports.done = false;
var b = require('./ch02.module-conflict-b.js');
console.log('in a, b.done = %j',b.done);
exports.done = true;
console.log('a done');
// ch02.module-conflict-b
console.log('b starting');
exports.done = false;
var b = require('./ch02.module-conflict-a.js');
console.log('in b, a.done = %j',a.done);
exports.done = true;
console.log('b done');
// result
main starting
a starting
b starting
in b, a.done = false
b done
in a, b.done = true 
a done 
in main a.done=true, b.done=true     
```
首先主js文件加载a.js文件模块，接着a.js文件又去加载b.js文件模块。这时b.js又会尝试加载a.js文件模块。这时Node.js框架为了防止无限地循环调用，a.js会返回一个unfinished copy给b.js。然后b.js就会停止加载，并将其exports对象.done返回给a.js文件模块。unfinished copy可以理解为未结束完成的对象副本。
- module.exports对象应用
在Node.js框架中，module.exports才是modules模块公开的接口，每个模块都会自动创建一个module对象，对象有一个exports属性，初始值是一个{},module的公开接口就是module.exports。
exports对象和module.exports对象指向同一个变量，因此在修改exports对象的时候也会修改module.exports对象。但如果修改了module.exports不为空，则exports就失效了。在Node.js框架中，如果想定义的模块是一个特定的类型就用module.exports对象，如果想定义的模块是一个典型的“实例化对象”就用exports对象。
- 包管理之package.json
包用于将独立的功能封闭起来，用于发布、更新、依赖管理和进行版本控制。Node.js是根据CommonJS规范实现了包机制，并开发了npm实用工具来解决包发布、更新、管理和获取需求。
	- 顶层目录包含package.json文件，包说明文件
	- bin目录存放二进制文件
	- lib目录存放JavaScript文件。
	- doc目录存放文档
	- test目录存放单元测试
通过npm包管理工具自动生成package.json（npm init）
## Node.js异步I/O与Async流程控制库
Node.js框架异步I/O编程指由于异步I/O等因素，在无法同步获取操作结果时，在回调函数里进行下一步操作的代码编程风格。常见的有Ajax异步请求、setTimeout()以及事件监听等。

- Async串行流程控制
Async是一个基于JavaScript语言的流程控制库，为Node.js设计，同时也可以直接在浏览器中使用。
``` javascript
console.info('-------------Node.js异步编程：Async.series------------');
var async = require("async");
async.series([ //数组或json对象
	function(callback){
		callback(null,'hello');
	},function(callback){
		callback(null,'async');
	},function(callback){
		callback(null,'series');
	}
],function(err, results) {
	console.log(results);
});
console.info();
console.info('---------------Node.js异步编程：Async.series-----------------');
console.info('---------------Node.js异步编程：Async.waterfall-----------------');
async.waterfall([ //只能是数组
	function(callback){
		callback(null,1);
	},
	function(data,callback) {
		console.info(data);
		callback(null,data+2);
	},
	function(data,callback) {
		console.info(data);
		callback(null,data+3);
	}
], function(err,results){
	console.log(results);
});
console.info('---------------Node.js异步编程：Async.waterfall-----------------');
```
async.waterfall与series类似，waterfall()只支持数组对象，通过waterfall()这个函数产生的值都将传给下一个函数，且当函数中的回调函数的第一个参数为非空值时，waterfall()就会停止执行剩余任务。
语法：
series(tasks,callback)
waterfall(tasks,[callback])
parrallel(tasks,[callback])
parrallelLimit(tasks,limit,[backcall])
whilst(test,fn,callback)
doWhilst(fn,test,callback)
queue(worker,concurrency)

## Buffer模块处理
全局核心模块Buffer操作二进制数据类型。
语法：
new Buffer(array)
buf.length
buf.toString([encoding][,starg][,end])
new Buffer(size)
buf.write(string,[offset],[length],[encoding])//offset默认0，length默认buffer.length-offset,encoding默认utf8
Buffer.isBuffer(obj)
Buffer.byteLength(string[,encoding])
buf.readDoubleBE(offset[, noAssert])#

buf.readDoubleLE(offset[, noAssert])#

offset <Number> 0 <= offset <= buf.length - 8
noAssert <Boolean> Default: false
Return: <Number>

buf.readInt32BE(offset[, noAssert])#

buf.readInt32LE(offset[, noAssert])#

offset <Number> 0 <= offset <= buf.length - 4
noAssert <Boolean> Default: false
Return: <Number>
buf.toString([encoding[, start[, end]]])#

encoding <String> Default: 'utf8'
start <Number> Default: 0
end <Number> Default: buffer.length
Return: <String>
buf.copy(targetBuffer[, targetStart[, sourceStart[, sourceEnd]]])#

targetBuffer <Buffer> Buffer to copy into
targetStart <Number> Default: 0
sourceStart <Number> Default: 0
sourceEnd <Number> Default: buffer.length
Return: <Number> The number of bytes copied.
Class Method: Buffer.concat(list[, totalLength])#

Added in: v0.7.11
list <Array> List of Buffer objects to concat
totalLength <Number> Total length of the Buffers in the list when concatenated
Return: <Buffer>
Returns a new Buffer which is the result of concatenating all the Buffers in the list together.
Buffer模块的强项在于操作字节流，会比转化成String效率高得多。
``` javascript

```

## 进程管理
Process模块是Node.js框架的全局内置对象，可以在任何位置访问该对象，这个对象就是Node.js代码宿主的操作系统进程对象。
``` javascript
process.cwd(); //获取应用程序当前的完整路径
process.chdir(); //改变应用程序当前目录
process.pid
process.title
process.versions //版本信息
process.config //配置信息
process.execPath //查看当前进程可执行文件的绝对路径
process.platform //查看当前系统信息
process.arch //当前CPU架构信息
```

## 路径处理应用
``` javascript
var path = require('path');
path.normalize('/home/king/../..'); // /
path.join('home','king',webstorm.js); // home/king/webstorm.js
path.resolve('test',text.txt); // text.txt的绝对路径
path.relative(patha,pathb); // b相对于a的相对路径
path.dirname(path_resolve); // 获取文件所在文件夹绝对路径
path.extname(path_resolve);
path.basename(path_resolve,ext); // 获取绝对路径最后一个'/'字符后面并除去文件后缀名的部分

var url = require('url');
url.parse(); // 地址-》对象
url.format(); // 对象-》地址
url.resolve(); // 地址增加或替换
var querystring = require(querystring);
querystring.parse(url.parse(queryUrl).query);
```

## TCP/UDP网络应用
- 创建TCP服务器
``` javascript
net.createServer([option][,connectionListener]) //创建TCP服务器
option包含缺省值{allowHalfOpen:false}的对象，定义连接方式（全开或半开），缺省全开方式；
connectionListener自动定义为connection事件的监听器，是一个事件监听器的回调函数。
net.createServer(function(sock) {
	sock.on('data',function(data){
		sock.write('Server write:' + data);
	});
	sock.on('close',function(data){

	});
}).listen(PORT,HOST);

var server = net.createServer();
server.listen(PORT,HOST);
server.on('connection',function(sock){
	console.log('CONNECTED:' + sock.remoteAddress + ':' + sock.remotePort);
});
server.on('listening',function(){
	
});
server.on('close',callback);
server.listen(PORT,HOST);
server.address(); //获取服务器地址参数
server.getConnections(function(err,count){

}); //获取服务器连接数
socket.address();
socket.localAddress
socket.localPort
socket.remoteAddress
socket.remotePort
socket.write();
socket.bytesRead //客户端发来的数据字节长度
socket.bytesWritten //回发到客户端的数据字节长度
socket.pause() //暂停data事件的操作
socket.resume() //恢复data事件的操作
```

- 创建TCP客户端
``` javascript
var client = net.connect(PROT,HOST,callback);
client.on('end',callback);
client.bytesRead //服务端发来的数据字节长度
```

- 创建UDP服务端
``` javascript
var dgram = require('dgram');
var server = dgram.createSocket('udp4'); //创建一个UDP服务器
server.on('listening',function(){
	var address = server.address();
	console.log('UDP Server listening on ' + address.address+':'+address.port);
})
server.on('message',function(message,remote){ // 添加一个message事件处理函数
	console.info('emitted "message" event...');
	console.log('UDP Server received from ' + remote.address+':'+remote.port);
})；
server.on('error',function(err){
	console.log("server error:\n" + err.stack);
});
server.on('close',callback);
server.bind(PORT,HOST);
```

- 创建UDP客户端
``` javascript
var dgram = require('dgram');
var client = dgram.createSocket('udp4');
var message = new Buffer('UDP Client to Server: Hello Server!');
client.send(message,0,message.length,PORT,HOST,function(err,bytes){
	if(err) {
	throw err;
	}
	console.log('UDP message sent to...');
});
client.on('close',callback);

```
UDP数据报发送到服务器后，服务器一般是不能回写给客户端的，而TCP可以。UDP协议就是为了快速而安全地发送大数据包而设计的。

- UDP广播服务的实现
``` javascript
//服务端
server.bind(PORT); //由于要接收客户端的广播数据，所以仅绑定端口
//客户端
var HOST = '255.255.255.255'; //广播使用广播地址255.255.255.255
client.bind(function(){
	client.setBroadcast(flag);//向广播网络发送数据报，flag用于设置或清除SO_BROADCAST套接字选项，设置后UDP数据报会被发送到一个本地接口的广播地址
});

```
- 简单聊天室
``` javascript
/**
* net_chat.js
*/
console.info('--------- net chat room ----------');
console.info();
var net = require('net');
var HOST = '127.0.0.1';
var PORT = 6688;
var clientList = [];
console.info('Now create Chat Server...');
console.info();
// 创建TCP服务器
var server = net.createServer();
server.on('connection',function(client){
	clientList.push(client); //socket入栈
	client.name = client.remoteAddress+':'+client.remotePort;
	broadcast('hi,'+client.name+'join in!\r\n',client);
	client.write('hi,'+client.name+'!\r\n');
	// 监听data事件
	client.on('data',function(data){
		broadcast(client.name+'say:'+data+'\r\n',client);
	});
	client.on('end',function(){
		broadcast('hi,'+client.name+'quit!\r\n',client);
		clientList.splice(clientList.indexOf(client),1);
	});
})

function broadcast(message,client) {
	var cleanup = [];
	for(var i=0,len=clientList.length;i<len;i++){
		if(client !== clientList[i]) {
			if(clientList[i].writable) {
				clientList[i].write(message);
			} else {
				cleanup.push(clientList[i]);
				clientList[i].destroy();//清除客户端socket
			}
		}
	}
	for(var i=0,len=cleanup.length;i<len;i++) {
		clientList.splice(clientList.indexOf(cleanup[i]),1);//删除客户端socket
	}
}
server.listen(PORT,HOST);
```

## 文件系统模块应用
updating...
- 重命名文件


## Node.js流（Stream）应用
文件系统fs模块每次操作都要将数据全部写入内存，然后再从内存取出，对于大文件操作I/O速度会有问题。
流Stream是一个抽象接口，数据源和目的地都可以是文件、内存或网络，它被Node.js框架中的很对象所实现，如对一个HTTP服务器的请求是一个流，stdout/stdin也是一个流，流是可读、可写或双工的。HTTP Request对象是可读流，HTTP Response对象是可写流。TCP连接既是可读流又是可写流。

- 创建可读流
updating...


## Node.js Web 开发
HTTP超文本传输协议
HTTPS安全套接字层超文本传输协议
- 构建基本HTTP服务器
``` javascript
var http = require('http');
http.createServer(function(req,res){
	res.writeHead(200,{'Content-type':'text/html'});
	res.write('<h3>Node.js---HTTP</h3>');
	res.end('<p>Create Basic HTTP Server Response to clients request!</p>');//end()是一个每次响应完成之后必须调用的方法
}).listen(6868);
```
http.createServer([requestListener]);//requestListener作为request事件的监听函数，自动添加到request事件
response.writeHead(statusCode,[reasonPhrase],[headers]);//状态码，原因短句，响应头内容
response.write(chunk,[encoding]);//字符串或缓存，默认UTF8
response.end([data],[encoding]);//指定了data相当于response.write(data,encoding);response.end(),该方法将信号发送给服务器，服务器会认为这个消息完成了
server.listen(port,[hostname],[backlog],[callback]);//backlog为连接等待队列的最大长度;callback被作为事件监听添加到listening事件中

- 构建HTTP客户端
``` javascript
var http = require('http');
var options = {
	hostname:'localhost',
	port:6868,
	path:'/',
	method:'POST'
};
var req = http.request(options,function(res){
	console.log('STATUS:'+res.statusCode);
	console.log('HEADERS:'+JSON.stringify(res.headers));
	res.setEncoding('utf8');
	res.on('data',function(chunk){
	console.log('BODY:'+chunk);
	});
});
req.on('error',function(e){
	console.log('problem with request:' + e.message);
});
req.write('data\n');
req.end();
```

- 设定和获取HTTP头文件
HTTP头文件包含了一系列用于控制服务器和客户端的信息，包括通用头信息、请求头信息、响应头信息和实体头信息四个部分。
response.getHeader()与response.setHeader()
response.writeHead(statusCode,{
	'Content-Type':'text/plain',
	'Content-Length':body.length
});

- 发送和处理GET请求
http.get(options,callback);//options为JSON数组对象，用于定义HTTP服务器主机地址、端口号、路径、查询请求字段等信息；callback回调函数，包含一个http.ClientResponse实例类型参数
``` javascript
http.get("http://lcoalhost:6868/signature=123&echostr=456",function(res){
	console.log('STATUS:' + res.statusCode);
	res.setEncoding('utf8');
	res.on('data',function(chunk){
		console.log('BODY:'+chunk);
	});
})
```

- 重定向操作
``` javascript
res.writeHead(301,{
	'Location':'http://localhost:8686/'
})
```
- 服务器多路径处理
