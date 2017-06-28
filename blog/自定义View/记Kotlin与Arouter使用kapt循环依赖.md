问题

		Error:Circular dependency between the following tasks:
		:module_home:compileDebugKotlin
		\--- :module_home:kaptDebugKotlin
		     \--- :module_home:compileDebugKotlin (*)
		(*) - details omitted (listed previously)


解决方案


使用高版本的gradle
	
	classpath 'com.android.tools.build:gradle:2.3.3'
	

这是一个Kotlin Gradle plugin 1.1.2-4的BUG

	
	buildscript {
	    ...
	    dependencies {
	        ...
	        classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:1.1.2-2'
	    }
	}


在 local.properties 添加以下:
	
	kotlin.incremental=false




地址

[原地址](https://stackoverflow.com/questions/44035504/how-to-use-data-binding-and-kotlin-in-android-studio-3-0-0)