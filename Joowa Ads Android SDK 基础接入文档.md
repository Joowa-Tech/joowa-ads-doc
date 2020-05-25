# Joowa Ads Android SDK 接入文档

Jowoa Ads Android SDK（后续简称 Joowa SDK） 在 Mopub SDK & AppsFlyer 基础上进行二次封装。开发者只需要进行简单的配置工作，即可享受 Joowa 提供的优化服务，包括但不限：

1. 自动创建对应聚合广告平台账号并填充广告
2. 由 Joowa 运营调整广告填充策略，以提高的填充率和eCPM

SDK 接入步骤概览：

* 申请 Joowa 开发者账号（需要和商务经理沟通获取）
* 申请 Joowa 应用配置（需要和商务经理沟通获取）
* 导入 Joowa SDK 到项目
* 完成 Joowa SDK 构建配置
* 完成 Joowa SDK 其他配置
* 添加网络安全配置
* 初始化 SDK
* 接入需要的广告形式
  * 激励视频广告
  * 插屏广告
* 提交 APK 给 Joowa 测试并申请开通正式广告账号
* 待广告账号开通后即可正式才产生收益

## 1. 申请开发者账号

* 开发者第一次接入 Joowa 时，需要申请 Joowa 开发者账号。后续接入将不用再次进行此步骤。
* 申请开发者账号后，可以获取 Joowa 开发者唯一标识。在 Joowa SDK 初始化的时候，需要传入 Joowa 开发者唯一标识。
* 开发者账号和唯一标识可通过和商务经理对接申请获取。

## 2. 申请 Joowa 应用配置

对于每一个包名，开发者都需要为其申请 Joowa 应用配置。申请流程如下：

1. 贵方商务经理将贵应用包名发送给 Joowa 商务经理。
2. Joowa 将在1个工作日内完成应用的相关基础配置，并返回配置信息（在后续的 Joowa SDK 对接中需要用上）
3. 开发者继续接入 Joowa SDK

## 3. 导入 Joowa SDK 到项目

将 `joowa-ads-xxx.aar` 和 `joowa-ads-xxx.pom` 复制到应用模块的 `libs` 中，具体路径为 `libs/com/joowa/joowa-ads/xxx`

e.g.

```
app            
└── libs
    └── com
        └── joowa
            └── joowa-ads
                └── xxx
                    ├── joowa-ads-xxx.aar
                    └── joowa-ads-xxx.pom
```

**请注意替换 `xxx` 为实际的sdk的版本号**

## 4. 完成 Joowa SDK 构建配置

在您的应用的应用构建模块中（如：`app/build.gradle`)加入以下配置即可

```
android {

    // ...

    defaultConfig {
        // （可选）开启 Multi Dex 以支持多个SDK
        multiDexEnabled true
    }

    // 配置 Java 编译选项
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

// 添加仓库地址
repositories {
    jcenter()
    mavenCentral()
    maven { url "https://s3.amazonaws.com/moat-sdk-builds" }
    maven { url 'https://dl.bintray.com/ironsource-mobile/android-sdk' }
    maven { url 'https://adcolony.bintray.com/AdColony' }
    maven { url 'https://jitpack.io' }
    maven { url 'https://chartboostmobile.bintray.com/Chartboost' }

  	// 指定项目的 libs 目录为 maven 仓库，方便依赖 JoowaAdsSDK 以及 JoowaAdsSDK 所依赖的包
	maven {
		url uri("${projectDir}/libs")
	}
}

dependencies {
    // 注意替换 xxx 为实际的版本号
    implementation('com.joowa:joowa-ads:xxx@aar') {
        transitive = true
    }
}
```

## 5. 完成 Joowa SDK 其他配置

1. 在您应用的 `strings.xml`（如：`app/src/main/res/values/strings.xml`）中填入以下配置：

    ```
    <resources >
        <!-- Joowa AppsFlyer Dev Key-->
        <string name="joowa_af_id" >填写申请到的 AppsFlyer Dev Key</string >
    </resources >
    ```

2. 在您的 `AndroidManifest.xml` 的 `<application>` 标签中添加以下内容：
    * 开启硬件加速（为了能播放Vungle中的部分广告）
    * Admob 广告的 Application Id
    * AppLovin SDK Key

    e.g.

    ```
    <manifest>
        <application
            android:hardwareAccelerated="true"
            >
            
            <!-- Admob Application Id -->
            <meta-data
                android:name="com.google.android.gms.ads.APPLICATION_ID"
                android:value="这里填写申请到的 Admob Application Id" />

            <!-- AppLovin SDK Key -->
            <meta-data
                android:name="applovin.sdk.key"
                android:value="这里填写申请到的 AppLovin SDK Key" />
        </application>
    </manifest>
    ```

## 6. 添加网络安全配置

从 Android 9.0 (API 28) 开始，应用默认禁止非Https的网络请求。为了能使用部分依旧还在使用 HTTP 请求的广告服务，需要添加额外的网络安全配置。步骤如下：

1. 添加网络安全配置（如：`res/xml/network_security_config.xml`）：

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>

        <!-- 默认允许Http请求 -->
        <base-config cleartextTrafficPermitted="true">
            <trust-anchors>
                <certificates src="system"/>
            </trust-anchors>
        </base-config>

        <!-- 指定的域名只能用Https请求，一般可用于配置贵方的后端服务 -->
        <domain-config cleartextTrafficPermitted="false">
            <domain includeSubdomains="true">example.com</domain>
            <domain includeSubdomains="true">cdn.example2.com</domain>
        </domain-config>

        <!-- Facebook 广告网络使用设备 localhost 去缓存广告数据，因此需要指定此地址能进行明文传输 -->
        <domain-config cleartextTrafficPermitted="true">
            <domain includeSubdomains="true">127.0.0.1</domain>
        </domain-config>

    </network-security-config>
    ```

2. 在您的 AndroidManifest.xml 文件中引用刚刚的网络安全配置：

    ```
    <manifest>
        <application
            ...
            android:networkSecurityConfig="@xml/network_security_config"
            ...>
        </application>
    </manifest>
    ```
 
## 7. 初始化 Joowa SDK


1. 创建自定义的 Application 类，在 `Application.onCreate()` 方法中，调用以下代码：

```
JoowaAds.initJoowa(String devKey);
JoowaAds.initAppsFlyer(Context context);
```
e.g.

```
/**
 * ps: 请记得在 AndroidManifest.xml 中的 application 标签中声明使用 MyApplication 
 */
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        JoowaAds.initJoowa("这里填写开发者key");
        JoowaAds.initAppsFlyer(this);
    }
}
```

2. 在您的 Activity 中调用以下代码，建议在首个 Activity 中调用初始化代码

```
JoowaAds.initMoPub(Activity activity, JoowaAdsInitializationListener listener);
```

e.g. :

```
public class MainActivity extends Activity{

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        JoowaAds.initMoPub(this, new JoowaAdsInitializationListener() {
            @Override
            public void onInitializationFinished() {
                // SDK 初始化完毕，后续建议步骤（可选）：
                // * 展示 GDPR 确认对话框
                // * 预加载激励视频广告
                // * 预加载插屏广告
                
            }
        });
    }
```

注意：

1. 在展示 GDPR 确认对话框的相关方法之前，必须先等候 `JoowaAds#initMoPub` 初始化成功。
1. 在执行 Joowa 插屏、激励视频的相关方法之前，必须先等候 `JoowaAds#initMoPub` 初始化成功。

## 8. 后续步骤

* 接入[激励视频广告](Joowa%20Ads%20Android%20SDK%20激励视频接入文档.md)
* 接入[插屏广告](Joowa%20Ads%20Android%20SDK%20插屏接入文档.md)

## 9. （可选）GDPR

《通用数据保护条例》（Gneral Data Protection Regulation，GDPR）是欧盟出台的数据保护方案。关于更多 GDPR 理解，可以参考 [这里](http://www.woshipm.com/pmd/1024908.html)。

如果您的产品面向欧盟用户，那么我们建议您的应用/游戏按照 Mopub 的 GDPR 引导流程去保证 SDK 遵守 GDPR 规范。

> * https://developers.mopub.com/publishers/best-practices/gdpr-guide/
> * https://developers.mopub.com/publishers/android/gdpr/

一个简单的例子如下：

```
JoowaAds.initMopub(this, new JoowaAdsInitializationListener() {
    @Override
    public void onInitializationFinished() {
        // GDPR 处理（必须在初始化之后才能生效）

        // 1. 检查是否需要展示确认对话框
        if (MoPub.getPersonalInformationManager() != null &&
            MoPub.getPersonalInformationManager().shouldShowConsentDialog()) {

            // 2. 如果需要，就开始确认对话框加载
            MoPub.getPersonalInformationManager().loadConsentDialog(new ConsentDialogListener() {
                @Override
                public void onConsentDialogLoaded() {
                    // 3. 加载成功之后展示给用户
                    MoPub.getPersonalInformationManager().showConsentDialog();
                }
                
                @Override
                public void onConsentDialogLoadFailed(@NonNull MoPubErrorCode moPubErrorCode) {
                    Log.i("JoowaAdsDemo", "加载失败");
                }
            });
        }
    }
});
```

## 10. 其他说明

### 10.1 Joowa SDK 最低版本要求

Android 5.0 (minSdkVersion 21)。

### 10.2 Joowa SDK 混淆配置

Joowa SDK（AAR） 已经内置配好混淆配置，开发者无需额外操作。

### 10.3 AndroidX 依赖

部分广告 SDK 依赖 AndroidX Library，需要在你项目的 `gradle.properties` 中加入使用AndroidX的配置

```
# AndroidX package structure to make it clearer which packages are bundled with the
# Android operating system, and which are packaged with your app's APK
# https://developer.android.com/topic/libraries/support-library/androidx-rn
android.useAndroidX=true

# Automatically convert third-party libraries to use AndroidX
android.enableJetifier=true
```

### 10.4 移除部分权限

Joowa SDK 中内置了部分可选权限：

* 定位权限
	* ACCESS_COARSE_LOCATION
	* ACCESS_FINE_LOCATION
* 读取设备信息权限
	* READ_PHONE_STATE

如果您的应用不需要上述权限，那么可以在您的应用的 `AndroidMainfest.xml` 中，参照下面写法移除权限：

```
<manifest >
    <uses-permission
        android:name="android.permission.ACCESS_COARSE_LOCATION"
        tools:node="remove" />
    <uses-permission
        android:name="android.permission.ACCESS_FINE_LOCATION"
        tools:node="remove" />
     <uses-permission
        android:name="android.permission.READ_PHONE_STATE"
        tools:node="remove" />
</manifest >
```

### 10.5 备份规则

如果您在 `AndroidManifest.xml` 的 `<application>` 标签内添加 `android:fullBackupContent="true"` ，您可能会收到错误提示：

```
Manifest merger failed : Attribute application@fullBackupContent value=(true)
```

要解决此错误，请在 `AndroidManifest.xml` 文件的 `<application>` 标签中添加

```
tools:replace="android:fullBackupContent"
```

如果您指定了自己的备份规则，请通过添加以下规则将它们与 AppsFlyer 规则手动合并：

```
<full-backup-content>
    ...//your custom rules
    <exclude domain="sharedpref" path="appsflyer-data"/>
</full-backup-content>
```

