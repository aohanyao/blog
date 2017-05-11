---
title: Android开发小技巧之有些事我早已忘记
date: 2016-08-09 15:14:02
tags:
---

##前言
开发中有些事我早已忘记，已经变成了本能。

### 开源库
1. 引入一个开源库之前一定要想清楚，这个库是不是合适自己的项目，是不是必需品，尤其是整体UI的库。


### 编码
1. 记住，要写基类，BaseActivity,BaseFragment，将一些常用的方法变量写到基类中.如Context，Dialog，Toast等，在基类写一个showToast(String msg)方法，直接调用不更好？
2. 想


### 沟通
1. 如果是研发阶段，请一定一定一定要对后台接口的返回数据格式规范，起码的msg,result,data，不要乱改！(虽然这只是没好的想法)

		{
	    "result": 0,
	    "msg": "message",
	    "data": {}
		}