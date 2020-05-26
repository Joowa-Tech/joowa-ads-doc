# Joowa Ads Android SDK 插屏接入文档

## 1. 接入前置要求

在接入 Joowa Ads Android SDK 插屏模块之前，请先确保完成 [Joowa Ads Android SDK 基础接入文档](Joowa%20Ads%20Android%20SDK%20基础接入文档.md)。

## 2. 配置广告位信息

在您应用的 `strings.xml`（如：`app/src/main/res/values/strings.xml`）中填入以下配置：

```
<resources >
    <!-- Joowa Interstitial Ids -->
    <string-array name="joowa_interstitial_ids" >

        <!-- 多个插屏广告位ID信息，可以创建多行 item -->

        <item >填写申请到的插屏广告位ID信息</item >
        <item >填写申请到的插屏广告位ID信息</item >
        <item >填写申请到的插屏广告位ID信息</item >

        <!-- ... -->

    </string-array >
</resources >
```
## 3. 在 Activity 中创建插屏广告实例

```
private JoowaInterstitial mJoowaInterstitial;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    JoowaAds.initMopub(this, new JoowaAdsInitializationListener() {
        @Override
        public void onInitializationFinished() {
            // 需要在初始化之后创建实例
            mJoowaInterstitial = new JoowaInterstitial(MainActivity.this);
        }
    });
}
```

## 4. 加载插屏广告

```
mJoowaInterstitial.load();
```

## 5. 判断插屏广告是否加载完毕

```
mJoowaInterstitial.isReady();
```

## 6. 播放插屏广告

```
mJoowaInterstitial.show();
```

## 8. 释放插屏实例

在 `Activity` 的 `onDestroy` 方法中释放插屏实例：

```
@Override
protected void onDestroy() {
    if (mInterstitial != null) {
        mInterstitial.destroy();
    }
    super.onDestroy();
}
```

## 9. 监听 Activity 声明周期

为了让广告SDK能正常使用，你需要在插屏广告示例所在 Activity 中，回调 Joowa SDK 对应的方法：

```
@Override
protected void onResume() {
    super.onResume();
    JoowaAds.onResume(this);
}

@Override
protected void onPause() {
    super.onPause();
    JoowaAds.onPause(this);
}

@Override
protected void onStop() {
    super.onStop();
    JoowaAds.onStop(this);
}
```


## 10. (可选)设置插屏广告回调监听器

```
private JoowaInterstitialListener mInterstitialListener = new JoowaInterstitialListener() {
    @Override
    public void onInterstitialLoaded() {
        Log.i("JoowaAdsSdkDemo", "插屏广告加载完毕");
    }
    
    @Override
    public void onInterstitialFailed(@NonNull JoowaErrorCode errorCode) {
        Log.i("JoowaAdsSdkDemo", "插屏广告加载失败");
    }
    
    @Override
    public void onInterstitialShown() {
        Log.i("JoowaAdsSdkDemo", "插屏广告展示成功");
    }
    
    @Override
    public void onInterstitialClicked() {
        Log.i("JoowaAdsSdkDemo", "插屏广告被点击");
    }
    
    @Override
    public void onInterstitialDismissed() {
        Log.i("JoowaAdsSdkDemo", "插屏广告消失");
    }
};

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    JoowaAds.initMopub(this, new JoowaAdsInitializationListener() {
        @Override
        public void onInitializationFinished() {
            mJoowaInterstitial = new JoowaInterstitial(MainActivity.this);
            mJoowaInterstitial.setListener(mInterstitialListener);
        }
    });
}
```

## 8. 补充说明

1. Joowa 封装了 Mopub 聚合广告，并移除了广告位（AdUnitId/AdPlacement）的相关概念。
2. 在调用 `load` / `isReady` / `show` 等方法时，SDK内部都会选择在合适的时机去预加载更多广告，开发者无需处理。