MultiProcessComponent
==============

源码地址
-------

+ <a href="https://github.com/onlynight/MultiProcessComponent">github源码下载地址https://github.com/onlynight/MultiProcessComponent</a>

概述
----

这个demo中将会向你展示如果将不同的组件分配到不同的进程中。这里我们只演示android原生提供的多进程方式，通过jni fork的进程我们不做演示。
+ android原生为我们提供的多进程方式只有一种就是在```AndroidManifest.xml```中申明组件的时候加上 ```android:process``` 属性。
+ 默认情况下我们不申明这个属性的时候所有的组件都在同一个进程中。
+ ```android:processs```值有以下两种书写方式：
	1. ```android:process=":remote"```这样前面带有引号切不包含包名的写法表示该组件为应用的私有组件，外部无法通过任何形式访问组件。实际上该组件所在进程的名称为```<package>:remote```，例如我的组件名称就是```com.github.onlynight:remote```
    2. ```android:process="com.github.onlynight.remote"```这种不带引号的写法就表示该组件可被其他应用访问，且该组件所在进程的名称为```com.github.onlynight.remote```
+ demo 如下

```xml
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
    
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>
    <activity android:name=".SecondActivity"
              android:process=":remote"/>
    
    <service
        android:name=".ThirdService"
        android:enabled="true"
        android:exported="true"
        android:process="org.github.lion.multiprocesscomponent.remote"/>
</application>
```
+ 输出日志截图如下
![org.github.lion.multiprocesscomponent](https://github.com/onlynight/MultiProcessComponent/blob/master/images/org.github.lion.multiprocesscomponent.png)
![org.github.lion.multiprocesscomponent.remote](https://github.com/onlynight/MultiProcessComponent/blob/master/images/org.github.lion.multiprocesscomponent.remote.png)
![org.github.lion.multiprocesscomponent:remote](https://github.com/onlynight/MultiProcessComponent/blob/master/images/org.github.lion.multiprocesscomponent-remote.png)