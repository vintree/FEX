#### 「JS」深入理解W3C标准冒泡和捕获原理

> 我们看似触发了一个事件，是从该事件向外延展，其实不然。
>
> 事件触发有两套机制，一个叫捕获，一个叫冒泡
>
> 这两兄弟，性格完全相反，简单，我们来看下，是什么原理吧！

我们来做几个任务吧！

有一个前提，#div为标签，#div-child为子标签，他们是嵌套关系

#####支线任务1
```
//捕获模式
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
}, true);

//捕获模式
document.getElementById('div-child').addEventListener('click', function(e) {
	alert('2');
}, true);
	
```

```
结果:
1. 1
2. 2

```

#####支线任务2
```
//捕获模式 
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
}, true);

//冒泡模式
document.getElementById('div-child').addEventListener('click', function(e) {
	alert('2');
}, false);
	
```

```
结果:
1. 1
2. 2

```


#####支线任务3
```
//冒泡模式
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
}, false);

//捕获模式
document.getElementById('div-child').addEventListener('click', function(e) {
	alert('2');
}, true);
	
```

```
结果:
1. 2
2. 1

```

#####支线任务4
```
//冒泡模式
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
}, false);

//冒泡模式
document.getElementById('div-child').addEventListener('click', function(e) {
	alert('2');
}, false);
	
```

```
结果:
1. 2
2. 1

```
> 总结1
> 从上面的结果看，结果的顺序和父标签(#div)的事件机制有关
> 
> 如果父标签为捕获机制，先触发父标签事件，再到子标签
> 
> 如果父标签为冒泡机制，先触发子标签事件，再到父标签
> 
> 我先把结论说出来吧：
> 
* 捕获机制：从本身（包含本身回调函数的执行）开始向子节点延伸，直至到达target对象上
* 冒泡机制：从target对象（包含本身回调函数的执行）开始向父节点冒泡，直至到达本身节点
>
> 这样我们就能很好地判断那个先，那个后了
>
> 但事件有一个stopPropagation()方法，这个方法又有什么效果呢！我们往下看吧

#####支线任务5
```
//捕获模式
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
	e.stopPropagation();
}, true);

//捕获模式
document.getElementById('div-child').addEventListener('click', function(e) {
	alert('2');
}, true);
	
```

```
结果:
1. 1

```

> 这个结果说明，stopPropagation()会阻止捕获机制向下延伸，无法传达到子节点上

#####支线任务6
```
//捕获模式
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
	e.stopPropagation();
}, false);

//捕获模式
document.getElementById('div-child').addEventListener('click', function(e) {
	alert('2');
}, true);
	
```

```
结果:
1. 2
2. 1

```
> 支线任务6和支线任务3基本保持一致，只是多加了一个e.stopPropagation()而已，但结果也是一样，说明在冒泡机制下e.stopPropagation()没有发挥作用！
>
> 在总结1当中提到冒泡机制是从下往上冒泡并且逐步运行，所以在支线任务6中，
> 
> * 第一步，#div被点击开始触发冒泡机制，触发到了#div-child上，#div-child开始执行。
> * 第二步，第一步执行完，向上冒泡，到了#div，开始执行#div绑定的函数。
> 
> 所以开起来e.stopPropagation()失效，其实和执行顺序有关


#####支线任务7
```
//捕获模式
document.getElementById('div').addEventListener('click', function(e) {
	alert('1');
}, false);

//捕获模式
document.getElementById('div-child').addEventListener('click', function(e) {
	e.stopPropagation();
	alert('2');
}, true);
	
```

```
结果:
1. 2

```
> 来分析一下为什么出来这个结果
> 
> 第一步，触发#div机制，一看，是一个冒泡机制，那就走到target吧，其实就是#div-child，执行过程中，触发了e.stopPropagation(),这个方法，导致冒泡链断裂，不再向上冒泡，所以#div没有被触发！


> 总结：
> 
> * 捕获是从自身向下延展，自身是起点
> 
> * 冒泡是target向上冒泡，自身是终点
> 
> * 这里进行了[1,2]、[2,1]、[1]、[2]出现4中情况的实例，可以很好地解释捕获和冒泡的关系，其实代码形式没有枚举，可以通过没有举例出来的代码来验证自己的答案。




























