---
title: android小技巧之来不及解释了快上车--EventBus3
date: 2016-08-10 09:52:32
tags:
---
## 什么是EventBus
[先附上EventBus的git地址](https://github.com/greenrobot/EventBus)

EventBus，就按照名字翻译来说"事件公共汽车",官方的说法是"EventBus is a publish/subscribe event bus optimized for Android."也就是"eventbus是Android发布/订阅事件总线"。或许不太好理解，但是在我现在做的这个项目中，我已经用EventBus来取代广播和handler，而且十分的好用及简单。

下面附上从官方偷过来的图
![](https://raw.githubusercontent.com/greenrobot/EventBus/master/EventBus-Publish-Subscribe.png)
## 如何获取EventBus
1. Android Studio(如果你还没有用上，那你就out了)直接使用依赖的方式即可
		 
		compile 'org.greenrobot:eventbus:3.0.0'
2. Eclipse.....只能去下载jar包了

   [EventBus jar 下载地址](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22de.greenrobot%22%20AND%20a%3A%22eventbus%22)
## EventBus使用方法
EventBus使用是特别的简单，只需要四步(当然依赖不算一步)
### 第一步 注册
在需要的地方进行注册，一般是在onCreate方法内进行注册

```java
	EventBus.getDefault().register(this);
```
###	第二步 取消注册
页面销毁一定要取消注册，要不然会照成内存泄露一般是在onDestroy或者onDestroyView方法中取消注册

```java
	EventBus.getDefault().unregister(this);
```
### 第三步 写上相应的方法
EventBus获取事件的方法有四个，修饰符是public，可接受有参和无参。意思就不用解释了，和名字一样。

	1、onEvent
	2、onEventMainThread
	3、onEventBackground
	4、onEventAsync


在我项目中我最常用的是onEventMainThread，因为我更多的是用来更新UI。

	 @Subscribe
     public void onEventMainThread(EventObject event) {
		//TODO 你要做的事情
	 }
 

### 第四步 post
在需要发送事件的地方post对象

```java
	 EventBus.getDefault().post(EventObject);
```
在这里一定要解释EventObject这个东西，这是一个事件对象。接收的事件的参数必须要与发送事件事件的参数一致才能接收事件，这就好比聊QQ、微信发消息，EventObject就是你要聊天的对象。

当然，有单聊和群聊，假如在多个地方注册同样的EventObject，改如何区分？
EventObject是一个对象，那么EventObject久可以有很多属性，例如你要在群里问某一个人一件事件，你发送了消息，大家都接收到了，但只有你问的对象才回复你。一般会怎么做，要么直接说某某啥啥的，要么直接艾特他。这样虽然所有人都看见了这个消息，但只有你问的对象才回复你。（如果这样你都不明白，那只有去补习java基础和广播部分的知识了）
### EventBus的实际小案例
代码是敲出来的，所以再多的文字说明都是苍白的。这里就来整个实际的小案例，也是我在项目中的运用方式。
	
#### 例子（一） 更新UI
主要实现类是未登录状态下登陆成功后更新UI，使用步骤和前面一样，就不在多说。

事件对象，也就是一个普通的javaBean

```java

	/**
	 * <p>作者：江俊超 on 2016/8/10 15:01</p>
	 * <p>邮箱：928692385@qq.com</p>
	 * <p>更新UI的事件对象</p>
	 */
	public class UpdateUiEvent {
	    private String ms;
	    public UpdateUiEvent(String msf) {
	        this.ms = msf;
	    }
	    public String getMs() {
	        return ms;
	    }
	
	    public void setMs(String ms) {
	        this.ms = ms;
	    }
	}
```

页面非常的简单，一个是未登录页面，可以不断的打开自己，是标准的启动模式，另一个是登陆页面，输入登陆信息后就会关闭并把输入的信息发送出去。未登录页面接收到事件之后想信息显示在TextView中。

未登录界面xml，很简单两个按钮，一个文本框

```xml

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:layout_height="match_parent"
    android:gravity="center"
    tools:context=".NoLoginActivity">
    <TextView
        android:id="@+id/tv_msg"
        android:layout_centerInParent="true"
        android:text="当前未登录"
        android:gravity="center"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="20sp" />
    <Button
        android:layout_width="wrap_content"
        android:text="去登陆"
        android:onClick="goLogin"
        android:layout_height="wrap_content" />
    <Button
        android:layout_width="wrap_content"
        android:text="继续启动自己"
        android:onClick="startUpdateUI"
        android:layout_height="wrap_content" />
</LinearLayout>

```

未登陆界面代码

```java
	
	public class NoLoginActivity extends AppCompatActivity {
	    private TextView mTvMsg;
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_no_login1);
	        mTvMsg = (TextView) findViewById(R.id.tv_msg);
	        EventBus.getDefault().register(this);//注册
	        mTvMsg.append("  当前页面："+hashCode()+" \n");
	    }
	    public void startUpdateUI(View view) {
	        startActivity(new Intent(this, NoLoginActivity.class));
	    }
	    public void goLogin(View view) {
	        startActivity(new Intent(this, LoginActivity.class));
	    }
	    @Subscribe
	    public void onEventMainThread(UpdateUiEvent uiEvent) {
	        mTvMsg.setText("  当前页面："+hashCode()+"\n信息："+uiEvent.getMs());//接收到事件  将信息设置到页面上
	    }
	
	    @Override
	    protected void onDestroy() {
	        EventBus.getDefault().unregister(this);//取消注册
	        super.onDestroy();
	    }
	}

```

登陆界面xml，一个按钮和一个输入框

```xml

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    tools:context="aohanyao.com.eventbussample.LoginActivity">

    <EditText
        android:id="@+id/et_msg"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="请输入登陆信息" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="login"
        android:text="登陆" />
</LinearLayout>

```

登陆页面代码，点击登陆，获取输入框的值，提示一下并发送出去。

```java

	public class LoginActivity extends AppCompatActivity {
	    private EditText mEtMsg;
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_login);
	        mEtMsg = (EditText) findViewById(R.id.et_msg);
	    }
	    public void login(View view) {
	        Toast.makeText(this, mEtMsg.getText().toString(), Toast.LENGTH_SHORT).show();
	        //发送事件
	        EventBus.getDefault().post(new UpdateUiEvent(mEtMsg.getText().toString()));
	        finish();
	    }
	}
```

最终就是下面的效果了，先不断的启动当前Activity三次后登陆，完成后，按下后退键，可以看到每个页面上面都显示了输入的内容。

![](http://obh9jd33g.bkt.clouddn.com/eventBusSample.gif)

## 应用场景
1. 像上面的例子是一种。
2. 类似于未读信息的显示之类的，有多个页面需要显示未读信息，但是在其中一个页面读了一条，那么所有页面剩余的未读信息UI都要更改。
3. .......好多好多

其实说上来，需要用自定义广播的地方，都可以使用EventBus来代替。
## 总结
文采不好，敬请见谅！

[源码地址](https://github.com/aohanyao/Advanced/tree/master/code/other/EventBusSample)