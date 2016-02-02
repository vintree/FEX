#### 深入理解变量与函数执行原理

> 有时因为使用不当，可以把一个全局变量突然变成局部变量，这种诡异的现象来自js的运行机制导致的，也是开发者不正确使用导致的，我们来看下，基本原理吧！

#####支线任务1
```
//请脑补 外层为window作用域下，以下均默认该作用域下

var a = 1;
function b() {
	console.log(a);
	a = function() {
	}
	console.log(a);
	a = 2;
	console.log(a);
}
b()
console.log(a);		
	
```

```
结果:
1. 1
2. function () {}
3. 2
4. 2

```
这个函数中的变量a一直是window下的a「a -> window」，是不是很简单，我们往下看吧！

#####支线任务2

```
var a = 1;
function b() {
	console.log(a);
	function a(){
	}
	console.log(a);
	a = 2;
	console.log(a);
}
b()
console.log(a);		
	
```

```
结果:
1. function a() {}
2. function a() {}
3. 2
4. 1

```
* 到这里有没有产生疑惑呢？这里只是把支线1中的a = function() {} 改成 function a() {}

* 我们观察他们的不同，仅仅是函数命名方式的不同而已，就产生了不同的变化！

* 我们来整体观察两个支线 a 的变化

	* 支线1: a在第1-4个log中均在window下
	* 支线2: a在第4个log中打印了1，说明a在window下，根据从上至下运行原则，可以推断变量a在第1-3个log中是b的成员
	
根据常识我们可以猜测在函数 b 中定义过变量 a，导致变量 a 在第1-3个log中变成为了成员变量，可以推断出:

* 这样的函数定义 function a(){} 会导成员变量 a 被定义而且被置前。
* a = function a() {} 定义也被置前

根据上面的推断我们来翻译下支线2

##### 支线任务2.1
```
var a = 1;
function b() {
	var a = function a(){};
	console.log(a);
	console.log(a);
	a = 2;
	console.log(a);
}
b()
console.log(a);
	
```
问题来了，var a = function a(){}; 为什么怎么巧都是 a 呢？

支线2的书写方式会导致一个和函数名相同的变量名出现。

这样就能解释支线2打印出来的顺序了！有没有理解呢？

既然a = function(){} 和 function a(){} 有怎么大的区别，要是放在一起，他们的先后关系是什么样的呢？会不会相互影响呢？我们带着疑问往下看吧！

#####支线任务3
```
var a = 1;
function b() {
	console.log(a);
	a = function() {
	}
	console.log(a);
	function a() {
	}
	console.log(a);
	a = 2;
	console.log(a);
}
b()
console.log(a);		
	
```

```
结果:
1. function a() {}
2. function () {}
3. function () {}
4. 2
5. 1

```

根据支线2的知识储备，我们可以根据支线3的结果来进行推断
#####支线任务3.1

```
var a = 1;
function b() {
	var a = function a() {
	}
	console.log(a);
	a = function() {
	}
	console.log(a);
	console.log(a);
	a = 2;
	console.log(a);
}
b()
console.log(a);		
	
```

对比得出：这样 function a(){} 的定义方式，相对交灵活