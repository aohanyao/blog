#前言
在之前的一个项目中，compileSdkVersion和targetSdkVersion都是使用的24，都是使用最新的，紧跟潮流嘛，一直是相安无事。直到有一天接入一个第三方的SDK的时候，问题暴露了：第三方SDK仅仅只支持最大22的编译，23及以上他本身的SDK就会直接崩溃。没办法，只能降级项目中的编译版本来兼容他了。这一降，才知道不是那么好办的。

### 1.创建项目

![](http://obh9jd33g.bkt.clouddn.com/E9EB4874-CBB3-4CA3-AAC0-2C27192582B7.png)

如上图所示，创建了一个项目，compileSdkVersion、targetSdkVersion都是22，并且引用了一个appcompat-v7:22.2.1包。


### 2.提升引用appcompat-v7版本
看下图，将v7包提升到了23.2.1，就直接爆出了一个错误。

![](http://obh9jd33g.bkt.clouddn.com/WX20170511-203415@2x.png)

这个错误，如果单单只是在我们自己的项目中出现，还是很好解决的，要么降低v7的版本和sdk一致，要么就提升sdk版本和v7保持一致。但是，有得时候所引用的v7是在我们引用的开源库中引用的，我们自己的sdk不能提升，也不能修改开源库中的引用版本。

### 3.其他开源库appcompat-v7高版本
为了方便，我就自己创建了一个library并引用了，并且在library中引用了appcompat-v7:22.2.1,并且sdk都提升到了24.如下图：

![](http://obh9jd33g.bkt.clouddn.com/3.png)

编译一下app，爆出了和前面一样的错误。直接引用了最高版本的v7：24.1.0

![](http://obh9jd33g.bkt.clouddn.com/4.png)

是不是很绝望？自己的的SDK又不能提升，别人的引用又不能修改。Gradle提供了一个强制依赖的方法：

	resolutionStrategy.force


### 4.resolutionStrategy.force

使用方法简单，只需要将以下脚本放在Android节点下即可


    configurations.all {
        resolutionStrategy.force "com.android.support:appcompat-v7:22.2.1"
    }
    
如图所示

![](http://obh9jd33g.bkt.clouddn.com/5.png)


##总结
半年了，终于又开始写博客了。现在Android行情已经不是那么好了。