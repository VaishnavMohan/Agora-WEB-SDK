---
id: multi_user_video
title: 七人以上视频场景
sidebar_label: 七人以上视频场景
---

## 功能描述
在一般的视频直播场景中，如果参与的主播超过七人，可能会引起音画不同步和信息丢失的问题。如果参与连麦的各方将订阅流设置为 1-N 模式，即订阅 1 路大流和 N 路小流，那么直播频道内的主播最多可以支持 17 人。

本文指导你如何使用 Agora Web SDK NG 实现七人以上视频互动直播。

## 实现方法

在开始前，请确保已在你的项目中实现基本的实时音视频功能。详见 [实现音视频通话](basic_call.md)。

参考如下步骤，在你的项目中实现七人以上视频场景功能：
1. 在发布之前，频道内各主播需调用 `AgoraRTCClient.enableDualStream` 方法开启[双流模式](https://docs.agora.io/cn/Agora%20Platform/terms?platform=All%20Platforms#a-name-duala%E5%8F%8C%E6%B5%81%E6%A8%A1%E5%BC%8F)。
> 开启双流模式后，SDK 会根据视频大流参数默认设置对应的视频小流参数，详见[大小流对应关系表](#大小流对应关系表)。

2. 频道内各主播调用 `AgoraRTCClient.setRemoteVideoStreamType` 方法将订阅的一路视频流设置为大流，其余路视频流均设置为小流。
> 大流理论上可以设置为所有支持的视频属性（Video Profile），但建议使用不超过 640x480，15 fps 的视频属性，例如：
> |分辨率|帧率|比特率|
> |----|----|----|
> |640x480|15 fps|500 Kbps|
> |640x360|15 fps|400 Kbps|
> |640x360|39 fps|600 Kbps|

3.（可选）调用 `AgoraRTCClient.setLowStreamParameter` 定制视频小流参数设置。
> - 小流的分辨率（宽高）比例需要和大流的分辨率（宽高）比例相同。
> - 由于不同的浏览器对于视频参数设置有不同的限制，通过使用该接口设置的视频参数不一定都会生效。目前发现的未能充分适配的浏览器有 Firefox 浏览器，对其设置帧率不生效，浏览器本身会把帧率固定在 30 fps。

4. [发布本地音视频](basic_call.md#创建并发布本地音视频轨道)，[订阅远端音视频](basic_call.md#订阅远端用户)。

### 示例代码
`client` 是指通过 `AgoraRTC.createClient` 创建的本地客户端对象

```js
// 定制视频小流参数设置。设置 Video Profile (非必填，SDK 会自动适配一个默认的值) 为 120 (px) × 120 (px), 15 fps, 120 Kbps。
client.setLowStreamParameter({
  width: 120,
  height: 120,
  framerate: 15,
  bitrate: 120,
});

// 开启视频双流模式。
client.enableDualStream().then(() => {
  console.log("enable dual stream success");
}).catch(err => {
  console.log(err);
});

// 将订阅的该路视频流设置为大流/小流。
client.setRemoteVideoStreamType(stream, streamType)
```

### API 参考
- [AgoraRTCClient.enableDualStream](/api/cn/interfaces/iagorartcclient.html#enabledualstream)
- [AgoraRTCClient.setRemoteVideoStreamType](/api/cn/interfaces/iagorartcclient.html#setremotevideostreamtype)
- [AgoraRTCClient.setLowStreamParameter](/api/cn/interfaces/iagorartcclient.html#setlowstreamparameter)

## 开发注意事项
Agora 建议你在界面布局里采用一大多小的窗口布局：大窗口申请大流；小窗口申请小流。

## 大小流对应关系表
> 由于各浏览器对小流的适配存在差异，部分浏览器的部分分辨率大小流对应关系可能存在一些偏差。

| **大流参数** | **对应小流参数** |
| ------------ | ---------------- |
| 720P_5       | 120P_1           |
| 720P_6       | 120P_1           |
| 480P         | 120P_1           |
| 480P_1       | 120P_1           |
| 480P_2       | 120P_1           |
| 480P_4       | 120P_1           |
| 480P_10      | 120P_1           |
| 360P_7       | 120P_1           |
| 360P_8       | 120P_1           |
| 240P         | 120P_1           |
| 240P_1       | 120P_1           |
| 180P_4       | 120P_1           |
| 120P_3       | 120P_1           |
| 120P         | 120P_1           |
| 120P_1       | 120P_1           |
| 480P_3       | 120P_3           |
| 480P_6       | 120P_3           |
| 360P_3       | 120P_3           |
| 360P_6       | 120P_3           |
| 240P_3       | 120P_3           |
| 180P_3       | 120P_3           |
| 480P_8       | 120P_4           |
| 480P_9       | 120P_4           |
| 240P_4       | 120P_4           |
| 720P         | 90P_1            |
| 720P_1       | 90P_1            |
| 720P_2       | 90P_1            |
| 720P_3       | 90P_1            |
| 360P         | 90P_1            |
| 360P_1       | 90P_1            |
| 360P_4       | 90P_1            |
| 360P_9       | 90P_1            |
| 360P_10      | 90P_1            |
| 360P_11      | 90P_1            |
| 180P         | 90P_1            |
| 180P_1       | 90P_1            |

其中，各参数对应的属性如下：

| **参数** | **对应分辨率** | **对应帧率** |
| -------- | -------------- | ------------ |
| 90P_1    | 160x90         | 15           |
| 120P     | 160x120        | 15           |
| 120P_1   | 160x120        | 15           |
| 120P_3   | 120x120        | 15           |
| 120P_4   | 212x120        | 15           |
| 180P     | 320x180        | 15           |
| 180P_1   | 320X180        | 15           |
| 180P_3   | 180x180        | 15           |
| 180P_4   | 424x240        | 15           |
| 240P     | 320x240        | 15           |
| 240P_1   | 320X240        | 15           |
| 240P_3   | 240x240        | 15           |
| 240P_4   | 424x240        | 15           |
| 360P     | 640x360        | 15           |
| 360P_1   | 640X360        | 15           |
| 360P_3   | 360x360        | 15           |
| 360P_4   | 640x360        | 30           |
| 360P_6   | 360x360        | 30           |
| 360P_7   | 480x360        | 15           |
| 360P_8   | 480x360        | 30           |
| 360P_9   | 640x360        | 15           |
| 360P_10  | 640x360        | 24           |
| 360P_11  | 640x360        | 24           |
| 480P     | 640x480        | 15           |
| 480P_1   | 640x480        | 15           |
| 480P_2   | 648x480        | 30           |
| 480P_3   | 480x480        | 15           |
| 480P_4   | 640x480        | 30           |
| 480P_6   | 480x480        | 30           |
| 480P_8   | 848x480        | 15           |
| 480P_9   | 848x480        | 30           |
| 480P_10  | 640x480        | 10           |
| 720P     | 1280x720       | 15           |
| 720P_1   | 1280x720       | 15           |
| 720P_2   | 1280x720       | 15           |
| 720P_3   | 1280x720       | 30           |
| 720P_5   | 960x720        | 15           |
| 720P_6   | 960x720        | 30           |