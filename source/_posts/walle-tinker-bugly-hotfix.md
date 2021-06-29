---
title: Walle + Tinker + Bugly 多渠道热更新方案
date: 2020-10-21 01:50:38
toc: true
categories:
 - Android
tags:
 - Hotfix
---

### 1.Walle

#### 1.1 接入

在 Project 级的 `build.gradle` 内加入：

{% codeblock "build.gradle" lang:groovy %}
dependencies {
    ...
    classpath 'com.meituan.android.walle:plugin:1.1.7'
    ...
}
{% endcodeblock %}

<!-- more -->

再在 app moudle 内的 `build.gradle` 中加入：

{% codeblock "build.gradle" lang:groovy %}
apply from: 'walle.gradle'

android {
    ...
    signingConfigs {
        release {
            keyAlias 'keyAlias'
            keyPassword 'keyPassword'
            storeFile file('storeFile path')
            storePassword 'storePassword'
        }
    }
    
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
            ...
        }
    }
}

dependencies {
    ...
    // 美团Walle多渠道打包
    implementation 'com.meituan.android.walle:library:1.1.7'
    ...
}
{% endcodeblock %}

app moudle 内新建一个 `walle.gradle`：

{% codeblock "walle.gradle" lang:groovy %}
apply plugin: 'walle'

android {
    walle {
        // 指定渠道包的输出路径
        apkOutputFolder = new File("${project.buildDir}/outputs/channels")
        // 定制渠道包的APK的文件名称
        apkFileNameFormat = '${projectName}-v${versionName}-${channel}-${buildTime}.apk'
        // 渠道配置文件
        channelFile = new File("${project.getProjectDir()}/channel")
    }
}
{% endcodeblock %}

同文件夹内 新建一个叫 `channel` 的文件，填写渠道名，每行一个，例如：

{% codeblock "channel" %}
Tencent
Oppo
Vivo
HuaWei
XiaoMi
QiHu
AppChina
QinYu
Dev
{% endcodeblock %}

#### 1.2 使用

使用如下命令打包：

```gradle
// 所有渠道
gradlew clean assembleReleaseChannels
// 指定渠道
gradlew aseembleReleaseChannels -PchannelList=渠道名
```

打出的渠道包在：`build/outputs/channels/` 中，路径可配。



### 2. Tinker + Bugly

#### 2.1 接入

在 Project 级的 `build.gradle` 内加入：

{% codeblock "build.gradle" lang:groovy %}
dependencies {
    ...
    classpath "com.tencent.bugly:tinker-support:1.2.1"
    ...
}
{% endcodeblock %}

再在 app moudle 内的 `build.gradle` 中加入：

{% codeblock "build.gradle" lang:groovy %}
apply from: 'tinker-support.gradle'

android {
    ...
    defaultConfig {
        ...
        multiDexEnabled true
        ...
    }
    
    dexOptions {
        jumboMode = true
    }
}

dependencies {
    ...
    implementation 'com.tencent.bugly:crashreport_upgrade:1.4.5'
    implementation 'com.tencent.tinker:tinker-android-lib:1.9.14.9'
    ...
}
{% endcodeblock %}

app moudle 内新建一个 `tinker-support.gradle`：

{% codeblock "tinker-support.gradle" lang:groovy %}
apply plugin: 'com.tencent.bugly.tinker-support'

def bakPath = file("${buildDir}/bakApk/")

/**
 * 此处填写每次构建生成的基准包目录
 */
 def baseApkDir = "app-1019-18-18-37"

/**
 * 对于插件各参数的详细解析请参考
 */
 tinkerSupport {

    // 开启tinker-support插件，默认值true
    enable = true

    // 指定归档目录，默认值当前module的子目录tinker
    autoBackupApkDir = "${bakPath}"

    // 是否启用覆盖tinkerPatch配置功能，默认值false
    // 开启后tinkerPatch配置不生效，即无需添加tinkerPatch
    overrideTinkerPatchConfiguration = true

    // 编译补丁包时，必需指定基线版本的apk，默认值为空
    // 如果为空，则表示不是进行补丁包的编译
    // @{link tinkerPatch.oldApk }
    baseApk = "${bakPath}/${baseApkDir}/app-release.apk"

    // 对应tinker插件applyMapping
    baseApkProguardMapping = "${bakPath}/${baseApkDir}/app-release-mapping.txt"

    // 对应tinker插件applyResourceMapping
    baseApkResourceMapping = "${bakPath}/${baseApkDir}/app-release-R.txt"

    // 构建基准包和补丁包都要指定不同的tinkerId，并且必须保证唯一性
    tinkerId = "1.0-patch-1"

    // 构建多渠道补丁时使用
    // buildAllFlavorsDir = "${bakPath}/${baseApkDir}"

    // 是否启用加固模式，默认为false.(tinker-spport 1.0.7起支持）
    // isProtectedApp = true

    // 是否开启反射Application模式
    enableProxyApplication = false

    // 是否支持新增非export的Activity（注意：设置为true才能修改AndroidManifest文件）
    supportHotplugComponent = true
 }

/**
 * 一般来说,我们无需对下面的参数做任何的修改
 * 对于各参数的详细介绍请参考:
 * https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97
 */
 tinkerPatch {
    //oldApk ="${bakPath}/${appName}/app-release.apk"
    ignoreWarning = false
    useSign = true
    dex {
        dexMode = "jar"
        pattern = ["classes*.dex"]
        loader = []
    }
    lib {
        pattern = ["lib/*/*.so"]
    }

    res {
        pattern = ["res/*", "r/*", "assets/*", "resources.arsc", "AndroidManifest.xml"]
        ignoreChange = []
        largeModSize = 100
    }

    packageConfig {
    }
    sevenZip {
        zipArtifact = "com.tencent.mm:SevenZip:1.1.10"
        //path = "/usr/local/bin/7za"
    }
    buildConfig {
        keepDexApply = false
        //tinkerId = "1.0.1-base"
        //applyMapping = "${bakPath}/${appName}/app-release-mapping.txt" //  可选，设置mapping文件，建议保持旧apk的proguard混淆方式
        //applyResourceMapping = "${bakPath}/${appName}/app-release-R.txt" // 可选，设置R.txt文件，通过旧apk文件保持ResId的分配
    }
 }
 {% endcodeblock %}

#### 2.2 初始化

##### 2.2.1 enableProxyApplication = false

> 这是Tinker推荐的接入方式，一定程度上会增加接入成本，但具有更好的兼容性。

在以上 `tinker-support.gradle` 中 `enableProxyApplication = false` 的情况下：

自定义 Application，：

{% codeblock "SampleApplication.java" lang:java %}
public class SampleApplication extends TinkerApplication {

    public SampleApplication() {
        super(ShareConstants.TINKER_ENABLE_ALL, "xxx.xxx.xxx.SampleApplicationLike",
                "com.tencent.tinker.loader.TinkerLoader", false, true);
    }
}
{% endcodeblock %}

> 注意：**这个类集成TinkerApplication类，这里面不做任何操作，所有Application的代码都会放到ApplicationLike继承类当中**
> 参数解析
> 参数1：tinkerFlags 表示Tinker支持的类型 dex only、library only or all suuport，default: TINKER_ENABLE_ALL
> 参数2：delegateClassName Application代理类 这里填写你自定义的ApplicationLike
> 参数3：loaderClassName Tinker的加载器，使用默认即可
> 参数4：tinkerLoadVerifyFlag 加载dex或者lib是否验证md5，默认为false
> 参数5：useDelegateLastClassLoaderOnAPI29AndAbove 在API29及以上使用 DelegateLastClassLoader

将 `AndroidManifest` 中 `application` 节点的 `android:name` 属性设置为该自定义 Application. 

{% codeblock "AndroidManifest.xml" lang:xml %}
<application
    android:name=".SampleApplication">
    ...
</application>
{% endcodeblock %}

自定义 ApplicationLike ：

{% codeblock "SampleApplicationLike.java" lang:java %}
public class SampleApplicationLike extends DefaultApplicationLike {

    private static final String TAG = "ApplicationLike";
    
    public AppLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag,
                   long applicationStartElapsedTime, long applicationStartMillisTime,
                   Intent tinkerResultIntent) {
        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime,
                applicationStartMillisTime, tinkerResultIntent);
    }
    
    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH)
    @Override
    public void onBaseContextAttached(Context base) {
        super.onBaseContextAttached(base);
        MultiDex.install(base);
        Beta.installTinker(this);
    }
    
    @Override
    public void onCreate() {
        super.onCreate();
        // 补丁回调接口
        Beta.betaPatchListener = new BetaPatchListener() {
            @Override
            public void onPatchReceived(String patchFile) {
                Log.d(TAG, "补丁下载地址: " + patchFile);
            }
    
            @Override
            public void onDownloadReceived(long savedLength, long totalLength) {
                Log.d(TAG, String.format(Locale.getDefault(), "%s %d%%",
                                Beta.strNotificationDownloading,
                                (int) (totalLength == 0 ? 0 : savedLength * 100 / totalLength)));
            }
    
            @Override
            public void onDownloadSuccess(String msg) {
                Log.d(TAG, "补丁下载成功: " + msg);
            }
    
            @Override
            public void onDownloadFailure(String msg) {
                Log.d(TAG, "补丁下载失败: " + msg);
            }
    
            @Override
            public void onApplySuccess(String msg) {
                Log.d(TAG, "补丁应用成功: " + msg);
            }
    
            @Override
            public void onApplyFailure(String msg) {
                Log.e(TAG, "补丁应用失败: " + msg);
            }
    
            @Override
            public void onPatchRollback() {
                Log.d(TAG, "补丁回滚");
            }
        };
        
        // 接入 Walle 后可以在这里设置渠道(可选)
        //String channel = WalleChannelReader.getChannel(getApplication(), "Dev");
        //Bugly.setAppChannel(getApplication(), channel);
        
        // 是否为开发设备(可选)
        Bugly.setIsDevelopmentDevice(getApplication(), true);
    
        // 这里实现SDK初始化，appId替换成你的在Bugly平台申请的appId
        // 调试时，将第三个参数改为true
        Bugly.init(getApplication(), "BUGLY_APP_ID", true);
    }
    
    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH)
    public void registerActivityLifecycleCallback(Application.ActivityLifecycleCallbacks callbacks) {
        getApplication().registerActivityLifecycleCallbacks(callbacks);
    }
    
    @Override
    public void onTerminate() {
        super.onTerminate();
        Beta.unInit();
    }
}
{% endcodeblock %}

> 注意：Tinker 需要开启 MultiDex；
> SampleApplicationLike 这个类是 Application 的代理类，以前所有在 Application 的实现必须要全部拷贝到这里，在 `onCreate` 方法调用SDK的初始化方法，在 `onBaseContextAttached` 中调用 `Beta.installTinker(this);` 。

##### 2.2.2 enableProxyApplication = true

{% codeblock "App.java" lang:java %}
public class App extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // 这里实现SDK初始化，appId替换成你的在Bugly平台申请的appId
        // 调试时，将第三个参数改为true
        Bugly.init(this, "BUGLY_APP_ID", true);
    }
    
    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        // you must install multiDex whatever tinker is installed!
        MultiDex.install(base);
    
        // 安装tinker
        Beta.installTinker();
    }
}
{% endcodeblock %}

> 注：无须你改造Application，主要是为了降低接入成本，我们插件会动态替换 AndroidMinifest 文件中的 Application 为我们定义好用于反射真实Application的类（需要您接入**SDK 1.2.2版本** 和 **插件版本 1.0.3**以上）。

#### 2.3 AndroidManifest 配置

##### 2.3.1 权限配置

{% codeblock "AndroidManifest.xml" lang:xml %}
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_LOGS" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
{% endcodeblock %}

##### 2.3.2 Actvity 配置

{% codeblock "AndroidManifest.xml" lang:xml %}
<activity
    android:name="com.tencent.bugly.beta.ui.BetaActivity"
    android:configChanges="keyboardHidden|orientation|screenSize|locale"
    android:theme="@android:style/Theme.Translucent" />
{% endcodeblock %}

##### 2.3.3 配置 FileProvider

> 注意：如果您想兼容 `Android N` 或者以上的设备，必须要在 `AndroidManifest.xml` 文件中配置 `FileProvider` 来访问共享路径的文件。

{% codeblock "AndroidManifest.xml" lang:xml %}
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="${applicationId}.fileProvider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths" />
</provider>
{% endcodeblock %}

如果你使用的第三方库也配置了同样的 `FileProvider`, 可以通过继承 `FileProvider` 类来解决合并冲突的问题，示例如下：

{% codeblock "AndroidManifest.xml" lang:xml %}
<provider
    android:name=".utils.BuglyFileProvider"
    android:authorities="${applicationId}.fileProvider"
    android:exported="false"
    android:grantUriPermissions="true"
    tools:replace="name,authorities,exported,grantUriPermissions">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths"
        tools:replace="name,resource"/>
</provider>
{% endcodeblock %}

在 `res` 目录新建 `xml` 文件夹，创建 `provider_paths.xml` 文件如下：

{% codeblock "provider_paths.xml" lang:xml %}
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- /storage/emulated/0/Download/xxx.xxx.xxx/.beta/apk-->
    <external-path name="beta_external_path" path="Download/"/>
    <!--/storage/emulated/0/Android/data/xxx.xxx.xxx/files/apk/-->
    <external-path name="beta_external_files_path" path="Android/data/"/>
</paths>
{% endcodeblock %}

> **注：Tinker 1.3.1 及以上版本，可以不用进行以上配置，aar 已经在 AndroidManifest 配置了，并且包含了对应的资源文件。**

#### 2.4 使用

首先设置 `tinker-support.gradle` 中的 `tinkerId` ，要确保 `tinkerId` 的唯一性，且不要与 App 版本号相同，使用 Walle 打包的同时，也会在 bakApk 里生成 基准包，如果发布之后发现Bug，则修改 `tinkerId` ，将 `tinker-support.gradle` 中的 `baseApkDir` 修改为 bakApk 文件夹中 基准包 的文件夹名，然后使用

```gradle
gralew buildTinkerPatchRelease
```

打出补丁，补丁文件在 `app\build\outputs\patch\release` 文件夹内，再使用 Bugly 后台上传，[官方文档](https://bugly.qq.com/docs/user-guide/instruction-manual-android-hotfix-demo/?v=20200622202242)

### 3.踩过的坑

#### 3.1 ~~gradle build tools 与 gradle 的版本不能太高~~

~~Bugly 的 Tinker Support 目前还不兼容 build tool 3.2.0 以上的版本，gradle 版本 4.6 以上也不兼容，否则会报各种错误~~

```groovy
classpath 'com.android.tools.build:gradle:3.2.0'

distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip
```

#### 3.2 ~~Java 8 语法~~

~~同时如果项目的编译语法设置为 **Java 8** 的话，会抛出如下错误：~~

```
Java 8 language support, as requested by 'android.enableD8.desugaring= true' in your gradle.properties file, is not supported when 'android.useDexArchive= false'.
```

~~需要在 `Project Properties` 级的 `gradle.properties` 中加入：~~

```groovy
android.enableD8.desugaring=false
android.useDexArchive= true
```

### 4. Demo 地址

[Github](https://github.com/WangZhiYao/TinkerTest)

基准包在 **base**  分支，修复包在 **patch** 分支

### 更新
2020.11.07 ：

根据 tinker 官方 [github issue](https://github.com/BuglyDevTeam/Bugly-Android-Demo/issues/247#issuecomment-656568646) 中 其他人的配置：
```groovy
classpath 'com.android.tools.build:gradle:3.4.1'

distributionUrl=https\://services.gradle.org/distributions/gradle-5.1.1-all.zip

implementation 'com.tencent.bugly:crashreport_upgrade:1.4.5' 
implementation 'com.tencent.tinker:tinker-android-lib:1.9.14.5' 
```

这样配置解决了 build gradle tool 版本过低导致 Java 8 语法的问题

app minSdkVersion 低于19 ，`multiDexEnabled  = true` 的时候，如果报出 xxxxloader.class 之类的不在 mianDex 时，需要手动去设置 mainDexFile，将之前报的 class 手动分配到 mainDex 中

app minSdkVersion >= 21 时，打补丁包同样会报错，因为 google 在 api >= 21 之后 mainDexFile 已经不生效了，这时需要在 `tinker-support.gradle` 中加入

{% codeblock "tinker-support.gradle" lang:groovy %}
tinkerSupport {
    ...
    // Android API 21 以上 无法自定义mainDexList，导致loader分到次dex中Tinker报错无法生成补丁，忽略即可
    // https://github.com/Tencent/tinker/issues/1084
    ignoreWarning = true
    ...
}
{% endcodeblock %}
