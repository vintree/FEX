#### 「HTML」探究HTML5接口FileReader

> 文件base64格式熟悉吗？
> 
> base64格式就是FileReader接口格式化后的数据！
> 
> 知道了这个接口的使用方法，我们就可以不用ajaxFileUpload这个插件了
> 
> 使用这个接口非常简单，我们往下看吧

#####支线任务1
```
// 我们先做好预设（一个图片标签、一个file标签）
<img src= />
<input type="file" multiple="multiple" accept="image/jpg,image/png,image/jpeg" onChange='fileChange(e)' />

```
#####支线任务2
我们要先获取input对象，再在对象中判断是否有数据

```
fileChange(e) {
	const fileNode = e.target; // input对象
	const imgData = ''; // 放置地址
	if(fileNode.files) {
		
	}
}

```

#####支线任务3
因为这是HTML5的接口，首先要判断这个接口是否存在

```
fileChange(e) {
	const fileNode = e.target;
	const imgData = '';
	if(fileNode.files) {
		if(window.FileReader) {
			
		}
	}
}

```

#####支线任务3
我们需要把这个接口实例化

```

fileChange(e) {
	const fileNode = e.target;
	const imgData = '';
	if(fileNode.files) {
		if(window.FileReader) {
			// var reader = new FileReader();
			const reader = new FileReader();
		}
	}
}

```

#####支线任务4
我再使用FileReader方法

FileReader提供了4个方法

* readAsBinaryString(file)-----将文件读取为二进制编码
* readAsText(file, encoding)-----将文件读取为文本
* readAsDataURL(file)-----将文件读取为DataURL
* abort()-----终端读取操作


```
fileChange(e) {
	const fileNode = e.target;
	const imgData = '';
	if(fileNode.files) {
		if(window.FileReader) {
			// var reader = new FileReader();
			const reader = new FileReader();
			reader.readAsDataURL(fileNode.files[0]);
		}
	}
}

//为了不影响支线任务，下面放置了所有使用案例
reader.readAsBinaryString(fileNode.files[0]);
reader.readAsText(fileNode.files[0], 'UTF-8');
reader.readAsDataURL(fileNode.files[0]);
reader.abort(fileNode.files[0]);	
	
```
readAsBinaryString
()




#####支线任务5
选择了方法后，需要接受数据，数据需要通过事件来获取，获取后放置img标签中

FileReader提供了6个监听事件

* onload-----成功读取
* onabort-----中断
* onerror-----报错
* onloadstart-----开始
* onprogress-----正在取读
* onloadend-----取读完成

```
fileChange(e) {
	const fileNode = e.target;
	const imgData = '';
	if(fileNode.files) {
		if(window.FileReader) {
			// var reader = new FileReader();
			const reader = new FileReader();
			reader.readAsDataURL(fileNode.files[0]);
			reader.onload = function(e1) {
				imgData = e1.target.result;
				document.querySelector('img').setAtrribute('src', imgData);
			}
		}
	}
}

//为了不影响支线任务，下面放置了所有使用案例
//成功读取
reader.onload = function(e) {
	alert('成功读取');
}
//中断
reader.onabort = function(e) {
	alert('中断');
}
//报错
reader.onerror = function(e) {
	alert('报错');
}
//开始
reader.onloadstart = function(e) {
	alert('开始');
}
//正在取读
reader.onprogress = function(e) {
	alert('正在取读')
}
//取读完成（无论成功 或是 失败）
reader.onloadend = function(e) {
	alert('取读完成');
}

```





























