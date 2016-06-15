## AndroidManifest.xml

### 内容
* package包名
* uses-sdk sdk版本需求
* uses-permission 权限控制
* application 具体针对该app的控制
* application name 应用名
* application theme 主题设置
* activity
    * name activity名
    * launchMode [launchMode参考](http://blog.csdn.net/liuhe688/article/details/6754323)
          1.standard
          2.singleTop
          3.singleTask
          4.singleInstance
    * intent-filter

### 具体

    这是需要第一个看的文件，一眼就能知道这个app的复杂程度了。
    与刚刚接触的Manifest是一样的作用，入口，描述性文件。
```
    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.mgtv_standard"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk
        android:minSdkVersion="14"
        android:targetSdkVersion="22" />

    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />

    <application
        <!-- 应用的全局入口 -->
        android:name="com.mgtv.base.DemoApplication"
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"

        android:theme="@android:style/Theme.NoTitleBar.Fullscreen" >
        <activity
            android:name="com.mgtv.main.MainActivity"
            android:label="@string/app_name"
            android:screenOrientation="landscape" >
            <intent-filter>
                <!-- 通过该标签找到应用的action入口 -->
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name="com.mgtv.dianbo.DianboPlayer"
            <!-- 设置launchMode模式为当前task中只启动一次activity -->
            android:launchMode="singleTask">
        </activity>
        <activity
            <!-- 设置launchMode模式为当前task中只启动一次activity -->
            android:name="com.mgtv.dianbo.MoviewDetail"
            android:launchMode="singleTask" >
        </activity>
        <activity
            <!-- 设置launchMode模式为当前task中只启动一次activity -->
            android:name="com.mgtv.dianbo.DianboAbout"
            android:launchMode="singleTask" >
        </activity>
        <activity
            <!-- 设置launchMode模式为当前task中只启动一次activity -->
            android:name="com.mgtv.dianbo.ChooseEpisode"
            android:launchMode="singleTask" >
        </activity>
        <activity
            <!-- 设置launchMode模式为当前task中只启动一次activity -->
            android:name="com.mgtv.lunbo.Lunbo"
            android:launchMode="singleTask"/>
    </application>

    </manifest>
```
