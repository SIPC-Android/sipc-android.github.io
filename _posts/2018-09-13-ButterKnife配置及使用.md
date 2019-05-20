---
title: ButterKnife配置及使用
layout:     post
subtitle:   ""
date:       2018-09-13 13:28:29
author:     "linweixian"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
---

## 黄油刀(ButterKnife)
![ButterKnife]()

### [官方文档](http://jakewharton.github.io/butterknife/)

### 简介(为什么需要使用它？)：
ButterKnife取代了findViewById完成View的注入，提高效率
+ 使用注解生成模块代码，用于把一些字段和方法绑定到 Android 的 View;
+ 强大的View绑定和Click事件等处理功能，简化代码，提升开发效率
+ 运行时不会影响APP效率，使用配置方便
+ 代码清晰，可读性强

### 配置
ButterKnife没有正确配置时，会有NullPointException。
1. 首先在project的build.gradle中添加classpath
```Groovy
buildscript {
  dependencies {
    ...
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
  }
}
```
2. 然后是model的build.gradle中添加依赖
这里看的所有教程都是用compile，apt。但其实compile已经被淘汰很久了。(误事啊误事...)
ButterKnife已经讲的的很明白了。
```Groovy
apply plugin: 'com.android.application'
dependencies {
    implementation 'com.jakewharton:butterknife:(insert latest version)'
	annotationProcessor 'com.jakewharton:butterknife-compiler:(insert latest version)'
}
```
+ insert laster verrsion 目前是8.8.1（2018.09）


### 使用：
+ 绑定资源
**@BindView(R.id) Type tempName;**
1. Activity中
需在onCreate 的setContentView()后添加ButterKnife.bind(this)绑定布局;
2. Fragment & Adapter ViewHolder中
F : ButterKnife.bind(this, rootView);
A : 
```java
public class MyAdapter extends BaseAdapter {
 	static class ViewHolder{
		@BindView(R.id.text_view) TextView mTextView;
	    @BindView(R.id.button) Button mButton;;
	    public ViewHolder(View itemView) {
	       ButterKnife.bind(this, itemView);
	    }
	}
}
```
3. setOnClickListener()	--绑定点击事件
```java
@OnClick(R.id)
void setClick(View v){}
```
+ 参数接受一个id集合用于多个View处理同一事件
```java
@OnClick({R.id, R.id, ...})
void setClick(View v){}
```

4. BaseActivity中是使用
由于butterknife绑定需要在每个Activity中都实现一遍，所以可以将这个操作写进一个Base，然后让所有的Activity都去继承它。BaseActivity中的实现细节如下：
```java
public class BaseActivity extends AppCompatActivity{
	protected UnBinder mBinder;
	public void setContentView(int LayoutResId){
		super.setContentView(LayoutResId);
		mBinder = ButterKnife.bind(this);		//ButterKnife初始化
	}
	protected void onDestroy() {
        mBinder.unbind();
        super.onDestroy();
    }
}
```

### 注意：
+ 这些view不能是priavate 或static 。
+ 在使用BindView方法的时候，如果目标View没有找的的话，会抛出异常。如果不想受到这个异常，可以使用@Nullable（该注释标注了该参数可否为null）。
+ 由于在Activity中都要进行ButterKnife.bind操作，所以可以将bind操作写入baseActivity。8.4以后ButterKnife移除了ButterKnife.unBind()方法，取而代之的是ButterKnife.bind(this)会返回一个Unbinder的引用，通过Unbinder的unbind()方法进行解除绑定。

5. Zelezny插件的使用
+ File->Setting->Plugins，打开搜索Zelezny，选择**Android ButterKnife Plugins**下载，重启AS可用。
+ 也可以选择本地安装。先到该项目的地址，下载jar包。[传送门](https://github.com/avast/android-butterknife-zelezny)
![jar包下载]()
然后还在上边那个Plugins页，点击Install plugins from disk...
![本地导入]()
然后重启。。。

+ 使用 
![官方的使用演示](https://raw.githubusercontent.com/avast/android-butterknife-zelezny/master/img/zelezny_animated.gif)
**Ps：将光标移动到R.layout上操作**
+ 有人说该插件只支持butterknife:7.0.1，可以试试将8.8.1切换到7.0.1的重新集成，应该可以使用Generate butterknife菜单功能，再切换回8.8.1版本，zelezny就可以正常使用了
PPs：其实讲道理，我也不能正常使用，AS重启以后插件没安装上，两种安装方式都不能成功，大概可能还是版本的原因...但因为该插件口碑很好，所以还是介绍一下... 
