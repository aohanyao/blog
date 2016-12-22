---
title: Android初级进阶之自定义酷炫菜单
date: 2016-08-26 15:26:36
tags:
---

## 效果图
![](http://obh9jd33g.bkt.clouddn.com/酷炫菜单.gif)

## 前言

最近一直在学习自定义控件，发现自己依然是那么的渣渣。上面的UI效果来自[这个网站](https://material.uplabs.com/posts/alignment-fab-bar)，果然，交互设计的效果与实际做出来的效果还是相差很大啊。

## 分析
整个效果实现起来还是十分的简单，分析一下：

1. 中间是一个圆用drawCircle()就可以画出来。
2. 圆中心是两条线：drawLine()
3. 后边的背景是一个圆角矩形：drawRoundRect()
4. 动画效果使用ValueAnimator发散1--0之间的数值，不断的更改长度和坐标即可完成。
5. 不想吐槽那四个小正方形。

## 开始编码
### Step 1
继承自原生的View，重写构造方法，在构造方法中初始化画笔等操作

	 /**
     * 初始化画笔等
     */
    private void initSomeThing() {
        mPaint = new Paint();
        mPaint.setAntiAlias(true);
        mPaint.setStrokeCap(Paint.Cap.ROUND);
        mPaint.setStrokeWidth(1);
    }

### Step 2 Measure（测量）
测量View的宽高，支持Padding，wrap_content等

	 @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int measureWidth = measureSize(widthMeasureSpec, getWindowMsg(getContext())[0] - getPaddingLeft() - getPaddingRight() - mCenterMenuOffset);
        int measureHeight = measureSize(heightMeasureSpec, dp2px(getContext(), 90)) + getPaddingBottom() + getPaddingTop();
        setMeasuredDimension(measureWidth, measureHeight);
    }


测量封装方法

	/**
     * 测量宽高 模式
     *
     * @param measureSpec
     * @return
     */
    private  int measureSize(int measureSpec,int defaultSize) {
        int result;
        int size = MeasureSpec.getSize(measureSpec);
        int mode = MeasureSpec.getMode(measureSpec);
        if (mode == MeasureSpec.EXACTLY) {
            result = size;
        } else {
            result = defaultSize;
            if (mode == MeasureSpec.AT_MOST) {
                result = Math.min(result, size);
            }
        }
        return result;
    }

获取屏幕宽高

	 /**
     * 获取屏幕宽高
     *
     * @return wh[1] 屏幕高度
     */
    private   int[] getWindowMsg(Context context) {
        int[] wh = new int[2];
        WindowManager wm = (WindowManager) context
                .getSystemService(Context.WINDOW_SERVICE);
        wh[0] = wm.getDefaultDisplay().getWidth();
        wh[1] = wm.getDefaultDisplay().getHeight();
        //Log.d("vivi", "屏幕宽 =" + wh[0]);
        //Log.d("vivi", "屏幕高 =" + wh[1]);
        return wh;
    }

dp2px

	/**
     * dp转px
     *
     * @param context
     * @param dpVal
     * @return
     */
    private  int dp2px(Context context, float dpVal) {
        return (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP,
                dpVal, context.getResources().getDisplayMetrics());
    }

### Step 3 在onSizeChanged中初始化宽高的一些属性

	@Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        mWidth = w;//宽度
        mHeight = h;//高度
        mCenterMenuOffset = (int) (mHeight * 0.3);//中间 圆形菜单的上下偏移量
        mMenuBackgroundOffset = (int) ((mHeight * 0.5) / 2); //紫色圆角矩形的上下偏移量
        mCenterMenuRadius = (mHeight - mCenterMenuOffset) / 2;//中间圆形的半径
        mForkLenght = (int) (mCenterMenuRadius * 0.4);//中间两个线的长度

        //得到中心菜单的矩阵 start
        int l = mWidth / 2 - mCenterMenuRadius;
        int t = mCenterMenuOffset / 2;
        int b = mHeight - mCenterMenuOffset / 2;
        int r = mWidth / 2 + mCenterMenuRadius;
        mCenterMenuRect = new Rect(l, t, r, b);//中心的圆的矩阵，用来响应点击事件的
        //得到中心菜单的矩阵 end
    }

### Step 4 画中心的圆
画个圆，没什么难度，偏移量，半径都在onSizeChanged中初始化完成了。这里要给圆加上阴影

	  //画中心的圆 start
        mPaint.setShadowLayer(7, 4, 5, 0xff999999);//设置阴影图层
        this.setLayerType(View.LAYER_TYPE_SOFTWARE, mPaint);
        mPaint.setColor(mCenterMenuColor);
        canvas.drawCircle(mWidth / 2, mHeight / 2, mCenterMenuRadius, mPaint);

### Step 5 圆中心的两根线
仔细看效果图，两个线上边的点是相同的，只是在动画的时候更改X轴的坐标就可以从 ^ 变成X了。首先要将画布的坐标点移到中心，这样更方便绘制这两条线。

	//圆形菜单中的 叉叉
        canvas.translate(mWidth / 2, mHeight / 2);//移动坐标点
        mPaint.setColor(Color.WHITE);
        mPaint.setStrokeWidth(10);
        canvas.drawLine(0, -mForkLenght, -mForkLenght, mForkLenght, mPaint);
        canvas.drawLine(0, -mForkLenght, mForkLenght, mForkLenght, mPaint);

效果就出来啦：

![](http://obh9jd33g.bkt.clouddn.com/中心初始状态.png)

只要将左边的线的坐标更改为负数，右边更改为正数，就变成了X了

	 canvas.drawLine(mForkLenght, -mForkLenght, -mForkLenght, mForkLenght, mPaint);
     canvas.drawLine(1mForkLenght, -mForkLenght, mForkLenght, mForkLenght, mPaint);

![](http://obh9jd33g.bkt.clouddn.com/中心交叉状态.png)
	

### Step 6 绘制背景矩形
绘制静态的矩形，也就是整个展开的效果

 	// 菜单背景 start
    int left = mWidth / 2;//100
    float nowWidth = (left - mMenuBackgroundOffset / 2);
    RectF mMenuRectF = new RectF(left - nowWidth, mMenuBackgroundOffset, left + nowWidth, mHeight - mMenuBackgroundOffset);
    mPaint.setColor(mMenuBackgroundColor);
    canvas.drawRoundRect(mMenuRectF, mCenterMenuRadius, mCenterMenuRadius, mPaint);

效果出来了有没有？

![展开初始状态](http://obh9jd33g.bkt.clouddn.com/展开初始状态.png)

### Step 7 为中心的按钮响应点击事件
自定义原生View应该如何响应点击事件呢？其实非常的简单，在onSizeChanged里面计算出了中心圆的矩形区域mCenterMenuRect。而Rect提供了一个方法contains(x, y)来判断xy坐标是不是在这个矩形当中，于是这就简单了，只需要重写onTouch方法判断xy坐标就好。

	  @Override
    public boolean onTouchEvent(MotionEvent event) {
        int x = (int) event.getX();//获得点击的坐标
        int y = (int) event.getY();
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                break;
            case MotionEvent.ACTION_UP:
                if (mCenterMenuRect.contains(x, y)) {//包含 打开菜单获关闭
                    openOrClose();
                }
                break;
        }
        return true;
    }

	/**
     * 打开或关闭
     */
    public void openOrClose() {
        if (isOpen) {
            startAnimator(1, 0);
        } else {
            startAnimator(0, 1);
        }
        isOpen = !isOpen;
    }
### Step 8 增加动画
使用ValueAnimator来发散1--0之间的数值，相当于百分比，然后不断的postInvalidate达到效果

	/**
     * 开启启动动画
     *
     * @param from
     * @param end
     */
    private void startAnimator(final int from, int end) {
        if (mAnimatorBackground != null && mAnimatorBackground.isRunning()) {
            mAnimatorBackground.cancel();
            mAnimatorBackground = null;
        }
        mAnimatorBackground = ValueAnimator.ofFloat(from, end).setDuration(500);
        mAnimatorBackground.setInterpolator(new OvershootInterpolator());
        mAnimatorBackground.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                mPreValue = (float) animation.getAnimatedValue();
                postInvalidate();
            }
        });
        mAnimatorBackground.start();
    }

onDraw方法代码

	 @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        // 菜单背景 start
        int left = mWidth / 2;//100
        float nowWidth = (left - mMenuBackgroundOffset / 2) * mPreValue;
        RectF mMenuRectF = new RectF(left - nowWidth, mMenuBackgroundOffset, left + nowWidth, mHeight - mMenuBackgroundOffset);
        mPaint.setColor(mMenuBackgroundColor);
        canvas.drawRoundRect(mMenuRectF, mCenterMenuRadius, mCenterMenuRadius, mPaint);
        // 菜单背景 end
        //画中心的圆 start
        mPaint.setShadowLayer(7, 4, 5, 0xff999999);//设置阴影图层
        this.setLayerType(View.LAYER_TYPE_SOFTWARE, mPaint);
        mPaint.setColor(mCenterMenuColor);
        canvas.drawCircle(mWidth / 2, mHeight / 2, mCenterMenuRadius, mPaint);
        mPaint.setShadowLayer(0, 0, 0, Color.TRANSPARENT);
        // canvas.rotate(360 * mPreValue, mWidth / 2, mHeight / 2);

        //画中心的圆 end
        //圆形菜单中的 叉叉
        canvas.translate(mWidth / 2, mHeight / 2);//移动坐标点
        mPaint.setColor(Color.WHITE);
        mPaint.setStrokeWidth(10);
        canvas.drawLine(mForkLenght * mPreValue, -mForkLenght, -mForkLenght, mForkLenght, mPaint);
        canvas.drawLine(-mForkLenght * mPreValue, -mForkLenght, mForkLenght, mForkLenght, mPaint);
    }

效果如下：

![酷炫菜单2](http://obh9jd33g.bkt.clouddn.com/酷炫菜单2.gif)


	 // canvas.rotate(360 * mPreValue, mWidth / 2, 

这句注释打开后就会有一个旋转的效果，如下：

![酷炫菜单3](http://obh9jd33g.bkt.clouddn.com/酷炫菜单3.gif)


##最后
OK，这个效果基本上到这里也就完成了，不要问我为什么没有画矩形，我只是想偷懒，最后会附上源码的地址，自己研究去，也就是计算矩形的位置加上动画而已。

自己造轮子，才是最快的进步方式。

[源码](https://github.com/aohanyao/Advanced/tree/master/code/CustomView/CoolMenu)