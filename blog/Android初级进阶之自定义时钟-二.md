---
title: Android初级进阶之自定义时钟(二)
date: 2016-08-09 14:16:13
tags:
---
在上一篇[Android初级进阶之自定义时钟(一)](http://www.jianshu.com/p/7df63512a1b0)里面已经完成了时钟的静态绘制，是这个样子的：

![](http://obh9jd33g.bkt.clouddn.com/colick_img.png)

现在我们要做的是在上面的基础上继续完善，达到以下的效果。

![](http://obh9jd33g.bkt.clouddn.com/时钟滚动.gif)

### 前言
果然，我还是不够格，还是要继续学习，继续积累。上一篇不是已经完成了页面的绘制咯，然后需要将时分秒的指针动起来，与系统时间对应，想了老半天：没有办法计算出指针的x2和y2坐标，于是什么勾股定理，三角函数统统上了一遍，结果还是不行。最后才发现，答案就在前面，我已经写出来了

	绘制刻度线与刻度值是最大的难点，在这里就是利用了Canvas为我们提供的rotate方法，顾名思义，就是旋转画布。我们以视图坐标系 x y 进行绘制，没绘制完成一次，旋转相应的角度就完成了刻度线的绘制。玩过PS的人应该好理解。

明明都已经将刻度盘都画出来了，那么是不是只要将刻度盘的刻度的长度延伸，这不就是时分秒指针了吗？作孽啊！

### 获取当前的时间
	 int hour = Integer.parseInt(new SimpleDateFormat("HH").format(new Date()));
     int minute = Integer.parseInt(new SimpleDateFormat("mm").format(new Date()));
     int second = Integer.parseInt(new SimpleDateFormat("ss").format(new Date()));

### 绘制指针
绘制指针并不难，就和刻度一样，只不过是将长度变长了而已，这里面最关键的部分是求角度，不过这没什么难度的，以下是绘制指针的代码
	
       //------------------绘制时针
        canvas.save();//保存画布
        Paint paintHour = new Paint();
        paintHour.setStrokeWidth(15);//宽度
        paintHour.setColor(0xffff6600);//颜色 红色
        paintHour.setAntiAlias(true);//抗锯齿
        //当前小时百分比的度数  分钟在一个小时内的度数
        float hourDegree = (hour / 12f * 360) + (minute / 60f * 15/*一个小时是15度*/);
        int hourWidth = (int) (mCircleWidth / 2 * 0.5/*时钟半径的一半，也就是长度*/);
        canvas.rotate(hourDegree, mCircleWidth / 2, mCircleWidth / 2);//旋转角度
        canvas.drawLine(mCircleWidth / 2,
                mCircleWidth / 2 - hourWidth,
                mCircleWidth / 2,
                mCircleWidth / 2 + offset + 10/*offset + 10 是为了突出指针，不至于难看*/,
                paintHour);
        //当前画布与保存的画布合并
        canvas.restore();
        //------------------绘制时针

        //------------------绘制分针
        canvas.save();//保存画布
        Paint paintMinute = new Paint();
        paintMinute.setStrokeWidth(10);
        paintMinute.setAntiAlias(true);
        float minuteDegree = minute / 60f * 360;
        int minuteWidth = (int) (mCircleWidth / 2 * 0.7);
        canvas.rotate(minuteDegree, mCircleWidth / 2, mCircleWidth / 2);
        canvas.drawLine(mCircleWidth / 2,
                mCircleWidth / 2 - minuteWidth,
                mCircleWidth / 2,
                mCircleWidth / 2 + offset * 2,
                paintMinute);
        //当前画布与保存的画布合并
        canvas.restore();
        //------------------绘制分针


        //----------------------秒针
        Paint paintSecond = new Paint();
        paintSecond.setStrokeWidth(7);
        paintSecond.setAntiAlias(true);
        paintSecond.setColor(0xffff0000);
        canvas.save();
        float secondDegree = second / 60f * 360;//秒针  15秒的 度数
        canvas.rotate(secondDegree, mCircleWidth / 2, mCircleWidth / 2);
        int secondWidth = (int) (mCircleWidth / 2 * 0.9);//获得秒针的长度
        canvas.drawLine(mCircleWidth / 2,
                mCircleWidth / 2 - secondWidth,
                mCircleWidth / 2,
                mCircleWidth / 2 + offset * 3,
                paintSecond);
        //当前画布与保存的画布合并
        canvas.restore();
        //----------------------秒针

        //---------------------绘制秒针上面的圆圈
        canvas.save();
        Paint pinPaintCircle = new Paint();
        pinPaintCircle.setColor(0xffff0000);
        pinPaintCircle.setAntiAlias(true);
        canvas.drawCircle(mCircleWidth / 2, mCircleWidth / 2, offset, pinPaintCircle);
        canvas.restore();
        //---------------------绘制秒针上面的圆圈

完了，这部分就是绘制指针，如何让指针动起来，也非常的简单，只需要绘制完成后调用postInvalidateDelayed(1000)方法延迟一秒后继续绘制就好了。就和前面的效果图一模一样啦。

### 结语
好记性不如烂笔头，这是一句是在的话。有很多东西，尤其是像我们搞技术的，技术不用，慢慢的就会忘记的。另外我的语言水平不怎么好和技术都不怎么好，如果我哪里说得不对，及时指出，我会虚心更正。

### 其它
随着慢慢的深入，我会将我的一些经验放出来的，希望共同进步。这个时钟暂时就到这里，不过我会在这个基础之上继续弄仪表盘。


[源码地址](https://github.com/aohanyao/Advanced/blob/master/code/CustomView/Dashboard/app/src/main/java/aohanyao/com/dashboard/DashboardView.java)