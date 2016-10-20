#javascript高级程序设计——笔记



##基本概念
- **基本数据类型包括Undefined/Null/Boolean/Number和String**
- **无须指定函数的返回值，实际上，未指定返回值的函数返回的是一个特殊的undefined值**

##变量、作用域和内存问题
- **基本类型值在内存中占据固定大小的空间，因此保存在栈内存中**
- **引用类型的值是对象，保存在堆内存中**
- **确定一个值是哪种基本类型用typeof，确定一个值是哪种引用用instanceof**
- **所有变量都存在于一个执行环境当中，这个执行环境决定了变量的生命周期，以及如一部分代码可以访问其中的变量。执行环境有全局执行环境和函数执行环境，每次进入一个新执行环境，都会创建一个用于搜索变量和函数的作用链。**
- **Javascript是一门具有自动垃圾收集机制的编程语言，离开作用域的值被自动标记为可以回收，“标记清除”是目前主流的垃圾收集算法，另一种是“引用记数”，这种算法的思想是跟踪记录所有值被引用的次数。在IE中访问非原生Javascript对象（DOM元素）时，这种算法会因为循环引用导致引用计数永远无法清零**
###Array类型
- **concat()先创建当前数组一个副本，然后接收的参数添加到副本末尾，最后返回**
- **slice()基于当前数组中的一个或多个项创建一个新数组**
- **splice()数组的增删**
- **indexOf()/lastIndexOf()查找项和查找起点位置，比较用的===。支持IE9+**
- **every()=and**
- **filter()返回函数返回为true的项数组**
- **forEach()运行指定函数，无返回**
- **map()返回每次函数调用的结果组成的数组**
- **some()＝||**
- **reduce()/reduceRight(),function(prev,cur,index,array),IE9+**
###Date类型
``` javascript
var now = new Date()//Date.parse()和Date.UTC()
var someDate = new Date(Date.parse("May 25,2004"));
var y2k = new Date(Date.UTC(2000,0)//GMT时间2000年1月1日午夜零时
```
###RegExp类型
- **RegExp类型中需要转义的元字符包括([{\^$|)?*+=.]}**
```  javascript
var pattern1 = /[bc]at/i;
var pattern2 = new RegExp("[bc]at","i");
```
**RegExp构造函数的两个参数都是字符串，在某些情况下要对字符进行双重转义**
字面量模式   |    等价的字符串
-----------------|-----------------
/\\ [bc\\]at/|"\\\\[bc\\\\]at"
/\w\\\\hello\\\\123/|"\\\\w\\\\\\\\\hello\\\\\\\\123"
- **RegExp实例属性:global是否设置了g标志，ignoreCase是否设置了i标志，lastIndex表示开始搜索下一个匹配项的字符位置，multiline是否设置m 标志，source正则表达式的字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回。正则表达式字面量始终会共享同一个RegExp实例，所以在循环时需要注意，会从上一次搜索位置开始匹配。**
- **exec()专门为捕获组而设计，没有匹配返回null，返回数组包含两个额外属性:index和input。其中index表示匹配项在字符串中的位置，input表示应用正则表达式的字符串。数组中，第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串（如果没有则数组只有一项）。**
- **test()匹配返回true否则false**
###Function类型
-**函数内部对象arguments和this,arguments除了保存函数参数，还有一个属性callee指向这个arguments对象的函数**
``` javascript
function factorial(num){
	if (num <= 1) {
		return 1;
	} else {
	return num * arguments.callee(num-1);
	}
}//通过arguments.callee消除函数名的紧密耦合 
```
-**this引用的是函数据以执行的环境对象——或者也可以说是this值。**
-**ECMAScript5规范化了另一个函数对象属性：caller。除了Opera早期版本不支持。这个属性中保存调用当前函数的函数的引用，如果在全局作用域中调用，则caller的值为null。**
-**length表示希望接收的命名参数的个数。**
-**prototype是保存引用类型所有实例方法的真正所在。诸如toString()和valueOf()等方法实际上都保存在prototype名下，只不过是通过各自对象的实例访问罢了。该属性不可枚举。**
-**每个函数都包含两个非继承方法：apply()和call()。作用是在特定的作用域调用函数，实际上等于设置函数体内this对象的值。区别在于传入第二个参数是arguments对象还是具体参数。**
``` javascript
window.color = "red";
var o = { color: "blue" };
function sayColor(){
	alert(this.color);
｝
sayColor();//red
sayColor.call(this);
sayColor.call(window);
sayColor.call(o);//blue
var objectSayColor = sayColor.bind(o);//即使在全局作用域中调用该函数，也会看"blue",IE9+支持
```
###基本包装类型
Boolean、Number、String
``` javascript
var s1 = "some text";
var s2 = s1.substring(2);

var s1 = new String("some text");
var s2 = s1.substring(2);
s1 = null;//自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁。
var value = "25";
var number = Number(value);//转型函数
var object = new Number(value);//构造函数
var num = 10.005;
alert(num.toFixed(2)); //"10.01"
alert(num.toExponential(1));//"1.0e+1"
var num = 99;
alert(num.toPrecision(1));//"1e+2"
```
- **字符串操作方法**
concat()/slice()/substr()/substring()都不会修改字符串本身的值，只是返回一个基本类型的字符串，对原始字符串没有任何影响。
indexOf()/lastIndexOf()，没有找到返回-1
trim()返回字符串副本，IE9+支持
match(正则/RegExp)同exec()
search(正则/RegExp)无匹配返回-1
replace(RegExp/字符串，字符串/函数)
split(RegExp/字符串，length)
charCodeAt()/formCharCode()
###单体内置对象
- **Global对象**
encodeURI()/encodeURIComponent()对URI(Uniform Resource Identifiers,能用资源标识符)进行编码，以便发送给浏览器。区别在于前者不会对本身属于URI的特殊字符进行编码，例如冒号、正斜杠、问号和井号；后者会对所有非标准字符进行编码。替代只能编码ASCII字符的escape()
- **eval()参数当然实际的ECMAScript语句来解析，然后把执行结果插入到原来位置。在eval()中创建的任何变量和函数都不会被提升。在严格模式下无法访问 eval中创建的变量和函数。**
- **undefined/NaN/Infinity以及所有原生引用类型的构造函数都是Global的属性**
- **Math.min()/max()/ceil()/floor()/round()/random()**
2-10整数间的随机数，包括2跟10
``` javascript
var num = Math.floor(Math.random()*9+2);
function selectFrom(lowerValue, upperValue) {
	var choices = upperValue - lowerValue + 1；
	return Math.floor(Math.random()*choices+lowerValue);
}
```

-------------------

##面向对象的程序设计
对象是散列表：无非就是一组名值对，其中值可以是数据或函数。

- 属性类型
	- 数据属性
		[[Configurable]]能否delete属性从而重新定义属性，能否修改属性描述，默认true
		[[Enumerable]]能否通过for-in循环，默认true
		[[Writable]]能否修改属性值，默认true
		[[Value]]包含这个属性的数据值，默认undefined
	- 访问属性
		同上前2
		[[Get]]读取属性时调用的函数，默认undefined
		[[Set]]写入属性时调用的函数，默认undefined
		Object.defineProperty(),configurable设为false后再调用该方法就会抛出错误了，该方法调用时，如果不指定默认都是false。访问属性不能直接定义，必须使用Object.defineProperty()来定义。
		```  javascript
		var book = {
			_year:2004,
			edition:1
		};
		Object.defineProperty(book,"year",{
			get:function(){
				return this._year;
			},
			set:function(newValue){
				if(newValue > 2004) {
					this._year = newValue;
					this.edition += newValue - 2004;
				}
			});
		var descriptor = Object.getOwnPropertyDescriptor(book,"year");
		alert(descriptor.value);//undefined
		alert(descriptor.enumerable);//false
		alert(typeof descriptor.get);//"function"
		
		book.year = 2005;
		alert(book.edition);//2,使用访问器属性一般是设置一个属性会导致其他属性发生变化
		var descriptor = Object.getOwnPropertyDescriptor(book,"_year");
		alert(descriptor.value);//2005
		alert(descriptor.configurable);//false
		alert(typeof descriptor.get);//"undefined"
		```
		不一定要同时指定getter和setter。在非严格模式下会返回undefined或被忽略，在严格模式下会抛出错误。ECMAScript 5又定义了Object.definedProperties()。
		IE9+(IE8部分支持）、Firefox 4+、Safari 5+、Opera 12+、Chrome
###创建对象

- 工厂模式
没法解决对象识别的问题，即对象类型问题。
- 构造函数模式
	``` javascript
	function Person(name, age, job){
		this.name = name;
		this.age = age;
		this.job = job;
		this.sayName = function(){
			alert(this.name);
		}
	}
	var person = new Person("Nicholas",28,"software engineer");
	```

	- 创建一个新对象；
	- 将构造函数的作用域赋给新对象；
	- 执行构造函数中的代码；
	- 返回新对象。
构造函数与其他函数的唯一区别在于调用它们的方式不同。任何函数，只要通过new来调用。
``` javascript
//当作构造函数使用
var person = new Person("Nicholas", 28,"software engineer");
person.sayName();
//作为普通函数调用
Person("Greg",23,"Doctor");
window.sayName();//"Greg"
//在另一个对象的作用域中调用
var o = new Object();
Person.call(o,"Kristen",21,"Nurse");
o.sayName();//"Kristen"
```
但此模式还是有缺点就是不同实例上的同名函数是不相等的，然而创建两个完成同样任务的Function实例是没有必要的；况且有this对象在，根本不用在执行代码前就把函数绑定到特定对象上面。因此可以像下面一样解决此问题。
``` javascript
function Person(name, age, job) {
	 this.name = name;
	 this.age = age;
	 this.job = job;
	 this.sayName = sayName;
}
function sayName() {
	alert(this.name);
}//此方法也存在问题，全局函数只被某个对象调用，而且会产生很多这样的全局函数
```
- 原型模式
	![原型图](http://img.blog.csdn.net/20160922204629872)
	创建的每个函数都有一个prototype属性，这个属性是一个指针，指向一个对象，该对象的用途是包含可以由特定类型的所有实例共享的属性和方法，prototype就是通过调用构造函数而创建的那个对象实例的原型对象。
	
	``` javascript
	alert(Person.prototype.isPrototypeOf(person1));//true,这里用的原型对象的isPrototypeOf()方法
	alert(Object.getPrototypeOf(person1 == Person.prototype);//ECMAScript5增加了Object.getPrototypeOf()返回对象的原型，IE9+/Firefox 3.5+/Safari 5+/Opera 12+和chrome
	```
	可以通过对象实例访问保存在原型中的值，但不能重写原型中的值，可以屏蔽不可以重写。想要取回原型中的值用delete person1.name;
``` javascript
alert(person1.hasOwnProperty("name"));//false
alert("name" in person1);//true,in操作符会在通过对象能够访问给定属性时返回true,无论该属性存在于实例中还是原型中。for-in循环是对象访问的、可枚举的属性。
function hasPrototypeProperty(object, name) {
	return !object.hasOwnProperty(name) && (name in object);
}//确定属性是否为原型属性
function Person() {
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
	alert(this.name);
};
var keys = Object.keys(Person.prototype);
alert(keys);//"name,age,job,sayName"

var p1 = new Person();
p1.name = "Rob";
p1.age = 31;
var p1keys = Object.keys(p1);
alert(p1keys);//"name,age" Object.keys()只包含可枚举的实例属性
var keys = Object.getOwnPropertyNames(Person.prototype);
alert(keys);//"constructor,name,age,job,sayName" Object.getOwnPropertyNames()可以得到所有实例属性，无论它是否可枚举
```
Object.keys和Object.getOwnPropertyNames都可用来替代fon-in循环，IE9+、Firefox 4+、Safari 5+、Opera 12+和Chrome支持。
``` javascript
function Person(){
}
var friend = new Person();
Person.prototype = {
	name:"Nicholas",
	age:29,
	job:"Software Engineer",
	sayName:function(){
		alert(this.name);
	}
};//Person.prototype设置为一个以对象字面量形式创建的新对象。最新结果相同，但constructor属性不再指向Person了。每创建一个函数，就会同时创建它的prototype对象，这个对象会自动获得constructor属性。这里重写了默认的prototype对象，该新的对象继承了原型对象(Object.prototype)的constructor属性（指向Object构造函数)，因此这时Person.prototype.constructor属性不再指向Person函数而是指向Object构造函数。
Person.prototype = {
	constructor:Person,
	name:"Nicholas",
	age:29,
	job:"Software Engineer",
	sayName:function(){
		alert(this.name);
	}
};//该种方法会导致重设的constructor属性的[[Enumerable]]特性被设置为true,默认是false。
Object.defineProperty(Person.prototype,"constructor",{
	enumberable:false,
	value:Person
});
friend.sayName();//error,因为friend指向的原型中不包含以该名字命名的属性。重写原型对象切断了现有原型与任何之前已经存在的对象实例之间的联系；它们引用的仍然是最初的原型。
```
	尽管可以随时为原型添加属性和方法，并且修改能够立即在所有对象实例中反映出来，但如果重写整个原型对象，情况就不一样了。调用构造函数时会为实例添加一个指向最初原型的[[Prototype]]指针，而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系。
	原型对象的缺点，省略了为构造函数传递初始化参数这一环节还有就是共享所导致，当包含引用类型值的属性时会有共享问题。
	
- 组合使用构造函数模式和原型模式
构造函数模式用于定义实例属性，而原型模式用于定义方法和共享属性，同时又支持向构造函数传递参数。
``` javascript
function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.friends = ["Shelby", "Court"];
}
Person.prototype = {
	constructor:Person,
	sayName:function(){
		alert(this.name);
	}
}
var person1 = new Person("Nicholas",29,"Software Engineer");
var person2 = new Person("Greg",27,"Doctor");
person1.friends.push("Van");
alert(person1.friends);
alert(person2.friends);//"Shelby,Count"
alert(person1.friends === person2.friends);//false
alert(person1.sayName === person2.sayName);//true
```
- 动态原型模式
``` javascript
function Person(name, age, job) {
	this.name = name;
	this.age = age;
	this.job = job;
	if(typeof this.sayName != "function"){
		Person.prototype.sayName = function(){
			alert(this.name);
		};
	}
}
```
- 寄生构造函数模式
``` javascript
function SpecialArray() {
	var values = new Array();
	values.push.apply(values, arguments);
	values.toPipedString = function() {
		return this.join("|");
	};
	return values;
}
```
	该种模式下返回的对象与构造函数或者与构造函数的原型属性之间没有关系，既构造函数返回的对象与在构造函数外部创建的对象没什么不同，因此不能依赖instanceof操作符来确定对象类型。
	
- 稳妥构造函数模式
	稳妥对象指没有公共属性，而且其方法也不引用this的对象。
	``` javascript
	function Person(name, age, job) {
		var o = new Object();
		//可以在这里定义私有变量和函数
		//添加方法
		o.sayName = function() {
			alert(name);
		};
		//返回对象
		return o;
	}//除了使用sayName()方法外，没有其他办法访问name值(传入到构造函数中的原始数据)
	var friend = Person("Nicholas",29,"Software Enginner");
	friend.sayName();
	```
	稳妥构造函数模式提供的这种安全性适合在某些安全执行环境。与寄生构造函数模式类似，该模式创建的对象与构造函数之间没什么关系。
## 继承
每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。
``` javascript
function SuperType() {
	this.property = true;
}
SuperType.prototype.getSuperValue = function() {
	return this.property;
};
function SubType() {
	this.subproperty = false;
}
//继承了Supertype
SubType.prototype = new SuperType();//property实例属性位于SubType.prototype中，SubType.prototype.constructor = SuperType
SubType.prototype.getSubValue = function() {
	return this.subproperty;
};
var instance = new SubType();
alert(instance.getSuperValue());//true
```
调用instance.getSuperValue()会经历三个搜索步骤：1)搜索实例2)搜索SubType.prototype；3）搜索SuperType.prototype，最后一步才会找到
实现的本质是重写原型对象，代之以一个新类型的实例。原来存在于SuperType的实例中的所有属性和方法，现在也存在于SubType.prototype中了。
原型链的问题：包含引用类型值属性的原型会导致所有实例共享该属性；在创建子类型的实例时，不能向超类型的构造函数传递参数，所以实践中很少会单独使用原型链。

**借用构造函数**
在子类型构造函数的内部调用超类型构造函数。解决了原型链的问题。
``` javascript
function SuperType() {
	this.colors = ["red", "green"];
}
function SubType() {
	//继承了SuperType
	SuperType.call(this);
}
var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);//"red,blue,green,black"
var instance2 = new SubType();
alert(instance2.colors);//"red,blue,green"
```
虽然如此还是有问题：无法避免方法都在构造函数上定义，就无法函数复用了。在超类的原型中定义的方法对子类而言是不可见的，所以借用构造模式也很少用。

**组合模式**
原型链和借用构造函数组合在一块，使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。
``` javascript
function SuperType(name) {
	this.name = name;
	this.colors = ["red", "blue","green"];
}
SuperType.prototype.sayName = function() {
	alert(this.name);
};
function SubType(name, age) {
	//继承属性
	SuperType.call(this, name);//第二次调用SuperType构造函数
	this.age = age;
}
//继承方法
SubType.prototype = new SuperType();//第一次调用Supertype构造函数
SubType.prototype.sayAge = function() {
	alert(this.age);
};
var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors);//"red,blue,green,black"
instance1.sayName();//"Nicholas"
instance1.sayAge();//29
var instance2 = new SubType("Greg",27);
alert(instance2.colors);//"red,blue,green"
instance2.sayName();//"Greg"
instance2.sayAge();//27
```
![这里写图片描述](http://img.blog.csdn.net/20160926202454202)
**原型式继承**
``` javascript
function object(o) {
	function F(){};
	F.prototype = o;
	return new F();
}
```
借助原型可以基于已有的对象创建新对象，不必因此创建自定义类型。ECMAScript5通过新增Object.create()方法规范化了原型式继承。接收参数：一个用作新对象原型的对象和（可选）一个为新对象定义额外属性的对象。一个参数时等同于上面的object()，在没有必要创建构造函数时使用。

**寄生式继承**
与寄生构造函数和工厂模式类似，创建一个仅用于封闭继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作一样返回对象。
``` javascript
function createAnther(original) {
	var clone = object(original);//通过调用函数创建一个新对象
	clone.sayHi = function(){
		//以某种方式来增强这个对象
		alert("hi");
	};
	return clone;//返回这个对象
}
```
**寄生组合式继承**
组合继承的不足是无论什么情况下都会调用两次超类型构造函数（创建子类型原型的时候和子类型构造函数内部）。寄生组合式继承解决此问题，思路：使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。基本模式如下：
``` javascript
function inheritPrototype(subType, superType) {
	var prototype = object(superType.prototype);//创建超类型原型的一个副本
	prototype.constructor = subType;//增强对象，为创建的副本添加constructor属性
	subType.prototype = prototype;//指定对象，将创建的副本赋值给子类型的原型
｝
function inherits(Child, Parent) {
    var F = function () {};
    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
}
```
将前面组合继承中的语句SubType.prototype = new SuperType();替换成inheritPrototype(SubType,SuperType);效率体现在它只调用了一次SuperType构造函数，并且因此避免了在SubType.prototype上面创建不必要的、多余的属性。与此同时，原型链还能保持不变。
寄生组合式继承是引用类型最理想的继承范式。
![引用廖雪峰的实例](http://img.blog.csdn.net/20160929105234418)

**总结**

- 创建对象
	- 工厂模式：使用简单的函数创建对象，为对象添加属性和方法，然后返回对象。
	- 构造函数模式：可以创建自定义引用类型，像创建内置对象实例一样使用new操作符。缺点是每个成员都无法得到复用，包括函数。
	- 原型模式：使用构造函数的prototype属性来指定那些应该共享的属性和方法。组合使用构造函数模式和原型模式时，使用构造函数定义实例属性，而使用原型定义共享的属性和方法。
	js主要通过原型链实现继承，通过将一个类型的实例赋值给另一个构造函数的原型来实现，子类型能够访问超类型的所有属性和方法。问题在于对象实例共享所有继承的属性和方法。解决方法是借用构造函数，即在子类型构造函数的内部调用超类型构造函数。使用最多的是继承模式是组合继承，使用原型链继承共享的属性和方法，通过借用构造函数继承实例属性。
	- 原型式继承：执行对给定对象的浅复制，复制得到的副本还可以得到进一步改造。
	- 寄生式继承：基于某个对象或某些信息创建一个对象，然后增强对象，最后返回对象。为了解决组合继承模式由于多次调用超类构造函数而导致低效率问题，可以将此模式与组合继承一起使用。
	- 寄生组合式继承：集寄生式继承和组合继承的优点与一身。
# 函数表达式
当某个函数被调用时，会创建一个执行环境及相应的作用域链。

执行环境（execution context）定义了变量或函数有权访问的其他数据，决定了它们各自的行为。每个函数都有自己的执行环境。当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。而在函数执行之后，栈将其环境弹出，把控制权返回给之前的执行环境。当代码在一个环境中执行时，会创建变量对象的一个作用域链（scope chain）。作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。作用域链本质上是一个指向变量对象的指针列表，它只引用但不实际包含变量对象。

每个环境都可以向上搜索作用域链，以查询变量和函数名；但任何环境都不能通过向下搜索作用域链而进入另一个执行环境。但是有其他办法来延长作用域链。有些语句可以在作用域链的前端临时增加一个变量对象，该变量对象会在代码执行后被移除。当执行流进入下列两种语句时，作用域链就会得到加长：

try-catch 语句的 catch 块
with 语句
这两个语句都会在作用域链的前端添加一个变量对象。对 with 语句来说，会将指定的对象添加到作用域链中。对 catch 语句来说，会创建一个新的变量对象，其中包含的是被抛出的错误对象的声明。

在 IE8 及之前版本的 JavaScript 实现中，存在一个与标准不一致的地方，即在 catch  语句中捕获的错误对象会被添加到执行环境的变量对象，而不是  catch  语句的变量对象中。换句话说，即使是在  catch  块的外部也可以访问到错误对象。 IE9 修复了这个问题。

闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见方式，就是在一个函数内部创建另一个函数。

无论什么时候在函数中访问一个变量时，就会从作用域链中搜索具有相应名字的变量。一般来讲，当函数执行完毕后，局部活动对象就会被销毁，内存中仅保存全局作用域（全局执行环境的变量对象）。但是，闭包的情况有所不同，因为在另一个函数内部定义的函数会将包含函数（即外部函数）的活动对象添加到它的作用域链中，参考下面的代码：
``` javascript
function createComparisonFunction(propertyName) {
	return function(object1, object2){
		var value1 = object1[propertyName];
		var value2 = object2[propertyName];
		if (value1 < value2){
			return -1;
		} else if (value1 > value2){
			return 1;
		} else {
			return 0;
		}
	};
}
var compare = createComparisonFunction("name");//当匿名函数从createComparisonFunction()中被返回后，它的作用域被初始化为包含createComparisonFunction()函数的活动对象和全局变量对象。只有在compare销毁后createComparisonFunction()函数的活动对象才能被销毁
var result = compare({ name: "Nicholas" }, { name: "Greg" });
compare = null;  //解除对匿名函数的引用（以便释放内存）
```
![这里写图片描述](http://img.blog.csdn.net/20160927112602392)
当createComparisonFunction()函数在执行完毕后，其活动对象也不会被销毁，因为匿名函数的作用域链仍然在引用这个活动对象（可以看成是暂时的全局活动对象）。直到匿名函数被销毁后， createComparisonFunction()的活动对象才会被销毁。
作用域链的这种配置机制引出了一个值得注意的副作用，即闭包只能取得包含函数中任何变量的最后一个值。因为闭包所保存的是整个变量对象，而不是某个特殊的变量。
``` javascript
function createFunctions(){
	var result = new Array();
	for (var i=0; i < 10; i++){
		result[i] = function(num){
			return function() {
				return num;
			};
		}(i);//函数参数是按值传递的，变量i的当前值复制给参数num
	}
	return result;
}
```
this对象是在运行时基于函数的执行环境绑定的：在全局函数中，this等于window，而当函数被作为某个对象的方法调用时，this等于那个对象。匿名函数的执行环境具有全局性，this指向window。普通函数被调用时，其他活动对象都会自动取得两个特殊变量this和arguments。内部函数搜索这两个变量时只会搜索到其活动对象为止。
``` javascritp
function assignHandler(){
	var element = document.getElementById("someElement");
	var id = element.id;
	element.onclick = function(){
		alert(id);
	};
	element = null;//在IE9之前对JScript对象和COM对象使用不同的垃圾收集，闭包在这会导致匿名函数保存了一个对assignHandler()的活动对象(包含element)的引用，导致无法减少element的引用数，只要匿名函数存在，element的引用数至少为1。这里解除引用。
}
```
**模仿块级作用域**
``` javascript
(function(){
	//这里是块级作用域
})();
```
无论什么地方，只要临时需要一些变量，就可以使用私有作用域。
``` javascript
function outputNumbers(count) {
	(function() {
		for(var i=0; i<count; i++) {
			alert(i);
		}
	})();//这种做法可以减少闭包占用内存的问题，因为没有指向匿名函数的引用（有引用就有作用域链）。只要函数执行完毕，就可以立即销毁其作用域了
	alert(i);//error
}
```
这种技术经常在全局作用域中被用在函数外部，从而限制向全局作用域中添加过多的变量和函数。

**私有变量**
不能在函数外部访问的变量叫私有变量。
特权方法：有权访问私有变量（任何函数中定义的变量）和私有函数的公有方法。在函数内部创建一个闭包，闭包通过自己的作用链访问私有变量。
``` javascript
function MyObject() {
	//私有变量和私有函数
	var privateVariable = 10;
	function privateFunction() {
		return false;
	}
	//特权方法
	this.publicMethod = function() {
		privateVariable++;
		return privateFunction();
	};
}
//隐藏不应该被直接修改的数据
function Person(name) {
	this.getName = function() {
		return name;
	};
	this.setName = function(value) {
		 name = value;
	};
}//缺点是必须通过构造函数来实现，而构造函数模式的缺点是对每个实例都会创建同样一组新方法，可以使用静态私有变量来避免
```
**静态私有变量**
``` javascript
(function() {
	//私有变量和私有函数
	var privateVariable = 10;
	function privateFunction() {
		return false;
	}
	//构造函数
	MyObject = function() {
	};//注意这里是全局变量
	//公有/特权方法
	MyObject.prototype.publicMethod = function() {
		privateVariable++;//变成一个静态、由所有实例共享的属性
		return privateFunction();
	};
})();//不足在于作用域链查找会多一个层次
var o = new MyObject();
alert(o.publicMethod());//false
```
调用publicMethod或新建一个MyObject实例都会赋予privateVariable一个新的值。结果就是所有实例都会返回相同的值。每个实例都没有自己的私有变量。
该模式用于为自定义类型创建私有变量和特权方法的。
**模块模式(为单例创建私有变量和特权方法)**
``` javascript
var singleton = function() {
	//私有变量和私有函数
	var privateVariable = 10;
	function privateFunction() {
		return false;
	}
	//特权/公有方法和属性
	return {
		publicProperty:true,
		publicMethod:function() {
			privateVariable++;
			return privateFunction();
		}
	};//这个对象字面量定义的是单例的公共接口
}();
```
这种模式在需要对单例（JavaScript是以对象字面量的方式来创建单例对象的）进行某些初始化，同时又需要维护其私有变量时是非常有用的。

# BOM

- 取得页面视口大小
``` javascript
var pageWidth = window.innerWidth,
	pageHeight = window.innerHeight;
if (typeof pageWidth != 'number'){
	if(document.compatMode == 'CSS1Compat') {
		pageWidth = document.documentElement.clientWidth;
		pageHeight = document.documentElement.clientHeight;
	} else {
		pageWidth = document.body.clientWidth;
		pageHeight = document.body.clientHeight;
	｝
｝
``` 
- window.open(URL, target, string, boolean)返回指向新窗口的引用，创建的窗口允许我们针对窗口调整大小和移动位置、关闭等。opener指向打开它的原始窗口。有很安全限制，如不允许关闭地址栏、默认情况下不允许移动弹出窗口或调整其大小。此外，有的浏览器只根据用户操作来创建弹出窗口。在页面尚未加载完成时调用window.open()的语句根本不会执行，换句说就是只能通过单击或者击键来打开弹出窗口。对于不是用户有意打开的弹出窗口，Chrome采取的不是简单的屏蔽而是只显示它们的标题栏，放在浏览器窗口的右下角。
``` javascript
var blocked = false;
try {
	var wroxWin = window.open("http://www.wrox.com","_blank");
	if(wroxWin == null) {
		blocked = true;
	}
} catch (ex) {
	blocked = true;
}
if (blocked) {
	alert("Thepopup was blocked!");
}
```
- 超时调用和间歇调用
在使用超时调用时，没必要跟踪超时调用ID，因为每次执行完代码之后，如果不再设置另一次超时调用，调用就会自行停止。一般认为使用超时调用来模拟间歇调用是一种最佳模式。后一个间歇调用可能会在前一个间歇调用结束之前启动。
- location对象
既是window的属性也是document的属性；
属性名|例子|说明
--------|------------|-----------------
hash|"#contents"|散列#号后面的跟的内容，没有则返回空
host|"www.wrox.com:80"|
hostname|"www.wrox.com"|
href|"http://ww.wrox.com"|
pathname|"/WileyCDA/"|
port|"8080"|
protocol|"http:"或"https"|
search|"?q=javascript"|
``` javascript
function getQueryStringArgs(){
	//取得查询字符串并去掉开头的问号
	var qs = (location.search.length > 0 ?location.search.substring(1):""),
	//保存数据对象
	args = {},
	//取得每一项
	items = qs.length?qs.split("&"):[],
	item = null,
	name = null,
	value = null,
	i = 0,
	len = items.length;
	//逐个将每一项添加到args对象中
	for(i=0;i<len;i++) {
		item = items[i].split("=");
		name = decodeURIComponent(item[0]);
		value = decodeURIComponent(item[1]);
		if(name.length) {
			 args[name] = value;
		}
	}
	return args;
}
//每次修改location属性（除hash）页面都会以新的URL重新加载。replace("")重定向后“后退”按钮处于禁用状态
location.assign("");
window.location = "";
location.href = "";
location.reload();//重新加载有可能从缓存中加载
location.reload(true);//重新加载（从服务器重新加载）
```
#客户端检测

- 能力检测
在可能的情况下，尽量使用typeof进行能力检测。
``` javascript
function hasCreateElement() {
	return typeof document.createElement == "function";
}//IE8及之前版本，这个函数会返回false，因为返回的是"object",IE及更早版本中的对象是通过COM而非JScript实现的。
function isHostMethod(object, property) {
	var t = typeof object[property];
	return t=='function'||(!!(t=='object'&&object[property]))||t=='unknown';
}//IE对typeof xhr.open会返回"unknown"
```
- 怪癖检测
``` javascript
var hasDontEnumQuirk = function() {
	var o = {toString:function(){}};
	for (var prop in o) {
		if(prop == "toString") {
			 return false;
		}
	}
	return true;
}();
```
- 用户代理检测
优先级排在能力检测和怪癖检测之后。确切知道浏览器的名字和版本号不如确切知道它使用的是什么呈现引擎。主要有五大呈现引擎：IE/Gecko/WebKit/KHTML和Opera。
``` javascript
var client = function() {
	var engine = {
		ie:0,
		gecko:0,
		webkit:0,
		khtml:0,
		opera:0,
		ver:null
	};
	return {
		engine:engine
	};
}();
var ua = navigator.userAgent;
if(window.opera) {
	engine.ver = window.opera.version();
	engine.opera = parseFloat(engine.ver);
} else if(/AppleWebKit\/(\S+)/.test(ua)) {
	engine.ver = RegExp["$1"];
	engine.webkit = parseFloat(engine.ver);
} else if(/KHTML\/(\S+)/.test(ua)) {
	engine.ver = RegExp["$1"];
	engine.khtml = parseFloat(engine.ver);
} else if (/rv:([^\)]+)\) Gecko\/\d{8}/.test(ua)) {
	engine.ver = RegExp["$1"];
	engine.gecko = parseFloat(engine.ver);
} else if(/MSIE ([^;]+)/.test(ua)) {
	engine.ver = RegExp["$1"];
	engine.ie = parseFloat(engine.ver);
}
```
# 事件
DOM2级事件规定的事件流包括三个阶段：事件捕获阶段（从外到里）、处于目标阶段和事件冒泡阶段。
事件特性的值是javascript，因此不能在其中使用未经转义的HTML语法字符。
当前元素是表单输入元素，作用域中还会包含访问表单元素的入口
``` javascript
function() {
	with(document) {
		with(this.form) {
			with(this) {
				//元素属性值
			}
		}
	}
}//在HTML中指定事件处理程序会有时差问题，还有扩展事件处理程序的作用域链在不同浏览器中会导致不同结果
//DOM0级事件处理程序
btn.onclick = function() {
	alert("Clicked");
}
//DOM2级事件处理程序IE9/Firefox/Safri/Chrome/Opera
var EventUtil = {
	addHandler:function(element, type, handler) {
		if(element.addEventListener) {
			element.addEventListener(type, handle, false);//true捕获阶段，false冒泡阶段
		} else if(element.attachEvent) {
			element.attachEvent("on"+type, handler);
		} else {
			element["on" + type] = handler;
		},
	removeHandler:function(element, type, handler) {
		if(element.removeEventListener) {
			element.removeEventListener(type, handle, false);//true捕获阶段，false冒泡阶段
		} else if(element.detachEvent) {
			element.deachEvent("on"+type, handler);
		} else {
			element["on" + type] = null;
		}，
	getEvent:function(event) {
		return event?event:window.event;
	},
	getTarget:function(event) {
		return event.target||event.srcElement;
	},
	preventDefault:function(event) {
		if(event.preventDefault) {
			event.preventDefault();
		} else {
			event.returnValue = false;
		}
	},
	stopPropagation:function(event) {
		if(event.stopPropagation) {
			event.stopPropagation();
		} else {
			 event.cancelBubble = true;
		}
	}
};
EventUtil.addHandler(window, "load", function() {
	var image = new Image();//通过DOM0级的Image对象实现图片的预加载，只不过无法将其添加到DOM树中
	EventUtil.addHandler(image, "load", function(event){
		alert("Image loaded!");
	});
	image.src = "smile.gif";
	var img = document.createElement("img");
	EventUtil.addHandler(img,"load",function(event) {
		event = EventUtil.getEvent(event);
		alert(EventUtil.getTarget(event).src);
});

```
- 事件对象
所有浏览器都支持event对象，但支持方式不同。
DOM中的事件对象，this始终等于currentTarget，而target则只包含事件的实际目标。
# 高级技巧
- 安全类型检测
javascript内置的类型检测并非完全可靠。
var isArray = value instanceof Array;代码要返回true，value必须是一个数组，而且还必须与Array构造函数在同个全局作用域中，因为Array是window的属性，如果value是在另外框架中定义的数组，那么代码返回为false。
``` javascript
function isArray(value) {
	return Object.prototype.toString.call(value) == "[object Array]";
}
```
- 作用域安全构造函数
``` javascriopt
function Person(name, age, job) {
	if(this instanceof Person) {
		this.name = name;
		this.age = age;
		this.job = job;
	} else {
		return new Person(name, age, job);
	}
}
```
- 惰性载入函数
惰性载入表示函数执行的分支仅会发生一次。
``` javascript 
function createXHR() {
	if(typeof XMLHttpRequest != "undefined") {
		createXHR = function() {
			return new XMLHttpRequest();
		};
	} else if (typeof ActiveXObject != "undefined") {
		createXHR = function() {
			if(typeof arguments.callee.activeXString != "string") {
				var versions=["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0","MSXML2.XMLHttp"],i,len;
				for(i=0,len=versions.length;i<len;i++) {
					try {
						new ActiveXObject(versions[i]);
						arguments.callee.activeXString = versions[i];
						break;
					} catch (ex) {
					}
				}
			}
			return new ActiveXObject(arguments.callee.activeXString);
		};
	} else {
		createXHR = function() {
			throw new Error("No XHR object available.");
		};
	}
	return createXHR();
}
var createXHR = (function() {
	//在声明函数时就指定适当的函数。
})();
```
- 函数绑定
创建一个函数，可在特定的this环境中以指定参数调用另一个函数。
``` javascript
function bind(fn, context) {
	return function() {
		return fn.apply(context, arguments);
	};
}
```
支持原生的bind()方法的浏览器有ie9+/Firefox 4+/Chrome

- 函数柯里化
用于创建已经设置好了一个或多个参数的函数。使用一个闭包返回一个函数。
``` javascript
function curry(fn) {
	var args = Array.prototype.slice.call(arguments,1);//5
	return function() {
		var innerArgs = Array.prototype.slice.call(arguments);
		var finalArgs = args.concat(innerArgs);//3
		return fn.apply(null,finalArgs);
	}
}//将被返回函数里的参数进行排序，fn是要被柯里化的函数。
function add(num1, num2) {
	return num1 + num2;
}
var curriedAdd = curry(add, 5);
alert(curriedAdd(3));//8

//作为函数绑定的一部分
function bind(fn, context) {
	var args = Array.prototype.slice(arguments, 2);
	return function() {
		var innerArgs = Array.prototype.slice(arguments);
		var finalArgs = args.concat(innerArgs);
		return fn.applay(context, finalArgs);
	};
}
var handler = {
	message:"Event handled",
	handleClick:function(name, event) {
		alert("this.message + ":" + name + ":" +event.type);
	}
};
var btn = document.getElementById("my-btn");
EventUtil.addHandler(btn, "click", bind(handler.handleClick, handler, "my-btn"));
```

- 防篡改对象
ECMAScript 5定义了防篡改对象，一旦设置了防篡改就无法撤消。
``` javascript
var person = {name:"Nicholas"};

Object.preventExtensions(person);
person.age = 29;
alert(person.age);//undefined
alert(Object.isExtensible(person));//false

Object.seal(person);//密封对象，不可扩展，而且已有成员的[[Configurable]]特性将被设置为false
person.age = 29;
alert(person.age);//undefined
delete person.name;
alert(person.name);//"Nicholas"
alert(Object.isExtensible(person));//false
alert(Object.isSealed(person));//true

Object.freeze(person);//最严格的冻结对象，既不可扩展又是密封的，[[Writable]]设置为false。如果定义[[Set]]访问器属性仍然是可写的。对于js库作者来说冻结对象是很有用的。
Object.isFrozen(person);//true
```

- 高级定时器
在5处创建时间间隔为200的定时器，在205处添加到队列中，但是onclick事件处理函数处理到300才开始执行每个定时器代码，在405处又给队列添加了一个定时器任务，在605处由于队列里已经有一个定时器任务了所以不会被添加到队列中。结果在5处添加的定时器任务一执行完，405处的定时器任务立马开始执行跳过了一个时间间隔。
``` javascript
setTimeout(function() {
	setTimeout(arguments.callee, interval);
}, interval);
```

# 离线应用与客户端存储
支持离线Web应用开发是HTML5的一个重点。所谓离线Web应用就是在设备不能上网的情况下仍然可以运行的应用。

- 离线检测
``` javascript
if (navigator.onLine) {
	//正常工作
｝else {
	//执行离线状态时的任务
}
EventUtil.addHandler(window, "online", function(){
	alert("Online");
});
EventUtil.addHnadler(window, "offline", function() {
	alert("Offline");
});
//IE6+(只支持属性）、Firefox 3/Safari4/Opera 10.6/Chrome/iOS 3.2版Safari/Android版WebKit
```
- 应用缓存
appcache，从浏览器缓存中分出来一块缓存区。想要在这个缓存中保存数据，可以使用一个描述文件：
``` text 
CACHE MANIFEST
#Comment
file.js
file.css
```
``` html
<html manifest="/offline.manifest">
```
文件的MIME类型必须是text/cache-manifest，扩展名以前用manifest，现在用aapcache，applicationCache对象控制应用缓存。

- 数据存储
	- Cookie 限制个数和长度，信息构成：名称、值、域、路径、失效时间、安全标志
``` javascript
var CookieUtil = {
	get: function(name) {
		var cookieName = encodeURIComponent(name) + "=",
			cookieStart = document.cookie.indexOf(cookieName),
			cookieValue = null;
		if(cookieStart > -1) {
			var cookieEnd = document.cookie.indexOf(";", cookieStart);
			if(cookieEnd == -1) {
				cookieEnd = document.cookie.length;
			}
			cookieValue = decodeURIComponent(document.cookie.subString(cookieStart+cookieName.length, cookieEnd));
		}
		return cookieValue;
	},
	set: function(name, value, expires, path, domain, secure) {
		var cookieText = encodeURIComponent(name) + "=" + encodeURIComponent(value);
		if(expires instanceof Date) {
			cookieText += "; expires=" + expires.toGTMString();
		}
		if(path) {
			cookieText += "; path=" + path;
		}
		if(domain) {
			cookieText += "; domain=" + domain;
		}
		if(secure) {
			cookieText += "; secure";
		}
		document.cookie = cookieText;
	},
	unset: function(name, path, domain, secure) {
		this.set(name, "", new Date(0), path, domain, secure);
	}
};
```
- Web存储机制
克服由cookie带来的一些限制，当数据需要被严格控制在客户端时，无须持续地将数据发回服务器。Web Storage两个目标：
	- 提供一种在cookie之外存储会话数据的途径；
	- 提供一种存储大量可以跨会话存在的数据的机制。
sessionStorage对象存储特定于某个会话的数据，该数据保持到浏览器关闭，但可以跨页面刷新而存在，浏览器崩溃重启后依然可用（IE不行）。sessionStorage对象绑定于某个服务器会话，所以当文件在本地运行的时候是不可用的，且数据只能由最初给对象存储数据的页面访问到。
``` javascript
for (var key in sessionStorage) {
	var value = sessionStorage.getItem(key);
	alert(key + "=" + value);
}
delete sessionStorage.name;
sessionStorage.removeItem('name');
```
globalStorage需要指定域访问数据，是依据发起请求的页面的域名、协议和端口来限制的，如http和https就不同。globalStorage属性中的数据会一直保留在磁盘上，非常适合在客户端存储文档或者长期保存用户偏好设置。
``` javascript
globalStorage["www.wrox.com"].name = "Nicholas";
globalStorage["www.wrox.com"].removeItem("name");
globalStorage[location.host].getItem("book");
```
localStorage对象在修订过的HTML5规范中作为取代globalStorage。要访问同一个localStorage对象，页面必须来自同一个域名（子域名无效），使用同一种协议，在同一个端口上。同globalStorage中的数据一样，数据保留到通过Javascript删除或者用户清除浏览器缓存。
``` javascript
function getLocalStorage() {
	if(typeof localStorage == "object") {
		return localStorage;
	} else if(typeof globalStorage == "object") {
		return globalStorage;
	} else {
		throw new Error("Local storage not available");
	}
}//兼容只支持globalStorage的浏览器
```

- storage事件，webkit尚不支持
	- domain 发生变化的存储空间的域名，ie8和firefox只支持该属性
	- key 设置或删除的键名
	- newValue 如果是set则为新值，如果是remove则为null
	- oldValue 键被更改之前的值
``` javascript
EventUtil.addHandler(document, "storage", function(event) {
	alert("Storage changed for " + event.domain);
});
```	
大多数对数据存储限制是5MB，Chrome和Safari以及iOS版Safari和Android版WebKit是2.5MB.

- IndexedDB
创建一套API方便保存和读取JavaScript对象，同时还支持查询及搜索。操作都是异步的，需要注册onerror或onsuccess事件处理程序。

``` javascript
var indexedDB = window.indexedDB || window.msIndexedDB || window.mozIndexedDB || window.webkitIndexedDB;
```
IndexedDB是一种类似SQL数据库的结构化数据存储机制。数据不是存在表中而是对象存储空间中。创建对象存储空间时定义一个键后就可以添加数据，可以使用游标在空间中查询特定对象，索引则是为了提高查询速度而基于特定的属性创建的。唯一需要注意的是不要存储敏感数据，因为数据缓存不会加密。

# 最佳实践

- 可维护性
	- 可理解性
	- 直观性
	- 可适应性
	- 可扩展性
	- 可调度性
- 代码约定
	- 可读性 函数和方法都应该包含一个注释，描述其目的和使用的算法，参数和返回；大段代码、Hack和复杂算法也需要一个描述任务的注释；变量以名词，函数以动词开头不要担心长度，长度可通过压缩缓解
- 松散耦合 直接写在HTML中JavaScript，使用包含内联代码的<script>元素或者使用HTML属性来分配事件都是过于紧密的耦合。它们将表示数据的HTML和定义行为的JavaScript紧密耦合在一起了。还有就是JavaScript包含了HTML，JavaScript用于插入数据时，尽量不要直接插入标记，一般可以在页面中直接包含并限期标记，然后等到整个页面渲染好之后，就可以用JavaScript显示该标记而非生成它。另一种就是进行Ajax请求并获取更多要显示的HTML，这个方法可以让同样的渲染层（PHP/JSP/Ruby等）来输出标记，而不是直接嵌在JavaScript中。更容易确定错误来源，减轻维护度。通过更改样式类而非特定样式来实现CSS与JavaScript的耦合。解耦应用逻辑/事件处理程序,一个事件处理程序应该从事件对象中提取想着信息，并将这些信息传送到处理应用逻辑的某个方法中。好处是更容易更改触发特定过程的事件。可以在不附加到事件的情况下测试代码。原则1:只传来自event对象中所需的数据；2：任何可以在应用层面的动作都应该可以在不执行任何事件处理程序的情况下进行；3:任何事件处理程序都应该处理事件，然后将处理交给应用逻辑。
``` javascript
function validateValue(value) {
	value = 5 * parseInt(value);
	if(value > 10) {
		document.getElementById("error-msg").style.display = "block";
	}
}
function handleKeyPress(event) {
	event = EventUtil.getEvent(event);
	if(event.keyCode == 13) {
		var target = EventUtil.getTarget(event);
		validateValue(target.value);
	}
}
```
``` javascript
//创建全局对象
var Wrox = {};
//创建命名空间
Wrox.ProJS = {};
Wrox.ProJS.EventUtil = {};
Wrox.proJS.CookieUtil = {};
```
	- 如果值应为引用类型，使用instanceof检查其构造函数
	- 如果值应为一个基本类型，使用typeof检查其类型
	- 如果希望对象包含某个特定的方法名，则使用typeof操作符确保指定qkpbr方法存在于对象上。
``` javascript
var Constants = {
	INVALID_VALUE_MSG: "Invalid value!",
	INVALID_VALUE_URL: "/errors/invalid.php"
};
function validate(value) {
	if(!value) {
		alert(Constants.INVALID_VALUE_MSG);
		location.href = Constants.INVALID_VALUE_URL;
	}
}//Constants对象甚至可以完全在单独的文件中进行定义
```

- 性能
	- 注意作用域 1.避免全局查找，使用全局变量和函数肯定比局部的开销更大，因为涉及作用域链上的查找。将在一个函数中会用到多次的全局对象存储为局部变量总是没错的。一旦多次用到对象属性也是一样的，应该将其存储在局部变量中，第一次访问是O(n),后续就是O(1)了。
	- 减值迭代
	- 简化终止条件 避免如o(n)的属性查找
	- 使用后测试循环 
	- 展开循环
	- 避免双重解释 如eval、new Function、setTimeout("alert('Hello');",500)等
	- 优化DOM交互，文档碎片、大的DOM更改innerHTML更快、可以将事件处理程序附加到更高层的地方负责多个目标的事件处理。

# 新兴API

- requestAnimationFrame()
大多数电脑显示器的刷新频率是60Hz，大概相当于每秒重绘60次，大多浏览器都会对重绘操作加以限制，不超过显示器的重绘频率，因为即使超了也不会提升用户体验。因此，最平滑动画的最佳循环间隔是1000ms/60，约等于17ms.
- Page Visibility API 页面最小化了或者隐藏在了其他标签后面，那么有些功能比如轮询或都动画效果可以停下来。
- Geolocation API 定位
- File API FileReader/读取部分内容/对象URL/读取拖放文件/使用XHR上传文件
- Web计时
- Web Workers

