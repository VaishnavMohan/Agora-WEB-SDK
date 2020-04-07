---
id: channel_encryption
title: 媒体流加密
sidebar_label: 媒体流加密
---

## 功能描述

如果你需要启用加密功能，Agora 提供加密方案与设置加密密码方法。

> - 通信和直播场景均支持媒体流加密功能。但是在直播场景下，如果你需要使用旁路推流、录制和储存，请勿使用媒体流加密功能。
> - 若需使用媒体流加密功能，需确保接收端和发送端都使用此功能，否则会出现未定义行为，例如音频无声或视频黑屏。

下图描述了启用内置加密方案的声网音视频通信方案：
![](https://web-cdn.agora.io/docs-files/cn/agora-encryption.png)


## 实现方法

在开始前，请确保已在你的项目中实现基本的实时音视频功能。详见[实现音视频通话](basic_call.md)。

`client` 是指通过 `AgoraRTC.createClient` 创建的本地客户端对象

```js
// 设置加密方案
// `encryptionMode `在 "aes-128-xts"，"aes-256-xts" 和 "aes-128-ecb" 中选择一种方案
// `password` 为加密密码
client.setEncryptionConfig(encryptionMode, password);
```

### API 参考
- [AgoraRTCClient.setEncryptionConfig](/api/cn/interfaces/iagorartcclient.html#setencryptionconfig)

## 开发注意事项

请确保在调用 `AgoraRTCClient.join` 加入频道之前设置加密。