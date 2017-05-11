### 啰嗦废话

本篇为[Android初级进阶之自定义果冻视图(BouncingJellyView)(一)](http://www.jianshu.com/p/dcae9e4e4171)的后续篇章。没有看过的赶紧去看看，顺便点个喜欢。


BouncingJellyView 果冻视图，就像果冻一样伸缩弹跳，也叫阻尼效果。这个效果在MIUI上面到处都可以看到。


上一篇文章到现在的间隔已经是足足的三个月时间了，因为期间在待业中，所以没什么心情来写文章，所以拖到了现在，找着了工作，解决了其中的一些小问题，才开始写文章。

我现在的公司是做自己的产品，而且是从零到有得一个过程，所以我会把我在做项目的过程中遇到的问题解决方案及一些自定义的UI控件写成文章和demo，希望大家多多star，多多关注，也为能为我将来增加一些砝码。

### 效果图
废话不多说  先整两张效果图

![普通的](http://obh9jd33g.bkt.clouddn.com/BouncingJelly_%E6%99%AE%E9%80%9A.gif)

![嵌套RecyclerView的](http://obh9jd33g.bkt.clouddn.com/BouncingJelly_recycleView.gif)

### 区别	
1. [上个版本](http://www.jianshu.com/p/dcae9e4e4171)所留下的BUG，应该没有人发现。我这里描述一下：在5.0以上使用ToolBar的时候，不管是上拉还是下拉都是没有问题的，但是在5.0以下的时候就很糟糕了，下拉没有问题，但是上拉却是会遮挡ToolBar，如下图所示：

![BouncingJellyView在5.0以下的BUG](http://obh9jd33g.bkt.clouddn.com/BouncingJellyView_bug.png)

2. [上个版本](http://www.jianshu.com/p/dcae9e4e4171)中，要使用对应的BouncingJelly才能够达到效果，但是在这个版本中，只需要使用BouncingJellyView就可以达到相应的效果。

3. 和RecyclerView等嵌套的时候快速滑动不是很流畅。


### 分析
1. 在5.0以下进行缩放的时候，会遮挡住ToolBar，其中的原因是：上面的ToolBar与我的BouncingJellyView是处与同一级的，而且都是CoordinatorLayout之下。缩放的是整个BouncingJellyView，导致遮挡。


### 解决方案
1. 继承至android.support.v4.widget.NestedScrollView更加便捷的解决滑动冲突。

2. 在onInterceptTouchEvent中做了一些判断，能让BouncingJellyView进行快速滑动。

		@Override
	    public boolean onInterceptTouchEvent(MotionEvent e) {
	        int action = e.getAction();
	        switch (action) {
	            case MotionEvent.ACTION_DOWN:
	                downX = (int) e.getRawX();
	                downY = (int) e.getRawY();
	                break;
	            case MotionEvent.ACTION_MOVE:
	                int moveY = (int) e.getRawY();
	                //判断是否达到最小滚动值
	                if (Math.abs(moveY - downY) > mTouchSlop) {
	                      return true;
	                }
	        }
	        return super.onInterceptTouchEvent(e);
	    }

3. 改变缩放目标，由原来的缩放BouncingJellyView改成缩放子View

		 @Override
	    protected void onLayout(boolean changed, int l, int t, int r, int b) {
	        super.onLayout(changed, l, t, r, b);
	        childAt = getChildAt(0);
	    }


		/**
	     * 从顶部开始滑动
	     */
	    public void bouncingTo() {
	        //设置X坐标点
	        ViewHelper.setPivotX(childAt, getWidth() / 2);
	        //设置Y坐标点
	        ViewHelper.setPivotY(childAt, 0);
	        //进行缩放
	        ViewHelper.setScaleY(childAt, 1.0f + offsetScale);
	        if (onBouncingJellyListener != null) {
	            onBouncingJellyListener.onBouncingJelly(1.0f + offsetScale);
	        }
	    }
	
	    /**
	     * 从顶部开始滑动
	     */
	    public void bouncingBottom() {
	        //设置X坐标点
	        ViewHelper.setPivotX(childAt, getWidth() / 2);
	        //设置Y坐标点
	        ViewHelper.setPivotY(childAt, childAt.getHeight());
	        ViewHelper.setScaleY(childAt, 1.0f + offsetScale);
	        if (onBouncingJellyListener != null) {
	            onBouncingJellyListener.onBouncingJelly(1.0f + offsetScale);
	        }
	    }

### TODO
1. 快速滚动到底部应该滑动一次。


[源码地址](https://github.com/aohanyao/BouncingJelly/tree/master)  希望大家能我一个star， 万分感谢。