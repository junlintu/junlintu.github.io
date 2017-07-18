# 你不知道的javascript上卷
## 作用域

javascript是一门编译语言，它不是提前编译的，编译结果也不能在分布式系统中移植。编译的步骤一般如下：
	

 - 分词/词法分析 词法分析是有状态的判断一个分词是一个独立的词法单元还是其他词法单元的一部分，调用有状态的解析规则。
 - 解析/语法分析 抽象语法结构树(AST)
 - 代码生成 将AST转换为可执行代码的过程
 
对javascript来说，大部分情况下编译发生在代码执行前的几微秒，javascript引擎不会有像其他语言编译器那么多的时间来进行优化。
编译器（语法分析、代码生成）-引擎（负责程序的编译及执行过程）-作用域（负责收集交维护由所有声明的标识符组成的一系列查询，确定标识符的访问权限）
引擎执行的查找会影响最终的查找结果。
RHS查询与简单地查找某个变量的值。
LHS查询则是试图找到变量的容器本身，从而可以对其赋值。
赋值操作的目标是谁（LHS）以及谁是赋值操作的源头(RHS)。
## 词法作用域
每一个函数都会创建一个新的作用域气泡。内部标识符会“遮蔽”外部标识符。
无论何种情况，eval(...)都可以在运行时期修改书写期的词法作用域。如果代码中大量使用eval或with，那么运行一定会变得非常慢。
## 函数作用域和块作用域
属于这个函数的全部变量都可以在整个函数的范围内使用及复用。
库通常会在全局作用域中声明一个名字足够独特的变量，通常是一个对象。这个对象用作库的命名空间，所有需要暴露给外界的功能都会成为这个对象的属性。
包装函数的声明以(function...而不是以function...开始，包装函数会被当作函数表达式而不是标准的函数声明来处理，通常在末尾加另外一个()可以立即执行这个函数，这种模式叫IIFE。
匿名函数表达式function()..，书写简单快捷，缺点是调试、递归和可读性差。
let关键字可以将变量绑定到所在的任意作用域中，通常是{...}内部，但let声明不会在块作用域中进行提升。
## 提升
编译阶段中的一部分工作就是找到所有的声明，并用合适的作用域将它们关联起来，所括变量和函数在内的所有声明都会在任何代码被执行前首先被处理。声明本身会被提升，函数声明会被提升，但函数表达式不会被提升，且函数会首先提升，然后才是变量。
##作用域闭包
当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行的。回调就是典型的闭包的例子。
延迟函数的回调会在循环结束时才执行，需要注意。
``` javascript
for (let i=1; i<=5; i++) {
	setTimeout(function timer() {
		console.log(i);
	}, i*1000);
}
```
模块需要具备的条件：
 
 - 必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）。
 - 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有状态。
 - 现代模块机制
``` javascript
 var MyModules = (function Manager() {
	 var modules = {};
	 function define(name, deps, impl) {
		 for (var i=0; i<deps.length; i++) {
			 deps[i] = modules[deps[i]];
		}
		modules[name] = impl.apply( impl, deps );
	}
	function get(name) {
		return modules[name];
	}
	return {
		define: define,
		get: get
	};
})();
MyModules.define("bar", [], function() {
	function hello(who) {
		return "Let me introduce: " + who;
	}
	return {
		hello: hello
	};
} );
MyModules.define("foo", ["bar"], function(bar) {
	var hungry = "hippo";
	function awesome() {
		console.log( bar.hello( hungry ).toUpperCase() );
	}
	return {
		awesome: awesome
	};
} );
var bar = MyModules.get("bar");
var foo = myModules.get("foo");
console.log( bar.hello("hippo") );//Let me introdue: hippo
foo.awesome();//LET ME INTRODUCE: HIPPO
```
##关于this
this被自动定义在所有函数的作用域中，它是在运行时进行绑定的，它的上下文取决于函数调用时的各种条件。
###绑定规则
 - 默认
 如果使用严格模式，则不能将全局对象用于默认绑定，因此this会绑定到undefined
 - 隐式
 隐式绑定规则会把函数调用中的this绑定到这个上下文对象。
 ``` javascript
 function foo() {
	 console.log( this.a ）；
}
var obj2 = {
	a: 42,
	foo: foo
};
var obj1 = {
	a: 12,
	obj2: obj2
};
obj1.obj2.foo(); //42
 ```
 隐式绑定的函数会丢失绑定对象
 ``` javascriopt
 function foo() {
	 console.log( this.a );
}
var obj = {
	a: 2,
	foo: foo
};
var bar = obj.foo;
var a = "oops, global";
bar(); //"oops, global"
 ```
 bar 是obj.foo的一个引用，但实际上，它引用的是foo函数本身，因此此时的bar()其实是一个不带任何修饰的函数调用，因此应用了默认绑定。
 - 显式
 call(..)和apply(..)
第一个参数对象是给this准备的。
 - 硬绑定
 ``` javascript
 function bind(fn, obj) {
	 return function() {
		 return fn.apply( obj, arguments );
	};
}
 ```
  
 ES5提供了内置方法Function.prototype.bind
 - new
 对于函数的“构造调用”，会自动执行如下步骤：
	 1.创建一个全新的对象
	 2.这个新对象会被执行[[Prototype]]连接
	 3.这个新对象会绑定到函数调用的this
	 4.如果函数没有返回其他对象，那么new表达式中的函数会自动返回这个新对象。
	 硬绑定>new
	 显示>隐式
	 new>隐式
在javascript中创建一个空对象最简单的方法就是Object.create(null)，和{}很像，但是并不会创建Object.prototype这个委托，所以它比{}更空。
函数的间接引用，调用这个函数里会应用默认绑定规则。
箭头函数的绑定无法被修改。
##对象
引擎会自动把字面量转换成内置对象，所以可以访问属性和方法。null和undefined没有对应的构造形式，它们只有文字形式，而Date只有构造没有文字形式。
在对象中属性名永远都是字符串。
``` javascript
Object.getOwnPropertyDescriptor(myObject, "a");
Object.defineProperty(myObject,"a",{
	value: 2,
	writable: true, //是否可以以修改属性的值
	configurable: true, //只要属性是可配置的，就可以使用defineProperty(..)
	enumerable: true //属性是否会出现在对象的属性枚举中，如for..in循环
} );
```
属性不变性
 - 对象常量 writable:false和able:false就可以创建一个真正的常量属性
 - 禁止扩展 Object.preventExtensions( myObject )
 - 密封 Object.seal(..)调用Object.preventExtensions()+configurable:false
 - 冻结 Object.freeze(..)调用Object.seal()+writable:false
 
 in操作符会检查属性是否在对象及其[[Prototype]]原型链中，hasOwnProperty(..)
 for..of遍历属性值
 
##“类”
javascript中的函数无法真正地复制，所以只能复制对共享函数对象的引用。
``` javascript
function Foo() {
	//...
}
var a = new Foo();
Object.getPrototypeOf( a) === Foo.prototype; //true
```
原型继承常常被视为动态语言版本的类继承。继承意味的复制操作，javascript并不会复制对象属性，相反会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。
a.constructor === Foo意味着a确实有一个指向Foo的.constructor属性，但实际上，.constructor（不可枚举）引用被委托给了Foo.prototype，而Foo.prototype.constrcutor默认指向Foo。
可以渲染序列图：

```sequence
a1->Foo.prototype:
a2->Foo.prototype:
Bar.prototype->Foo.prototype:
b1->Bar.prototype:
b2->Bar.prototype:
```
##原型继承
``` javascript
function Foo(name) {
	this.name = name;
}
Foo.prototype.myName = function() {
	return this.name;
}
function Bar(name,label) {
	Foo.call(this, name);
	this.label = label;
}
//我们创建了一个新的Bar.prototype对象并关联到Foo.prototype
Bar.prototype = Object.create( Foo.prototype );
//注意！这里没有Bar.prototype.constructor了
//如果你需要这个属性的话需要手动修复一下它
Bar.prototype.myLabel = function() {
	return this.label;
};
var a = new Bar("a","obj a");
a.myName(); //"a"
a.myLabel(); //"obj a"
```
``` javascript
function inherits(Child, Parent) {
	var F = function(){};
	F.prototype = Parent.prototype;
	Child.prototype = new F();
	Child.prototype.construction = Child;
}
```
##委托控件对象
``` javascript
var Widget = {
	init: function(width, height){
		this.width = width || 50;
		this.height = height || 50;
		this.$elem = null;
	},
	insert: function($where) {
		if (this.$elem) {
			this.$elem.css( {
				width: this.width + "px",
				height: this.height + "px"
			} ).appendTo( $where );
		}
	}
};
var Button = Object.create( Widget );
Button.setup = function(width, height, label) {
	//委托调用
	this.init( width, height );
	this.label = label || "Default";
	this.$elem = $( "<button>" ).text( this.label );
};
Button.build = function($where) {
	//委托调用
	this.insert( $where );
	this.$elem.click( this.onClick.bind( this ));
};
Button.onClick = function(evt) {
	console.log( "Button'" + this.label + "' clicked!" );
};
$( document ).ready( function() {
	var $body = $( document.body );
	var btn1 = Object.create( Button );
	btn1.setup(125, 30, "Hello");
	var btn2 = Object.create( Button );
	btn2.setup(150, 40, "World");
	btn1.build($body);
	btn2.build($body);
```
