# Joowa Ads Android SDK 激励视频接入文档

## 1. 重要

在接入 Joowa Ads Android SDK 激励视频模块之前，请先确保完成 [Joowa Ads Android SDK 基础接入文档](Joowa%20Ads%20Android%20SDK%20基础接入文档.md)

## 2. 配置广告位信息

在您应用的 `strings.xml`（如：app/src/main/res/valuse/strings.xml）中填入以下配置：

```
<resources >
    <!-- Joowa Rewarded Video Ids -->
    <string-array name="joowa_rewarded_video_ids" >

        <!-- 多个激励视频广告位ID信息，可以创建多行 item -->

        <item >填写申请到的激励视频广告位ID信息</item >
        <item >填写申请到的激励视频广告位ID信息</item >
        <item >填写申请到的激励视频广告位ID信息</item >

        <!-- ... -->

    </string-array >
</resources >
```

## 3. 加载激励视频广告

```
JoowaRewardedVideo.load();
```

## 4. 判断激励视频广告是否加载完毕

```
JoowaRewardedVideo.isReady();
```

## 5. 展示激励视频广告

```
JoowaRewardedVideo.show();
```

## 6. 监听 Activity 生命周期

为了让广告SDK能正常使用，你需要在你有以下行为的 Activity 中

* `JoowaRewardedVideo.load()` 
* `JoowaRewardedVideo.show` 
* `JoowaRewardedVideo.isReady` 
  
回调 Joowa SDK 对应的方法：

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    JoowaAds.onCreate(this);
}

@Override
protected void onStart() {
    super.onStart();
    JoowaAds.onStart(this);
}

@Override
protected void onRestart() {
    super.onRestart();
    JoowaAds.onRestart(this);
}

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

@Override
protected void onDestroy() {
    super.onDestroy();
    JoowaAds.onDestroy(this);
}

@Override
public void onBackPressed() {
    super.onBackPressed();
    JoowaAds.onBackPressed(this);
}
```


## 7. (可选)设置激励视频广告回调监听器

```
JoowaRewardedVideoListener listener = new JoowaRewardedVideoListener() {
    @Override
    public void onRewardedVideoLoadSuccess() {
        Log.i("JoowaAdsSdkDemo", "激励视频广告加载成功");
    }
    
    @Override
    public void onRewardedVideoLoadFailure(@NonNull JoowaErrorCode errorCode) {
        Log.i("JoowaAdsSdkDemo", "激励视频广告加载失败: " + errorCode.toString());
    }
    
    @Override
    public void onRewardedVideoStarted() {
        Log.i("JoowaAdsSdkDemo", "激励视频广告播放开始");
    }
    
    @Override
    public void onRewardedVideoPlaybackError(@NonNull JoowaErrorCode errorCode) {
        Log.i("JoowaAdsSdkDemo", "激励视频广告播放失败: " + errorCode.toString());
    }
    
    @Override
    public void onRewardedVideoClicked() {
        Log.i("JoowaAdsSdkDemo", "激励视频广告触发点击");
    }
    
    @Override
    public void onRewardedVideoClosed() {
        Log.i("JoowaAdsSdkDemo", "激励视频广告触发关闭");
    }
    
    @Override
    public void onRewardedVideoCompleted() {
        Log.i("JoowaAdsSdkDemo", "激励视频广告播放完成");
    }
};
JoowaRewardedVideo.setListener(listener);
```

*ps:  JoowaRewardedVideo.setListener(null); 即可取消监听器*

## 8. 补充说明

1. Joowa 封装了 Mopub 聚合广告，并移除了广告位（AdunitId/AdPlacement）的相关概念。
2. 在调用 `load` / `isReady` / `show` 等方法时，SDK内部都会选择在合适的时机去预加载更多广告。