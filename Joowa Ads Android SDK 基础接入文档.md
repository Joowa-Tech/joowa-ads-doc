# Joowa Ads Android SDK 接入文档

Jowoa Ads Android SDK（后续简称 Joowa SDK） 在 Mopub SDK & AppsFlyer 基础上进行二次封装。开发者只需要进行简单的配置工作，即可享受 Joowa 提供的优化服务，包括但不限：

1. 自动创建对应聚合广告平台账号并填充广告
2. 由 Joowa 运营调整广告填充策略，以提高的填充率和eCPM

## SDK 接入步骤概览

* 申请 Joowa 开发者账号（需要和商务经理沟通获取）
* 配置并引入 Joowa SDK 相关依赖
* 添加网络安全配置
* 配置 Admob Application ID（需要和商务经理沟通获取）
* 初始化 SDK
* 接入对应的广告形式：
  * 激励视频广告
  * 插屏广告（未支持）
* 提交 APK 给 Joowa 测试并申请开通正式广告账号
* 待广告账号开通后即可正式才产生收益

### 申请开发者账号

申请开发者账号后，可以获取开发者唯一标识。

开发者唯一标识，在 Joowa SDK 初始化的时候，需要传入，以便 Joowa SDK 通过此标识找到应用（/渠道）包的相关信息并进行初始化。

开发者账号和唯一标识可通过和商务经理对接申请获取。

### 配置并引入 Joowa SDK 相关依赖

在您的应用的应用构建模块中（如：app/build.gradle)加入以下依赖配置即可

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
}

dependencies {
    // 加入 Joowwa Ads Android SDK
    implementation('com.joowa:ads:1.0.0') {
        transitive = true
    }
}
```

### 添加网络安全配置

> * https://developers.mopub.com/publishers/android/integrate/#step-4-add-a-network-security-configuration-file
> * https://developers.mopub.com/publishers/mediation/networks/facebook/#localhost-whitelisting

从 Android 9.0 (API 28) 开始，应用默认禁止非Https的网络请求。为了能使用部分依旧还在使用 HTTP 请求的广告服务，需要添加额外的网络安全配置。

1. 添加网络安全配置（如：在res/xml/network_security_config.xml）：

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>
        ...

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
        ...
    </network-security-config>
    ```

2. 在你的 AndroidManifest.xml 文件中引用刚刚的网络安全配置：

    ```
    <manifest>
        <application
            ...
            android:networkSecurityConfig="@xml/network_security_config"
            ...>
        </application>
    </manifest>
    ```

### 配置 Admob Application ID

1. 通过商务经理去拿到 **此应用包名** 所对应的 Admob 广告的 Application Id
2. 在您的 AndroidManifest.xml 的 application 标签中添加 Admob 广告的 Application Id

    ```
    <manifest>
        <application>
            <meta-data
                android:name="com.google.android.gms.ads.APPLICATION_ID"
                android:value="这里填写申请到的 Application Id" />
        </application>
    </manifest>
    ```

###  初始化 Joowa SDK

通过调用一下代码以完成 SDK 初始化配置

```
JoowaAds.init(Context context, String developerKey, JoowaAdsInitializationListener listener);
```

e.g. :

```
JoowaAds.init(this, "your developer key", new JoowaAdsInitializationListener() {
    @Override
    public void onInitializationFinished() {
        // Joowa ads sdk complete initialization.
    }
});
```

注意：

1. 在执行所有其他 Joowa SDK 的方法之前，必须先完成初始化调用

## 后续步骤

* 接入[激励视频广告](Joowa%20Ads%20Android%20SDK%20激励视频接入文档.md)
* 接入插屏广告（未支持）

## 其他说明

### 混淆配置

Joowa Ads Android SDK（AAR） 已经内置配好混淆配置，开发者无需额外操作

### Joowa Ads Android SDK 最低版本要求

Android 5.0 (minSdkVersion 21)

### 移除部分权限

Joowa SDK 中内置了部分可选权限：

* 定位权限
	* ACCESS_COARSE_LOCATION
	* ACCESS_FINE_LOCATION
* 读取设备信息权限
	* READ_PHONE_STATE

如果你的应用不需要上述权限，那么可以在你的应用的 `AndroidMainfest.xml` 中，通过下面写法移除权限：

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

### 备份规则

如果您在 `AndroidManifest.xml` 的 `<application>` 标签内添加 `android:fullBackupContent="true"` ，您可能会收到错误提示：

```
Manifest merger failed : Attribute application@fullBackupContent value=(true)
```

要解决此错误，请在 `AndroidManifest.xml` 文件的 `<application>` 标签中添加

```
tools:replace="android:fullBackupContent"
```

如果您指定了自己的备份规则，请通过添加以下规则将它们与AppsFlyer规则手动合并：

```
<full-backup-content>
    ...//your custom rules
    <exclude domain="sharedpref" path="appsflyer-data"/>
</full-backup-content>
```

