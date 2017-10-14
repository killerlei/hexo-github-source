---
title: swiper-animate 冲突解决
date: 2017-04-23 11:39:44
tags: [swiper-animal] 
categories: [front]
---
# 冲突
* swiper是个使用广泛，兼容性很高的js插件，可以独立使用，也可以跟jQuery配合使用。
* swiper-animate是swiper基础上开发的动画插件，可以实现非常酷炫的效果。
* 这周在写一个页面，里面有轮播图、tab选项卡，用swiper开发非常方便，当然也用到了swiper-animate，
但是，两个swiper实例之间的 swiper-animate会互相影响，发生动画效果消失或错乱。
* 在网上也没找到解决办法，最后只能用笨办法了。

<!-- more -->

#解决
1. 在swiper-animate.jsh中复制三个函数，并分别改名为原名+me，修改querySelectorAll(".ani")为querySelectorAll(".anime")。
![11](http://oo0zdjapt.bkt.clouddn.com/hexo-2swiper-animate.png)
2. 在html中把第二个用到动画的标签类名由ani改成animate
![22](http://oo0zdjapt.bkt.clouddn.com/hexo-2swiper-html.png)
3. 在js中创建实例时候，调用修改过的函数名。
![33](http://oo0zdjapt.bkt.clouddn.com/hexo-2swper-function.png)

这样就解决了冲突，只是办法太笨了。