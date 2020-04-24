# Joowa Ads Android SDK 激励视频接入文档

## 1. 重要

在接入 Joowa Ads Android SDK 激励视频模块之前，请先确保完成 [Joowa Ads Android SDK 基础接入文档](Joowa%20Ads%20Android%20SDK%20基础接入文档.md)

## 2. 加载激励视频广告

```
JoowaRewardedVideo.load();
```

## 3. 判断激励视频广告是否加载完毕

```
JoowaRewardedVideo.isReady();
```

## 4. 展示激励视频广告

```
JoowaRewardedVideo.show();
```

## 5. (可选)设置激励视频广告回调监听器

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

## 6. 补充说明

1. Joowa 封装了 Mopub 聚合广告，并移除了广告位（AdunitId/AdPlacement）的相关概念。
2. 在调用 `load` / `isReady` / `show` 等方法时，SDK内部都会选择在合适的时机去预加载更多广告。