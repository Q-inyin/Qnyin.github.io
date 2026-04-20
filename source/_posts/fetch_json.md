---
title: fetch() 与 JSON 的碰撞
date: 2022-11-17 02:19:07
updated: 2026-04-21 02:19:07
categories: ['JavaScript']
tags: ['js','分享']
---
### fetch() 与 JSON 的碰撞

**JSON是一种结构化数据的简单格式，下面是一个包含带有 props 和 values 的对象的 JSON 示例：**

~~~json
{
  "name": "公众号",
  "id": "FedJavaScript",
  "isVillain": true,
  "friends": []
}
~~~

***在这篇文章中，将介绍如何使用 fetch() API get 和 post JSON 数据。***

#### 调用fetch()

Fetch API 提供了一个 JavaScript 接口，用于访问和操纵 HTTP 管道的一些具体部分，例如请求和响应。它还提供了一个全局 fetch() 方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

fetch() 接受 2 个参数，一个基本的 fetch 请求设置起来很简单。看看下面的代码：

~~~javascript
fetch('https://mp.weixin.qq.com/mp/profile_ext?action=home&__biz=MzAwNjI5MTYyMw==')
~~~

**可选的第二个参数，用于配置请求，例如：**

~~~json
{
  method:'POST',
  body: JSON.stringify(data),
  headers: {
    'content-type': 'application/json'
  }
}
~~~

#### GET JSON

await fetch('/api/names') 启动一个 GET 请求，并在请求完成时评估响应对象。然后，从服务器响应中，您可以使用await response.json() 将 JSON 解析为纯 JavaScript 对象。默认情况下fetch()执行GET请求。但是您始终可以明确指出 HTTP 方法：

~~~json
// ...
const response = await fetch('/api/names', {
  method: 'GET'
});
// ...
~~~

##### 某些 API 服务器可能使用多种格式：JSON、XML 等。这就是这些服务器可能要求您指明发布数据的格式的原因。为此，需要使用 headers 选项，将 Accept 标头设置application/json 以显式请求 JSON：

~~~json
// ...
const response = await fetch('/api/names', {
  headers: {
    'Accept': 'application/json'
  }
});
// ...
~~~

#### POST JSON

```json
async function postName() {
  const object = { name: 'James Gordon' };
  const response = await fetch('/api/names', {
    method: 'POST',
    body: JSON.stringify(object)
  });

  const responseText = await response.text();
  console.log(responseText); // logs 'OK'
}

postName();
```

**这种方法适用于执行POST, 及 PUT 或 PATCH 请求。**

这种方法适用于执行POST, 及 PUT 或 PATCH 请求。

```json
// ...
const object = { name: 'James Gordon' };

const response = await fetch('/api/names', {
  method: 'POST',
  body: JSON.stringify(object),
  headers: {
    'Content-Type': 'application/json'
  }
});
// ...
```