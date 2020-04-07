---
id: cloud_proxy
title: 使用云代理
sidebar_label: 使用云代理服务
---

## 功能描述

对于安全需求较高的企业用户，设置防火墙可以限制员工访问非认可的网站，保护内部信息安全。

为避免这些企业因防火墙无法使用 Agora 的服务，Agora 开发了云代理服务。用户只需要在防火墙上将特定的 IP 及端口列入白名单，就可以实现内网访问 Agora 服务。

相比配置单一的代理服务器的 IP，云代理服务更灵活稳定，因此在大型企业、医院、高校、金融等安全需求较高的机构内都有广泛的应用。

## 实现方法

在开始前，请确保已在你的项目中实现基本的实时音视频功能。详见[实现音视频通话](basic_call.md)。

参考以下步骤使用云代理服务：

1. 联系 sales@agora.io，提供 App ID，并提供代理服务使用区域、并发规模、网络运营商等信息，申请开通云代理服务。
2. 将以下测试 IP 及端口添加到企业防火墙的白名单。

    源地址为集成了 Agora Web SDK NG 的客户端。

    #### 国内测试

    | 协议 | 目标地址       | 端口                      | 端口用途                      |
    | ---- | -------------- | ------------------------- | ----------------------------- |
    | TCP  | 150.138.153.78 | 443, 4000<br/>3433 - 3460 | 消息数据传输<br/>媒体数据交换 |
    | TCP  | 47.74.211.17   | 443                       | 边缘节点通信                  |
    | TCP  | 52.80.192.229  | 443                       | 边缘节点通信                  |
    | TCP  | 52.52.84.170   | 443                       | 边缘节点通信                  |
    | TCP  | 47.96.234.219  | 443                       | 边缘节点通信                  |
    | UDP  | 150.138.153.78 | 3478 - 3500               | 媒体数据交换                  |

    #### 国外测试

    | 协议 | 目标地址       | 端口                     | 端口用途                      |
    | ---- | -------------- | ------------------------ | ----------------------------- |
    | TCP  | 23.236.115.138 | 443, 4000<br/>3433 - 3460 | 消息数据传输<br/>媒体数据交换 |
    | TCP  | 148.153.66.218 | 443, 4000<br/>3433 - 3460 | 消息数据传输<br/>媒体数据交换 |
    | TCP  | 47.74.211.17   | 443                      | 边缘节点通信                  |
    | TCP  | 52.80.192.229  | 443                      | 边缘节点通信                  |
    | TCP  | 52.52.84.170   | 443                      | 边缘节点通信                  |
    | TCP  | 47.96.234.219  | 443                      | 边缘节点通信                  |
    | UDP  | 23.236.115.138 | 3478 - 3500               | 媒体数据交换                  |
    | UDP  | 148.153.66.218 | 3478 - 3500               | 媒体数据交换                  |

    > 以上 IP 仅供测试阶段调试使用，正式上线前需要向 Agora 申请独立的云代理服务资源。

3. 调用 `AgoraRTCClient.startProxyServer` 方法打开云代理功能，测试是否能正常实现音视频通话或直播。
4. 测试完成后，Agora 会为你部署云代理服务正式环境，并提供相应的 IP 和端口。将 Agora 提供的 IP 和端口添加到企业防火墙的白名单。
5. 如果需要关闭代理，调用 `AgoraRTCClient.stopProxyServer`。

### 示例代码

**开启云代理服务**

```js
const client = AgoraRTC.createClient({mode: 'live',codec: 'vp8'});

// 开启云代理
client.startProxyServer();
// 加入频道
client.join("<YOUR TOKEN>", "<YOUR CHANNEL>").then(() => {
  /** ... **/
});
```

**关闭云代理服务**

```js
// 开启云代理并加入频道后
/** ... **/

// 离开频道
client.leave();

// 关闭云代理服务
client.stopProxyServer();

// 重新加入频道
client.join("<YOUR TOKEN>", "<YOUR CHANNEL>").then(() => {
  /** ... **/
});
```

### API 参考
- [startProxyServer](/api/cn/interfaces/iagorartcclient.html#startproxyserver)
- [stopProxyServer](/api/cn/interfaces/iagorartcclient.html#stopproxyserver)

## 工作原理

Agora 云代理的工作原理如下：
![](https://web-cdn.agora.io/docs-files/1569400362511)

1. SDK 在连接 Agora SD-RTN™™ 之前，向云代理发起请求。
3. 云代理发送相应代理信息。
4. SDK 向云代理发送数据，云代理将接收到的数据透传给 Agora SD-RTN™。
5. Agora SD-RTN™ 向云代理返回数据，云代理再将接收到的数据发送给 SDK。

## 开发注意事项

-  `startProxyServer` 必须在加入频道前调用， `stopProxyServer` 必须在离开频道后调用。
- Agora Web SDK NG 还提供 `setProxyServer` 和 `setTurnServer` 两个方法给用户自行部署代理服务器。这两个方法与 `startProxyServer` 不可同时调用，调用了其中任一个方法，再调用 `startProxyServer` 会报错，反之亦然。
-  `stopProxyServer` 会关闭所有代理服务，包括通过 `setProxyServer` 和 `setTurnServer` 设置的代理。