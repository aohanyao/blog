## 前言
说机不说吧，没图说个啥？先看看效果：

![水平循环的View](http://olrt5mymy.bkt.clouddn.com/screens_hots.gif)


水平方向无限滑动的View，支持任意View，自定义。不会oom哦。

以下都是讲解其中的内容，如果你不想看的话你可以直接查看[仓库](https://github.com/aohanyao/HorizontalLoopView).

前置技能树:

	1. 自定义View相关知识
	2. Scroller相关

## 原理

	在设置的View个数上，左右各再增加一个View，左右滑动等于一个View的宽度的时候立即回滚到原
	来的样子，并且更新数据。


## 编码

### 1. 初始化
创建HorizontalLoopView，直接继承LinearLayout，设置垂直方向为水平。

	``` java
	
    /**
     * 初始化一些滚动的属性
     */
    private void initScroll() {
        mScroller = new Scroller(getContext());
        //水平垂直
        setGravity(Gravity.CENTER_VERTICAL);
        //水平方向
        setOrientation(HORIZONTAL);
        //视图滚动配置
        final ViewConfiguration configuration = ViewConfiguration.get(getContext());
        //获取最小滚动距离
        mMinimumVelocity = configuration.getScaledMinimumFlingVelocity();
        //最大滚动距离
        mMaximumVelocity = configuration.getScaledMaximumFlingVelocity();
    }

	```