# 蚂蚁实时视频通信Android推流SDK使用说明

  Artvc Streamer Android SDK是蚂蚁金服支付宝多媒体团队推出的 Android 平台上使用的软件开发工具包(SDK), 负责视频直播的采集和推流。
## 功能特点

* [x] 支持软编和硬编
* [x] 网络自适应， 可根据实际网络情况动态调整目标码率，保证流畅性
* [x] 音频编码：OPUS
* [x] 视频编码：H.264/VP8
* [x] 推流协议：基于WebRTC
* [x] 视频分辨率：支持360P, 480P, 540P和720P
* [x] 音视频目标码率：可设
* [x] 支持手机端单摄像头推流
* [x] 支持搭载Android系统的娃娃机双摄像头同时推流

## 运行环境

* 最低支持版本为Android 4.3 (API level 18)
* 支持的cpu架构：armv7, arm64

## 快速集成

### 配置项目

引入目标库streamer-x.x.x.aar并添加依赖,x.x.x代表版本号。

可参考下述配置方式（以Android Studio为例）：
- 将streamer-x.x.x.aar包copy到目标工程的根目录下，比如放入app/libs下；
- 修改目标工程的build.gradle文件，在android节点下增加：
````gradle
    repositories {
        flatDir{ dirs 'libs'}
    }
````
- 接着在 gradle 的dependencies配置中加入
````gradle
   compile(name: 'streamer-x.x.x', ext: 'aar')
````
- 在AndroidManifest.xml文件中申请相应权限
````xml
<!-- 使用权限 -->
<uses-permission android:name="android.permission.CAMERA"/>
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
<uses-permission android:name="android.permission.RECORD_AUDIO"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.CAPTURE_VIDEO_OUTPUT"/>
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

<!-- 硬件特性 -->
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />
````

### 简单推流示例

- 创建并配置StreamerConfig
````java
StreamerConfig mConfig = new StreamerConfig(machineId,roomUrl,signature);
````
   machineId: 推流方唯一编号，比如娃娃机机器ID编号2000

   roomUrl: 推流房间管理服务器地址，测试地址:ws://artvcroom.d3119.dl.alipaydev.com/ws

   signature: 推流凭证，由业务方和房间管理器方协商，认证通过，才允许推流,测试凭证:signature

- 创建推流事件监听，可以收到推流过程中的异步事件(该步骤可选)。
**注意：该回调直接运行在产生事件的各工作线程中，不要在该回调中做任何耗时的操作，或者直接调用推流API。**
````java
public OnInfoListener mOnInfoListener = new OnInfoListener() {
    @Override
    public void onInfo(StreamerErrorCode what, String id, Object obj) {
        Log.i(TAG,"Id is " + id + ",info:" + what);
        // obj may be null
        switch (what) {
            // ...
        }
    }
}
public OnErrorListener mOnErrorListener = new OnErrorListener() {
    @Override
    public void onError(StreamerErrorCode what, String id, String description) {
        Log.e(TAG,"Id is " + id + ",err:" + what + ",des:" + description);
        // description may be null
        switch (what) {
            // ...
        }
    }
}
````
- 创建ArtvcStreamer对象
````java
ArtvcStreamer mStreamer = new ArtvcStreamer(context,mConfig);
mStreamer.setOnInfoListener(mOnInfoListener);
mStreamer.setOnErrorListener(mOnErrorListener);
````
- 开始推流
````java
mStreamer.startStream();
````
- 停止推流
````java
mStreamer.stopStream();
````
- Activity的生命周期回调处理
```java
public class CallActivity extends Activity {

    // ...
    @Override
    public void onCreate() {
       super.onCreate();
       .......
       mConfig = new StreamerConfig(machineId,roomUrl,signature);
       mStreamer = new ArtvcStreamer(this,mConfig);
       mStreamer.startStream();
       ......
    }
    @Override
    public void onResume() {
        super.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        mStreamer.stopStream();
    }
}
```

##反馈与建议
- TEL：15986802725
- 邮箱：<luoli.zll@alipay.com>
