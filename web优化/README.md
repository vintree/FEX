### web优化合集

> 在web做事，都要瞻前顾后做开发，不能大手大脚，看似拙计，但里面有不少门道

####img和background
> 在web端，放置图片有两种方式：
> 
>	1. 「css」background
>	2. 「dom」img
>

#####	从浏览器运行机制理解；
先看两幅图：

webkit策略
![]('https://pic3.zhimg.com/b2b7c07bd7f5af231cdeaa0c3804a686_b.png')

Gecko策略
![]('https://pic3.zhimg.com/b2b7c07bd7f5af231cdeaa0c3804a686_b.png')

从两个内核可以看出，dom和css同时准备好，再进行下一步，以图片为主的页面，采用img方案，会加长html解析，从而推迟了css的解析工作，实际表相空白时间/无样式时间加上；

#####	从html，css角度理解；
######	img：
img标签，在页面有img的位置，当页面有大量的img标签时，页面出现卡顿现象，内存吃紧，简单说dom很耗内存，在低配安卓机上不够理想；

一般使用img放置重要图片，logo等


######	background：
background是css方法，

一般使用background放置展览、banner等

#####	进一步优化：
浏览器滚动为了保证在60帧，做了大量的优化，但效果，dom多了就开始吃机器性能了；

设想一下，如果有10000个显式dom，每次滚动页面，页面拖着10000个显式dom在滚动，不在视口的dom，对用户来说是无意义的，我们可以对不在视口的dom做隐藏，使用visibolity: hidden，主动隐藏，页面流畅度大大提高，避免出现吃机器性能的情况发生；
