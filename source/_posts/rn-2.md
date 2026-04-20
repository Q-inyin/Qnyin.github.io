---
title: RN注入JS WebView与安卓之间的简单通讯
date: 2026-04-25 00:11:00
tags: ['分享','Android']
---

**文档查看**

[WebView · React Native 中文网](https://reactnative.cn/docs/0.70/webview)

**安卓里“注入 JS + WebView 通讯”本质上就是 **让 JavaScript 和 Java 两个运行环境互相调用对方的方法**。可以理解成在网页和原生代码之间搭了一座“桥”（Bridge）。**

在 Android 中，WebView 其实就是一个浏览器内核（基于 Chromium），它运行 JS 引擎，同时也允许 Java 层参与。

{% post_link rn-3 JSBridge原理详解 %}

通讯的本质就是两件事：

### 1️⃣ JS 调用 Java

通过系统提供的接口，把 Java 对象“暴露”给 JS

👉 JS 可以直接调用 Java 方法

------

### 2️⃣ Java 调用 JS

通过 WebView 执行 JS 字符串代码

👉 本质是“让浏览器执行一段 JS”

## evaluateJavascript（推荐方法）

Java 调 JS：

```tsx
webView.evaluateJavascript("javascript:alert('hi')", null);
```

或获取返回值：

```tsx
webView.evaluateJavascript("javascript:getData()", value -> {
    Log.d("result", value);
});
```

### 🔍 原理

- 直接调用 Chromium 的 JS 引擎执行代码
- 支持回调结果（异步）

👉 比 `loadUrl("javascript:...")` 更现代

------

# 三、JS 注入方式

## 1️⃣ 页面加载后注入

```js
webView.evaluateJavascript("javascript:xxx()", null);
```

------

## 2️⃣ 拦截 HTML 注入

通过：

```js
shouldInterceptRequest()
```

修改 HTML 内容，把 JS 插进去

------

## 3️⃣ 本地 JS 文件注入

```js
webView.loadUrl("file:///android_asset/xxx.js");
```

### 场景：JS 调用 Android

1️⃣ 页面加载
 2️⃣ Android 注入接口
 3️⃣ JS 调用 `Android.xxx()`
 4️⃣ WebView 内核：

- JS → C++ → Java（反射）
   5️⃣ Java 执行方法

------

### 场景：Android 调 JS

1️⃣ Java 调用：

```js
evaluateJavascript()
```

2️⃣ WebView 内核执行 JS
 3️⃣ JS 返回结果（可选）



**JSBridge 框架**

很多项目不会直接用原生方式，而是封装一层：

👉 JSBridge

常见实现思路：

- 双向消息队列
- JSON 格式通信
- Promise / callback 机制

流程：

```
JS -> 发送 message(JSON)
   -> Android 解析
   -> 执行方法
   -> 回调 JS
```

👉
 **Android WebView 通讯本质是通过 addJavascriptInterface 或 URL 拦截等方式，把 Java 方法暴露给 JS，同时通过 evaluateJavascript 执行 JS，实现双向调用，本质是跨语言调用桥接机制。**

**rn注入js**


```tsx
import { WebView } from 'react-native-webview';
```

**根页面**

```tsx
render() {
    const { params } = this.props.route;
    const { habit, webSrc, jsCode } = params;

    console.log("takeOver:", webSrc);
    return (
      <WebView
        // ref={this.webviewRef}
        source={{ uri: webSrc }}
        scalesPageToFit={false}
        originWhitelist={['*']}
        onMessage={this.appafMessage}
        injectedJavaScript={jsCode}
        mediaPlaybackRequiresUserAction={true}
        style={{ width: deviceWidth, height: deviceHeight }}
      />
    )
  }
```

------

## H5 → RN：调用原生（postMessage）

H5 页面：

```
window.ReactNativeWebView.postMessage(
  JSON.stringify({
    type: "getToken"
  })
);
```

RN 端：

```
<WebView
  onMessage={(event) => {
    const data = JSON.parse(event.nativeEvent.data);
    if (data.type === 'getToken') {
      console.log("H5 请求 token");
    }
  }}
/>
```

------

## RN → H5：回调数据（动态注入）

```
webViewRef.current.injectJavaScript(`
  window.onGetToken("abc123");
`);
```

H5：

```
window.onGetToken = function(token) {
  console.log("收到 token:", token);
};
```

## 完整通信流程

### ✅ 登录态同步流程

 1️⃣ RN 登录成功拿到 token
 2️⃣ WebView 注入 JS（injectedJavaScript）
 3️⃣ H5 页面读取 `window.APP_TOKEN`
 4️⃣ 完成免登录

------

### ✅ H5 调用原生能力

 1️⃣ H5 调用：

```
postMessage()
```

 2️⃣ RN `onMessage` 接收
 3️⃣ 根据 type 分发逻辑（类似接口路由）
 4️⃣ RN 再通过 `injectJavaScript` 回调