---
title: Android小技巧之无限循环的ViewPager
date: 2016-08-25 09:53:00
tags:
---
## 前言
之所以会写着篇文章的原因是我现在项目用运用到了广告轮播(BannerView)，当时在赶项目的时候在github上面找到了符合的开
源库	就直接引用了，但是该开源库稍微有点庞大，功能比较繁多。于是在这样的情况之下，我决定自己造轮子。（现在处于项目完善和迭代器，正在重构项目，去除多余的第三方等以减少APK的大小，增加用户的体验度）

效果图如下，为了录制效果，所以将速度加快了。

![轮播图](http://obh9jd33g.bkt.clouddn.com/轮播图.gif)

## Step1
在这期间碰到的第一个难点就是ViewPager的无缝循环，于是乎在各种谷歌之下发现都是千篇一律的代码之后依然投入了[stackoverflow](http://stackoverflow.com/)的怀抱，最终找到了解决无缝循环的两种方式：

第一种：
	左右各增加一个页面，造成无缝的假象

第二种：
	getcount的时候返回Integer.MAX_VALUE，这种方式会创建大量的对象，对于我来说不可取。


## Step2
第一种方法实现起来非常的简单，其原理是在Adapter里面实现OnPageChangeListener接口，重写onPageSelected来搞定，假如我有三个页面需要相互切换：
	
	A<->B<->C

只要在初始化数据的时候在A的前面加上C，C的后面加上A现在就变成了3+2五个页面：

	C<->A<->B<->C<->A

这时候在重写onPageSelected方法，当前的position为0的时候，setCurrentItem为(getCount-2),假如当前的position==4(getCount-1),就setCurrentItem为1：

	int pageCount = getCount();
    if (position == 0) {
        pager.setCurrentItem(pageCount - 2, false);
    } else if (position == pageCount - 1) {
        //延时切换，避免闪烁
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                pager.setCurrentItem(1, false);
            }
        },600);
    }

里面我写了一个匿名内部类，为了方便才这么写，至于为什么需要延迟？注释了试试就知道了。

### Step3
完整代码如下：
	
	/***
	 * BannerAdapter
	 * @param <T>
	 */
	public class BannerAdapter<T> extends PagerAdapter {
	    private List<T> mDatas;
	    private Context mContext;
	    private int count;
	
	    public BannerAdapter(final ViewPager pager, List<T> mDatas, Context mContext) {
	        super();
	        this.mBannerHelper = mBannerHelper;
	        this.mContext = mContext;
	        int actualNoOfIDs = mDatas.size();
	        count = actualNoOfIDs + 2;
	
	        T t = mDatas.get(0);//获取到第一个
	        T t1 = mDatas.get(mDatas.size() - 1);//获取到最后一个
	
	        this.mDatas = mDatas;
	        this.mDatas.add(mDatas.size(), t);
	        this.mDatas.add(0, t1);
	
	        pager.addOnPageChangeListener(new OnPageChangeListener() {
	            @Override
	            public void onPageSelected(int position) {
	                int pageCount = getCount();
	                if (position == 0) {
	                    pager.setCurrentItem(pageCount - 2, false);
	                } else if (position == pageCount - 1) {
	                    //延时切换，避免闪烁
	                    new Handler().postDelayed(new Runnable() {
	                        @Override
	                        public void run() {
	                            pager.setCurrentItem(1, false);
	                        }
	                    },600);
	                }
	            }
	
	            @Override
	            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
	            }
	
	            @Override
	            public void onPageScrollStateChanged(int state) {
	            }
	        });
	    }
	
	    public int getCount() {
	        return count;
	    }
	
	    public Object instantiateItem(View container, int position) {
	        View view;
	        //TODO 在这里返回你的View
	        ((ViewPager) container).addView(view, 0);
	        return view;
	    }
	
	    @Override
	    public void destroyItem(View container, int position, Object object) {
	        ((ViewPager) container).removeView((View) object);
	    }
	
	    @Override
	    public void finishUpdate(View container) {
	    }
	
	    @Override
	    public boolean isViewFromObject(View view, Object object) {
	        return view == object;
	    }
	
	    @Override
	    public void restoreState(Parcelable state, ClassLoader loader) {
	    }
	
	    @Override
	    public Parcelable saveState() {
	        return null;
	    }
	
	    @Override
	    public void startUpdate(View container) {
	    }
	}

## 最后
最近在学习自定义View，感觉进度十分的缓慢，不过刚看到几个UI交互效果，准备自己实现试试，挑战自我。

[源码](https://github.com/aohanyao/BannerView)