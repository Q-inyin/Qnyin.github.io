---
title: JSBridge是什么
date: 2026-04-26 00:38:08
tags: ['分享','Android']
---

# JSBridge 是什么（WebView 通信原理详解）

## 一、概念（面试开头一句话）
**JSBridge 是一种用于 WebView 场景下，实现 H5（JavaScript）与原生（Android / iOS / RN）双向通信的桥接机制。**

👉 本质：  
> 把“跨语言调用”封装成类似接口调用（RPC）的通信方式

---

## 二、通俗理解
你可以把 JSBridge 理解为：

👉 **翻译 + 通信协议**

- JS 说：我要调用相机  
- JSBridge：帮你转成原生能听懂的格式  
- 原生执行后：再把结果返回给 JS  

---

## 三、为什么需要 JSBridge

### ❌ 不用 JSBridge（混乱写法）
```js
window.ReactNativeWebView.postMessage("getToken");
window.ReactNativeWebView.postMessage("openCamera");
```

### ✔️ 使用 JSBridge（规范写法）

```
bridge.call("getToken").then(token => {
  console.log(token);
});
```

👉 优点：

- 类似调用接口（API）
- 支持 Promise
- 易维护 / 易扩展
- 解耦 H5 和原生

## 四、JSBridge 核心结构

### 1️⃣ JS 发起调用

```
bridge.call("openCamera", { quality: 0.8 });
```

------

### 2️⃣ 统一协议（核心）

```
{
  "method": "openCamera",
  "params": { "quality": 0.8 },
  "callbackId": "123"
}
```

👉 类似 HTTP 请求结构

------

### 3️⃣ 原生接收并执行

```
if(method.equals("openCamera")){
    // 打开相机
}
```

------

### 4️⃣ 原生返回结果

```
{
  "callbackId": "123",
  "result": "image_url"
}
```

------

### 5️⃣ JS 接收回调

```
resolve(result);
```

------

## 五、完整通信流程

### 👉 标准流程（面试直接说这个）

1. JS 调用 `bridge.call`
2. 封装成 JSON（method + params + callbackId）
3. 通过 WebView 通道发送
4. 原生解析 method 并执行
5. 执行完成返回结果
6. JS 根据 callbackId 触发回调（resolve）

------

## 六、底层实现方式

### Android

- `addJavascriptInterface`
- `shouldOverrideUrlLoading`
- `evaluateJavascript`

------

### React Native

- `window.ReactNativeWebView.postMessage`
- `onMessage`

------

### iOS

- `WKWebView`
- `messageHandlers`

------

## 七、简单 JSBridge 示例（RN版）

### JS 侧

```
function call(method, params) {
  return new Promise((resolve) => {
    const callbackId = Date.now();

    window.ReactNativeWebView.postMessage(
      JSON.stringify({ method, params, callbackId })
    );

    window[callbackId] = resolve;
  });
}
```

------

### RN 侧

```
onMessage={(e) => {
  const { method, callbackId } = JSON.parse(e.nativeEvent.data);

  if (method === "getToken") {
    webview.injectJavaScript(`
      window.${callbackId}("abc123");
    `);
  }
}}
```

------

## 八、进阶设计（加分点）

### 1️⃣ 统一协议

```
{
  "method": "xxx",
  "params": {},
  "callbackId": ""
}
```

------

### 2️⃣ Promise 化

- 支持 `.then()`
- 提升开发体验

------

### 3️⃣ 安全控制

- 校验来源 URL
- 限制 method 白名单
- 防止 JS 注入攻击

------

## 九、本质总结（重点）

👉 JSBridge 本质类似：

- RPC（远程调用）
- HTTP API 调用

### 区别：

| 类型     | 场景                      |
| -------- | ------------------------- |
| HTTP     | 跨网络                    |
| JSBridge | 跨运行环境（JS ↔ Native） |

## 十、总结

JSBridge 是一种在 WebView 场景中实现 H5 与原生双向通信的桥接机制，本质是对 postMessage 或 addJavascriptInterface 等能力的封装，通过统一的协议（method + params + callbackId）实现类似 RPC 的调用方式，使前端可以像调用接口一样调用原生能力。