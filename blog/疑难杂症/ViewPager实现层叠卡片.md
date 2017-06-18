### (一) 开始 
ViewPager实现一个层叠的卡片，先看看效果

![层叠卡片](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%87.gif)


我将其用在了APP的引导页面上，这个效果虽然看上去很难，但实际上实现起来特别的简单，主要是使用PageTransformer来实现这个效果，推荐先看一下hongyang的前置教程:[Android 自定义 ViewPager 打造千变万化的图片切换效果](http://blog.csdn.net/lmj623565791/article/details/38026503)，请确保你已经掌握前置，原理里都写了，就不在累述.(主要还是因为太懒了)


### (二) 编码
#### 1. 首先我们先创建一个Activity，配置好页面，就像以下效果。一个ViewPager,里面放fragment，fragment里面就放一个CardView。还需要给viewpager的setOffscreenPageLimit一个大一点的值。

![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%871.png)

正常情况下，viewpager里面的内容是水平排列的。现在要做的第一步，就是将viewpager里面所有的view都显示在同一个位置，那么就需要自定义PageTransformer了。

#### 2. 自定义PageTransformer
既然ViewPager里面的View是水平排列的，那么只要将每个view的x轴坐标更改为：view的宽度乘以下标的负数，这样就排列在一起了，为了方便起见，还给view增加了一个透明度。代码如下:

		public void transformPage(View page, float position) {
	        //设置透明度
	        page.setAlpha(0.5f);
	        //设置每个View在中间
	        page.setTranslationX((-page.getWidth() * position));
	
	    }
	    
来看看效果吧。
![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%872.png)

果然不出所料，效果和我们猜想的一样。

#### 3.看到这里，你是否已经想到接下来该怎么了做了吧？是的，我们只需要根据下标，将view按照顺序进行xy轴缩放，并且将Y轴向下移动。我定义了一个变量mOffset表示偏移量，赋值为40px。代码如下

		page.setTranslationX((-page.getWidth() * position));
        //缩放比例
        float scale = (page.getWidth() - mOffset * position) / (float) (page.getWidth());

        page.setScaleX(scale);
        page.setScaleY(scale);

        page.setTranslationY(mOffset * position);


当当当当。。。下面就是见证奇迹的时刻了

![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%873.png)

是不是完全达到了我们的预期呢？然而还没有结束，因为我不管怎么滑动都是没有效果，这是为什么呢？因为没有处理划出去的那一页，加了一个下标判断，position<= 0的情况下就是在翻页(打log)，接下来看代码：


		public void transformPage(View page, float position) {
        if (position <= 0.0f) {//被滑动的那页
            page.setTranslationX(0f);
        } else {//未被滑动的页
            page.setTranslationX((-page.getWidth() * position));
            //缩放比例
            float scale = (page.getWidth() - mOffset * position) / (float) (page.getWidth());

            page.setScaleX(scale);
            page.setScaleY(scale);

            page.setTranslationY(mOffset * position);

        }
    }
    
    
   来看看效果：
    ![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%874.gif)
    
    
#### 4.至此，也是差不多实现了效果，还差最后一步。
第一张效果图中，在翻页的时候有一个旋转的角度，而我们前面实现的只是一个平滑的效果。在这里达到的效果就是从0°开始，随着翻页的进行，卡片旋转至-45°角，从上一步得到一个结论：position<= 0的情况下就是在翻页。看代码：

	public void transformPage(View page, float position) {
        if (position <= 0.0f) {//被滑动的那页
            page.setTranslationX(0f);
            //旋转角度  45° * -0.1 = -4.5°
            page.setRotation((45 * position));
        } else {
            //缩放比例
            float scale = (page.getWidth() - mScaleOffset * position) / (float) (page.getWidth());

            page.setScaleX(scale);
            page.setScaleY(scale);

            page.setTranslationX((-page.getWidth() * position));
            page.setTranslationY((mScaleOffset * 0.8f) * position);
        }

    }
    
    
  运行起来看看效果
  
  ![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%875.gif)
  
  效果虽然是达到了，但是为什么会留一个角呢？想必聪明的你应该想明白了吧?里面的view移动的是一个屏幕的宽度，当我们平移的时候刚好移动到了屏幕的外面，当然没有问题。但是旋转却是以中心为原点进行喜欢转的，所以自然，就会漏出一个角了。
  解决方法是view进行旋转的同时，将view的X轴进行减少，减少多少呢？从上图看，大概⅓就差不多能够移动到屏幕外面了。
  上代码：
  
  	public void transformPage(View page, float position) {
        if (position <= 0.0f) {//被滑动的那页  position 是-下标~ 0
            page.setTranslationX(0f);
            //旋转角度  45° * -0.1 = -4.5°
            page.setRotation((45 * position));
            //X轴偏移 li:  300/3 * -0.1 = -10
            page.setTranslationX((page.getWidth() / 3 * position));
        } else {
            //缩放比例
            float scale = (page.getWidth() - mScaleOffset * position) / (float) (page.getWidth());

            page.setScaleX(scale);
            page.setScaleY(scale);

            page.setTranslationX((-page.getWidth() * position));
            page.setTranslationY((mScaleOffset * 0.8f) * position);
        }

    }
    
    
看看，效果是不是更加的细腻了呢？

![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%877.gif)

至此，整个效果就算是完成了，下面附上PageTransformer的代码

	/**
	 * 水平滑动的
	 */
	public class CardPageTransformer implements ViewPager.PageTransformer {
	    /**
	     * 偏移量
	     */
	    private int mScaleOffset = 40;
	
	
	    /**
	     * @param mScaleOffset 缩放偏移量 单位 px
	     */
	    public CardPageTransformer(int mScaleOffset) {
	        this.mScaleOffset = mScaleOffset;
	    }

	    @SuppressLint("NewApi")
	    public void transformPage(View page, float position) {
	        if (position <= 0.0f) {//被滑动的那页  position 是-下标~ 0
	            page.setTranslationX(0f);
	            //旋转角度  45° * -0.1 = -4.5°
	            page.setRotation((45 * position));
	            //X轴偏移 li:  300/3 * -0.1 = -10
	            page.setTranslationX((page.getWidth() / 3 * position));
	        } else {
	            //缩放比例
	            float scale = (page.getWidth() - mScaleOffset * position) / (float) (page.getWidth());
	
	            page.setScaleX(scale);
	            page.setScaleY(scale);
	
	            page.setTranslationX((-page.getWidth() * position));
	            page.setTranslationY((mScaleOffset * 0.8f) * position);
	        }
	
	    }

	}




### (三) 展望

上面的步骤有没有引起你的联想呢？例如：层叠在上面、左面、右面、上下滑动，再加个透明动画？哈哈 这些我都实现了，你可以直接引用我的library，使用我里面已经写好的PageTransformer。

来先看看效果

![](http://obh9jd33g.bkt.clouddn.com/%E5%B1%82%E5%8F%A0%E5%8D%A1%E7%89%878.gif)
以下是使用方法

#### setp 1

	repositories {
		...
		maven { url 'https://jitpack.io' }
	}
	
	
### step 2

	dependencies {
	        compile 'com.github.aohanyao:ViewPagerCardTransformer:v1.0'
	}

### step 3

	 vpMain.setPageTransformer(true, CardPageTransformer.getBuild()//建造者模式
                .addAnimationType(PageTransformerConfig.ROTATION)//默认动画 default animation rotation  旋转  当然 也可以一次性添加两个  后续会增加更多动画
                .setRotation(-45)//旋转角度
                .addAnimationType(PageTransformerConfig.ALPHA)//默认动画 透明度 暂时还有问题
                .setViewType(mViewType)
                .setOnPageTransformerListener(new OnPageTransformerListener() {
                    @Override
                    public void onPageTransformerListener(View page, float position) {
                        //你也可以在这里对 page 实行自定义动画 cust anim
                    }
                })
                .setTranslationOffset(40)
                .setScaleOffset(80)
                .create());






### about
如果文中有什么不对的地方，欢迎指出！

[源码地址](https://github.com/aohanyao/ViewPagerCardTransformer) 如果我的代码对你有帮组，请给我一个star

我的[简书](http://www.jianshu.com/u/3e53005808b1)

来来扫下码，关注一下吧,或者微信搜索AndroidRookie

![AndroidRookie](http://upload-images.jianshu.io/upload_images/1760510-809d019561f671ed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)