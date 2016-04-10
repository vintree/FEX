### 「请求」协同开发新思路

#### 什么是协同
在前端开发中，我们正在渐渐地采用协同的方式来合作，例如我们在使用bootstrap的样式和交互，bootstrap帮助我们协同，在使用jQeury的dom操作，jQeury帮助我们协同，使用公司的通用组件，组件在帮助我们协同，大家用着都一样东西，解决不同的问题，协同就像我们的`后勤`，协同我们开发，完成我们需要的功能，而去忽略过程，那`请求`如何协同呢？

#### 请求中的协同
我们试想一下，一个新项目启动，是如何分配任务？一般情况会把业务属性相同的模块或页面交给同一个人开发，是为了更好地复用代码，接口的数据属性基本一致，也就是说开发同学是根据项目属性进行分派，这里的协同就是自己和自己协同，因为第二次使用之前的代码时，过程已经清楚，注重结果；再说到`请求`，我们需要一个请求模块，帮我们忽略过程，直接给出结果，这里的请求模块不是简单的ajax，在说新思路之前，我们先要对比一下传统请求模式。

#### 传统的请求模式

![_2016_04_01_8_22_28](http://img3.tbcdn.cn/L1/461/1/f6bc9bf09fa1cc1d7d59de82fa131745ec7182e9)

上面这幅图代表着传统请求模式的开发策略，js和js之间没有联系，每使用一次接口，后面都有一个故事，但有些故事讲的是同一件事，这类故事没有给我们带来更高的开发效率，而被类似地址切换，参数名更改等问题降低了开发效率，我们需要减负，把更多的精力放在体验上。

有一句玩笑话
> 你们不做好体验，和那些后端有什么区别

在传统请求模式中，我们需要关心五个参数（url，method，params，success，error），还有可能需要更多，比如:

1. url
2. 数据格式
3. 统一处理的错误情况
4. 请求协议（get、post）
5. ....

在前端可以简单视为：

1. url可以看做是取数据的门牌号，不必关心他从哪里来，而关心带过来什么数据
2. 数据格式无论json还是jsonp，均视为json的读取方式
3. 在实际开发中，大部分错误情况均采用通用方式处理，如果有特殊情况可在error方法中重写
4. 无论是什么请求协议，无非是找到门牌号的方式，还是关系带过来什么数据
5. ....

> 无论你是谁，从哪里来，到哪里去，我只关心你带了多少盘缠和遇到了警察怎么办！

在敏捷开发、快速迭代的模式下，这些过程可以先忽略，这对我们开发效率来说没有明显的帮助，到了需要了解的时候查看一下源码，就一目了然了，这就是我们需要改进的地方。

#### 改进的请求模式

![_2016_04_01_11_09_41](http://img1.tbcdn.cn/L1/461/1/b3281b445df937aa80f06fe65f495d72d8c6e341)

在这种请求模式中添加了`请求模块`，其中有三个核心`中间件`，分别处理错误文案管理（errorMsg），请求管理（api），参数验证管理（param）

1. errorMsg，负责错误信息文案，通用处理方式
2. api，负责对url，请求方式，静态参数进行封装
3. param，负责对动态参数设置默认值和对必要参数预警

这三个中间件封装了请求中可以忽略的因素，而且可以预警一些普遍的低级问题，例如不需要关心参数初始值等

这些的目的都是为了提交请求，ajax模块需要根据后端给出的数据结构进行调整

##### ajax模块（简版）

这里没有使用jQeury的ajax方法，而采用了node的请求模块[`superagent`](https://www.npmjs.com/package/superagent)

这部分处理了与服务器的成功和失败状态，如果成功给出放出数据，失败则采用统一的处理方法或根据不同的业务采用其他方法（如：弹层提示，主动跳转等），在这里开发同学得到的数据是真正有价值的数据。

```

/*

	@structure
		
		底层  - 中间层
			 / ajax_common 公共部分：请求结束后的执行结构
			/
		ajax - get 对外提供get方法
			 - post 对外提供post方法

	@author
		abel
	@des
		1. 代理请求
		2. 处理请求中间过程
	@api
		ajax || post || get
		ajax:
			* method: String 请求方法
			* name: String 接口名称
			* params: Object 接口需要参数
			* success: Function 成功方法
			* error: Function 失败方法
		post:
			* name
			* params
			* success
			* error
		get: 
			* name
			* params
			* success
			* error
 */
import Superagent from 'superagent';

import Vars from './vars.js';
import FormatAjax from './formatAjax.js';
import Errs from './errs.js';

const ajax = () => {};
ajax.ajax = (method = '', name = '', params = {}, success = ()=>{}, error = (data)=>{alert(data.status.msg)}) => {
	let url = Vars.api(name);
	if(url) {
		if(method === 'get' || method === undefined) {
			url = FormatAjax.get(Vars.api(name), params);
			Superagent.get(url).end((err, res) => {
				ajax.ajax_common(res, success, error);
			});
		} else if(method === 'post') {
			Superagent.post(url).send(params).end((err, res) => {
				ajax.ajax_common(res, success, error);
			});
		} else {
			console.err(Errs.err('unMethod', method))
		}
	} else {
		console.err(Errs.err('un', name));
	}
}

ajax.ajax_common = (res, success, error) => {
	if(res.status === 200) {
		let data = JSON.parse(res.text);
		if(data.status.code === '0') {
			success(data);
		} else {
			error(data);
		}
	}
}

ajax.get = (name, params, success, error) => {
	ajax.ajax('get', name, params, success, error);
}

ajax.post = (name, params, success, error) => {
	ajax.ajax('post', name, params, success, error);
}

module.exports = ajax;


```


##### errorMsg（简版）

errorMsg负责统一的错误文案或预设特殊处理方式

```
const errs = function() {}

/*
	@author
		abel
	@des
		前缀术语：
			un: 未放置，空参数
			unfully: 不完全，不完整，没有传递所有参数
			format: 格式不正确
 */

errs.err = (key = '', name = '') => {
	const obj = {
		format: '格式不正确! ' + name,
		un: '没有找到! ' + name,
		unMethod: '未提供该方法支持! '+ name,
		unFully: '未提供完善数据！'+ name
	}
	if(key) {
		return obj[key];
	} 
	console.err(obj['un'] + 'key');
	
	module.exports = errs;
}

```

#### api

##### user.js

这个js负责用户登入注册接口，一类接口用一个js分类放置，方便管理；

params: 传入的参数

needPrams: 必要参数，并设置默认值

采用什么协议请求(get)

使用哪个url（user_show）

两个函数（success, err）


>这是请求模块的核心，放出去的api就是ajax.user.show({}, callback1, callback2)，开发同学关心需要的参数，成功函数和失败函数；

那请求模块的代码谁写呢？这时候需要一个对业务理解深刻的同学来进行封装，并与后端对接，关注业务走向，看似复杂，其实就是开发同学的`后勤`，在这里他需要关心参数是否正确，协议，地址等；


```
import Common from './_common.js';
const user = () => {};
 
user.show = (params, success, err) => {
	let needParams = {
		mid: 0,
		requester: 0
	};
	Common(params, needParams, 'get', 'user_show', success, err);
};


```

##### params

##### _common.js

该js负责对协议，必要参数进行处理

```

import Ajax from '../../ajax.js';
import Errs from '../../errs.js';

const common = (allParams, needParams, method, name, success, err) => {
	let isFullParams = common.isFullParams(allParams, needParams);
	if(isFullParams) {
		if(method === 'get') {
			Ajax.get(name, allParams, success, err);
		} else if(method === 'post') {
			Ajax.post(name, allParams, success, err);
		}
	}
};

common.isFullParams = (allParams, needParams) => {
	for(let o in needParams) {
		if(needParams.hasOwnProperty(o)) {
			if(!allParams.hasOwnProperty(o)) {
				console.error(Errs.err('unFully'), o);
				return false;
			}
		}
	}
	return true;
};

common.ajax = Ajax;
common.errs = Errs;
module.exports = common;


```

#### 总结
>请求协同可以统一管理请求中的通用部分，把过程弱化，强调结果，开发同学弱关注需要关注parasm（参数），success（成功函数），error（错误函数）即可，应为已经有协同帮助我们矫正了一些可遇见的问题


如果有问题，先找`后勤`同学帮助处理，这样我们就可以当一个安静的美男子了！