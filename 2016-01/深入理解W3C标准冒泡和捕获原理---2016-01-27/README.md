#### 深入理解W3C标准冒泡和捕获原理

> 在前端开发时，项目经常使用jQuery、zepto作为快速开发的基础库，用基础库封装的事件，我们知道这两个基础库都是冒泡机制的事件。
>
> 事件机制一般在dom嵌套，并父、子层均绑定事件时需要注意。
>
> 这两个技术说来也简单，他们是两种传播策略而已，一个向上（冒泡），一个向下（捕获）
>
> 思考：为什么这两个基础库都采用冒泡事件，而不考虑捕获！

#### (一) 捕获和冒泡如何相互影响

我们来做几个任务吧！

有一个前提，#parent为标签，#child为子标签，他们是嵌套关系

#####支线任务1
```
//捕获模式
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent');
}, true);

//捕获模式
document.getElementById('child').addEventListener('click', function(e) {
	console.log('child');
}, true);
	
```

```
结果:
1. parent
2. child
```
![_2016_03_26_7_22_17](http://img2.tbcdn.cn/L1/461/1/3f4a80f1ceb8dfb03acc90b6cec0f59163737e7e)

#####支线任务2
```
//捕获模式
document.getElementById('parent').addEventListener('click', function(e) {
    console.log('parent');
}, true);

//冒泡模式
document.getElementById('child').addEventListener('click', function(e) {
    console.log('child');
}, false);
	
```

```
结果:
1. parent
2. child
```
![_2016_03_26_7_22_17](http://img2.tbcdn.cn/L1/461/1/3f4a80f1ceb8dfb03acc90b6cec0f59163737e7e)


#####支线任务3
```
//冒泡模式
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent');
}, false);

//捕获模式
document.getElementById('child').addEventListener('click', function(e) {
	console.log('child');
}, true);
	
```

```
结果:
1. child
2. parent
```
![_2016_03_26_7_25_46](http://img1.tbcdn.cn/L1/461/1/8e3c090cea653bed8a8d961ab65e4194096fb0bd)

#####支线任务4
```
//冒泡模式
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent');
}, false);

//冒泡模式
document.getElementById('child').addEventListener('click', function(e) {
	console.log('child');
}, false);
	
```

```
结果:
1. child
2. parent
```
![_2016_03_26_7_25_46](http://img1.tbcdn.cn/L1/461/1/8e3c090cea653bed8a8d961ab65e4194096fb0bd)

#### 总结

##### 研究
| child\parent | true(捕获) | false(冒泡) |
| ------------ | ------------- | ------------ |
| true(捕获)  | parent child  | child parent |
| false(冒泡) | parent child  | child parent |

* 从上面的结果得出，父容器的机制确定，子容器的机制不定，结果是确定的，说明父容器影响传递；
* 支线任务1-4是点击在子容器上，以子容器作为研究对象；
	1. 如果父容器为捕获（true）机制，先触发本身事件，再到子容器；
	2. 如果父容器为冒泡（false）机制，先触发子容器事件，再到本身；
	
##### 结论1	
* 在嵌套容器上点击时，点击对象是最外层容器；
* 捕获机制：本身作为第一个执行参考对象，从本身开始向下传递；
* 冒泡机制：从最底层开始向上传递，直至到达本身


#### （二）stopPropagation方法，阻止了什么？

前提：如果有stopPropagation方法，均放在console方法后面；

有一个假设：

1. 因为stopPropagation是在console后面执行，所以无论何种情况至少出现一个结果；
2. 有4种策略(true + stopPropagation)、(false + stopPropagation)、(true)、(false)，也就是说父容器和子容器的排序有16种；
3. 因为`假设1`，所以结果应该（不确定）有4个分别是(parent、child)、(child、parent)、(parent)、(child)；

我们带着这些假设来做任务。

#####支线任务5
```
//捕获模式
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent')
	e.stopPropagation();
}, true);

//捕获模式
document.getElementById('child').addEventListener('click', function(e) {
	console.log('child')
}, true);
	
```

```
结果:
1. parent

```
![_2016_03_27_12_52_39](http://img3.tbcdn.cn/L1/461/1/53296d5d7cc1b573b39c5e5c3b718c67c56f7667)

> 这个结果说明，stopPropagation方法会阻止传递，无法传达到子节点上。
> 
> **所以stopPropagation不是传闻的阻止冒泡方法，而是阻止传递的方法**；

#####支线任务6
```
//冒泡模式
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent')
	e.stopPropagation();
}, false);

//捕获模式
document.getElementById('child').addEventListener('click', function(e) {
    console.log('child')
}, true);
	
```

```
结果:
1. child
2. parent

```
![_2016_03_27_1_00_10](http://img2.tbcdn.cn/L1/461/1/a1830f03f9aa5329a807931a11c9b049e4053352)

> 按照上面的知识储备，再次论证了`结论1`

#####支线任务7
```
//冒泡模式
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent');
}, false);

//捕获模式
document.getElementById('child').addEventListener('click', function(e) {
	e.stopPropagation();
    console.log('child');
}, true);
	
```

```
结果:
1. child

```
![_2016_03_27_1_02_42](http://img4.tbcdn.cn/L1/461/1/559ed30d72c921eecb55ce43d54eff4cea43ed3d)
 
> 子容器触发了e.stopPropagation()，导致冒泡链断裂，不再向上冒泡，所以#parent没有被触发！

#####支线任务8
```
//捕获
document.getElementById('parent').addEventListener('click', function(e) {
	console.log('parent');
}, true);

//冒泡
document.getElementById('child').addEventListener('click', function(e) {
	console.log('child');
	e.stopPropagation();
}, false);
	
```

```
结果:
1. parent
2. child

```
![_2016_03_26_7_22_17](http://img2.tbcdn.cn/L1/461/1/3f4a80f1ceb8dfb03acc90b6cec0f59163737e7e)


因为得到了`假设2`的所有结果，所以不再列举了，来总结一下！

#### 总结

##### 规律
| child\parent | true(捕获) stop| false(冒泡) stop |       true |     false   |
| ------------ | ------------- | ------------ | ------------- | ------------ |
| true(捕获) stop  |   未	|  	未		|      未           |    child -   |
| false(冒泡) stop|   	未		|  		未		|     parent child          |    未    |
| true            | parent -   |child parent   |      未        |         未     |
| false           |      未      |    未           |    未          |    未          |

##### 结论
这里进行了[parent,child]、[child,parent]、[parent]、[child] 4种结果，但没有枚举，其余12种排序自己撸一遍，可以更深入了解事件机制背后的原理！


### 全部总结
1. 通过click事件可以推论，事件的触发均从最外层触发（document、html）；
2. 事件的执行是先通过机制再通过回调执行；
3. 捕获机制：本身作为第一个执行参考对象，从本身开始向下传递；
4. 冒泡机制：从最底层开始向上传递，直至到达本身；
5. stopPropagation方法不是大家说的阻止冒泡，而是阻止传递（捕获 + 冒泡）；