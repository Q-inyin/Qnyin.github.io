---
title: 使用 JSBridge 与原生 IOS、Android 进行交互
date: 2024-04-20 22:40:12
tags: ['分享','Android']
---

# 使用 JSBridge 与原生 IOS、Android 进行交互（含H5、Android、IOS端代码，附 Demo）

本文详细讲述了如何使用 JSBridge 在 H5 和原生 Android、IOS之间进行交互。IOS 端包含 OC 和 Swift 的版本，Android 端包含 Java 和 Kotlin 版本。



## 一、写在前面

本文主要是通过代码讲述了如何使用 JSBridge 在 H5 和 原生之间进行通信。文中包含 H5、IOS、Android 三部分的代码。
IOS 中使用 OC 和 Swift 分别进行了代码实现。Android 中使用 Java 和 Kotlin 分别进行了代码实现。

Demo 地址：[jsbridge-example](https://github.com/beichensky/jsbridge-example)

- `JSBridgeH5`：`H5` 端代码实现
- `JSBridgeIOSOC`：原生 `IOS` 端 `OC` 代码实现
- `JSBridgeIOSSwift`：原生 `IOS` 端 `Swift` 代码实现
- `JSBridgeAndroidJava`：原生 `Android` 端 `Java` 代码实现
- `JSBridgeAndroidKotlin`：原生 `IOS` 端 `Kotlin` 代码实现

本文没有讲解关于原理的部分，只是详细使用代码介绍了 JSBridge 的使用。想要了解原理的朋友，可以另行搜索关于原理的博客。

------

## 二、H5 端代码

- 初始化 `WebViewJavascriptBridge`，方式代码如下
- 注册供原生调用的事件函数：
  `window.setupWebViewJavascriptBridge(bridge => bridge.registerHandler('fnName', function) )`
- 调用原生事件函数：
  `window.setupWebViewJavascriptBridge(bridge => bridge.callHandler('fnName', data, callback) )`

### 1、初始化 `WebViewJavascriptBridge`

在项目入口文件或者根 `js` 文件下，添加以下代码：

```
// 这里根据移动端原生的 userAgent 来判断当前是 Android 还是 ios
const u = navigator.userAgent;
// Android终端
const isAndroid = u.indexOf('Android') > -1 || u.indexOf('Adr') > -1;
// IOS 终端
const isIOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); 

/**
 * 配合 IOS 使用时的初始化方法
 */
const iosFunction = (callback) => {
    if (window.WebViewJavascriptBridge) { return callback(window.WebViewJavascriptBridge) }
    if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback) }
    window.WVJBCallbacks = [callback];
    var WVJBIframe = document.createElement('iframe');
    WVJBIframe.style.display = 'none';
    WVJBIframe.src = 'wvjbscheme://__BRIDGE_LOADED__';
    document.documentElement.appendChild(WVJBIframe);
    setTimeout(function(){
         document.documentElement.removeChild(WVJBIframe);
    }, 0);
}

/**
 * 配合 Android 使用时的初始化方法
 */
const andoirFunction = (callback) => {
    if (window.WebViewJavascriptBridge) {
        callback(window.WebViewJavascriptBridge);
    } else {
        document.addEventListener('WebViewJavascriptBridgeReady', function () {
            callback(window.WebViewJavascriptBridge);
        }, false)
    }
}

window.setupWebViewJavascriptBridge = isAndroid ? andoirFunction : iosFuntion;

isAndroid && window.setupWebViewJavascriptBridge(function (bridge) {
    // 注册 H5 界面的默认接收函数（与安卓交互时，安卓端可以不调用函数名，直接 send 数据过来，就能够在这里接收到数据）
    bridge.init(function (msg, responseCallback) {
        message.success(msg);
        responseCallback("JS 返回给原生的消息内容");
    })
});
```



### 2、注册与原生交互的事件函数

```
/*
    window.setupWebViewJavascriptBridge(bridge => {
        bridge.registerHandler('事件函数名',fun 执行函数);
    })
*/
window.setupWebViewJavascriptBridge(bridge => {
    /**
     * data：原生传过来的数据
     * fn: 原生传过来的回调函数
     */
    bridge.registerHandler("H5Function", (data, fn) => {
        console.log(data);
        fn && fn();
    });
});
```

### 3、调用原生注册的事件函数

调用原生注册的时间函数时使用如下的代码：

```
/*
    window.setupWebViewJavascriptBridge(bridge => {
        bridge.callHandler('安卓端函数名', "传给原生端的数据", callback 回调函数);
    })
*/
window.setupWebViewJavascriptBridge(bridge => {
    bridge.callHandler('changeData', data, (result) => {
        console.log(result);
    });
})
```



------

## 三、IOS 端代码

- 初始化 WebViewJavascriptBridge:

  ```
  + (instancetype)bridgeForWebView:(id)webView;
  + (instancetype)bridge:(id)webView;
  ```

- 注册与 `H5` 端交互的事件函数：
  `- (void)registerHandler:(NSString*)handlerName handler:(WVJBHandler)handler;`

- 调用 `H5` 端事件函数：

  ```
  - (void)callHandler:(NSString*)handlerName;
  - (void)callHandler:(NSString*)handlerName data:(id)data;
  - (void)callHandler:(NSString*)handlerName data:(id)data responseCallback:(WVJBResponseCallback)responseCallback;
  ```

### 1、引入 WebViewJavascriptBridge

#### 直接使用方式

- 下载 [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)
- 找到 `WebViewJavascriptBridge`文件夹，直接拖入到 XCode 项目中，在提示的弹窗中选择 `Copy items if needed` 和 `Create groups`，如下图：
  ![Drag Group](https://lufanfan.github.io/images/jsbridge/dragGroup.png)
- 在 `ViewController.h` 头文件中引入 `#import "WebViewJavascriptBridge.h"` 即可

#### Cocopad 使用方式

如必须使用这种方式请自行 Google。

### 2、初始化 WebViewJavascriptBridge

```
// 启用 WebViewJavascriptBridge Log
[WebViewJavascriptBridge enableLogging];

// 初始化 WKWebViewConfiguration 对象
self.webConfig = [[WKWebViewConfiguration alloc] init];
// 设置偏好设置
_webConfig.preferences = [[WKPreferences alloc] init];
// 默认为0
_webConfig.preferences.minimumFontSize = 10;
// 默认认为YES
_webConfig.preferences.javaScriptEnabled = YES;
// 在iOS上默认为NO，表示不能自动通过窗口打开
_webConfig.preferences.javaScriptCanOpenWindowsAutomatically = NO;

// TODO: 请替换成页面的 url 地址
NSString *URLSTR = @"http://xxx.xxx.xxx.xx:xxxx";
self.webView = [[WKWebView alloc] initWithFrame:self.view.bounds configuration:_webConfig];
// 设置 UserAgent 后缀
_webView.customUserAgent = [NSString stringWithFormat:self.webView.customUserAgent, @"app"];
_webView.UIDelegate = self;
_webView.navigationDelegate = self;
NSURL *url = [NSURL URLWithString:URLSTR];
NSURLRequest *urlRequest = [NSURLRequest requestWithURL:url];
[_webView loadRequest:urlRequest];
[self.view addSubview:_webView];

self.bridge = [WebViewJavascriptBridge bridgeForWebView:self.webView];
[_bridge setWebViewDelegate:self];
```

### 3、注册与 H5 端交互的事件函数

```
// 例：注册修改 User 名称的 changeUser 函数
[self.bridge registerHandler:@"changeUser" handler:^(id data, WVJBResponseCallback responseCallback) {
    // 在这里处理逻辑
    NSLog(@"JS 传过来的数据%@", data);
    if (responseCallback) {
        // 执行回调函数
        responseCallback(@"返回给 JS 的数据");
    }
}];
```

### 4、调用 H5 端事件函数

```
// 调用 H5 界面的 changeName 事件函数
[self.bridge callHandler:@"changeName" data:name responseCallback:^(id responseData) {
    NSLog(@"JS 调用 OC 回调函数返回的值：%@", responseData);
}];
```

------

## 四、Android 端代码

- 注册与 `H5` 交互的事件函数：

  ```
  public void registerHandler(String handlerName, BridgeHandler handler) {
      if (handler != null) {
          messageHandlers.put(handlerName, handler);
      }
  }
  ```

- 调用 `H5` 端事件函数

  ```
  public void callHandler(String handlerName, String data, CallBackFunction callBack) {
      doSend(handlerName, data, callBack);
  }
  ```

- 注册与 `H5` 交互的默认事件，即 `H5` 端不调用函数名，直接使用 `send` 函数传递数据，安卓端也可以在这个事件中接收到数据

  ```
  // 设置默认接收函数
  public void setDefaultHandler(BridgeHandler handler) {
      this.defaultHandler = handler;
  }
  ```

- 调用 H5 端注册的默认事件函数

  ```
  @Override
  public void send(String data, CallBackFunction responseCallback) {
      doSend(null, data, responseCallback);
  }
  ```

### 1、引入 BridgeWebView

- 在项目的 build.gradle 文件中添加如下代码：

  ```
  buildTypes {
      // ...
      repositories {
          // ...
          maven { url "https://jitpack.io" }
      }
  }
  ```

- 添加依赖：`implementation 'com.github.lzyzsd:jsbridge:1.0.4'`

### 2、初始化 BridgeWebView

#### 在 `activity_main.xml` 文件中添加布局

```
<com.github.lzyzsd.jsbridge.BridgeWebView
    android:id="@+id/main_wv"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
</com.github.lzyzsd.jsbridge.BridgeWebView>
```

#### 在 MainActivity 中初始化 BridgeWebView

```
mWebView = findViewById(R.id.main_wv);

mWebView.getSettings().setAllowFileAccess(true);
mWebView.getSettings().setAppCacheEnabled(true);
mWebView.getSettings().setDatabaseEnabled(true);
// 开启 localStorage
mWebView.getSettings().setDomStorageEnabled(true);
// 设置支持javascript
mWebView.getSettings().setJavaScriptEnabled(true);
// 进行缩放
mWebView.getSettings().setBuiltInZoomControls(true);
// 设置UserAgent
mWebView.getSettings().setUserAgentString(mWebView.getSettings().getUserAgentString() + "app");
// 设置不用系统浏览器打开,直接显示在当前WebView
mWebView.setWebChromeClient(new WebChromeClient());
mWebView.setWebViewClient(new MyWebViewClient(mWebView));

mWebView.loadUrl(URL);
```

### 3、注册与 H5 交互的事件函数

```
// 默认事件函数
mWebView.setDefaultHandler(new BridgeHandler() {
    @Override
    public void handler(String data, CallBackFunction function) {
        Toast.makeText(MainActivity.this, data, Toast.LENGTH_LONG).show();
        function.onCallBack("安卓返回给 JS 的消息内容");
    }
});

// 普通事件函数
mWebView.registerHandler("reloadUrl", new BridgeHandler() {

    @Override
    public void handler(String data, CallBackFunction function) {
        mWebView.reload();
        Toast.makeText(MainActivity.this, "刷新成功~", Toast.LENGTH_SHORT).show();
        function.onCallBack("");
    }
});
```

### 4、调用 H5 端事件函数

```
// 调用 H5 端默认事件函数
mWebView.send("安卓传递给 JS 的消息", new CallBackFunction() {
    @Override
    public void onCallBack(String data) {
        Toast.makeText(MainActivity.this, data, Toast.LENGTH_LONG).show();
    }
});

// 调用 H5 端普通事件函数
mWebView.callHandler("changeName", mEditName.getText().toString(), new CallBackFunction() {
    @Override
    public void onCallBack(String data) {
        Toast.makeText(MainActivity.this, "name 修改成功", Toast.LENGTH_SHORT).show();
        mEditName.setText("");
    }
});
```

### 5、添加网络权限

这一步是必须的，否则的话， WebView 加载不出来，手机界面会提示 `Webpage not available`。

- 在 `AndroidManifest.xml` 清单文件中添加：

  ```
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
  ```

- 添加了权限之后，网页可能还是加载不出来，可能是因为对未加密的流量不信任，在 `AndroidManifest.xml` 的 `application` 中添加一个属性：`android:usesCleartextTraffic="true"`。如下：

  ```
  <?xml version="1.0" encoding="utf-8"?>
  <manifest ...>
      <application 
          ...
          android:usesCleartextTraffic="true">
          ...
      </application>
  </manifest>
  ```

------

## 五、Tips

### 1、在 Swift 中使用 WebViewJavascriptBridge

和在 OC 中使用类似，直接将下载好的 `WebViewJavascriptBridge` 文件夹拖到 `Swift` 项目中。
但此时还不能直接使用，因为 `WebViewJavascriptBridge` 中使用 `OC` 编写的。所以需要先创建一个头文件，名为：项目名-Bridging-Header.h，将需要用到的 `OC` 的头文件引入进去。
这里我建议不要手动创建，可以让 `XCode` 自动帮忙创建。创建方式：在 `Swift` 项目中创建一个 `OC` 文件：

![创建 OC 文件](https://lufanfan.github.io/images/jsbridge/newOC.png)

创建 OC 文件



之后 XCode 会自动提示创建 `Bridging Header`：

![创建 Bridging Header](https://lufanfan.github.io/images/jsbridge/ocBridge.png)

创建 Bridging Header



创建之后，在文件夹中引入 `WebViewJavascriptBridge.h` 头文件：

![引入 OC 头文件](https://lufanfan.github.io/images/jsbridge/importOC.png)

引入 OC 头文件



最后，就可以在 `Swift` 代码中正常使用 `OC` 编写的方法了：

![使用 OC 编写方法](https://lufanfan.github.io/images/jsbridge/useOC.png)

使用 OC 编写方法



### 2、运行 XCode 项目报错，控制台提示：Unknown class ViewController in Interface Builder file.

解决办法：
打开 `Main.storyboard` 文件，按照下图所示，找到箭头所指输入框中的 `ViewController`，删除掉，之后再重新输入，找到新的 `ViewController`，填进去即可：

![解决异常](https://lufanfan.github.io/images/jsbridge/resolveProblem.png)

解决异常



------

## 六、参考链接

- [JsBridge](https://github.com/lzyzsd/JsBridge)
- [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)
- [iOS开发-WKWebView设置cookie](https://www.jianshu.com/p/7068868776c0)
- [swift项目中使用OC/C的方法](https://www.cnblogs.com/huntaiji/p/4073006.html)
- [iOS类似Android上toast效果](https://blog.csdn.net/a411358606/article/details/50478737)

------

## 七、Demo 地址

[jsbridge-example](https://github.com/beichensky/jsbridge-example)

如果有所帮助，欢迎 Star！