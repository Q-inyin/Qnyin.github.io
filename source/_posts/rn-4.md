---
title: rn接入google admob
date: 2026-04-26 00:47:14
tags: ['分享','Android']
---

# 一、AdMob 是什么

👉
 **AdMob 是 Google 提供的移动广告平台，用来在 App 中展示广告并获得收入。**

------

# 二、它是干嘛的

你开发了一个 App，比如：

- 工具类 App
- 游戏
- H5 容器（WebView）

👉 想赚钱但不收费？

就可以接入 AdMob：

- 用户打开 App
- 看到广告
- 点击 / 展示
- 你赚钱 💰

------

# 三、AdMob 的工作原理

可以理解成一个“广告中介平台”：

```
广告主 → AdMob → 你的 App → 用户
```

------

## 🔁 详细流程

1️⃣ 广告主在 Google 投广告
 2️⃣ AdMob 管理广告资源
 3️⃣ 你的 App 请求广告
 4️⃣ AdMob 返回广告内容
 5️⃣ 用户看到广告（展示 / 点击）
 6️⃣ 你获得收益

------

# 四、常见广告类型

## 1️⃣ Banner（横幅广告）

- 页面底部/顶部一条
- 不影响主流程

------

## 2️⃣ Interstitial（插屏广告）

- 全屏广告
- 常见于页面切换时

------

## 3️⃣ Rewarded（激励广告）

- 看广告换奖励（游戏最常见）

------

## 4️⃣ Native（原生广告）

- 和 App UI 融合
- 自定义样式

------

# 五、开发怎么接入（以 Android / RN 为例）

## 基本步骤

 1️⃣ 注册 AdMob 账号
 2️⃣ 创建 App 和广告位 ID
 3️⃣ 引入 SDK
 4️⃣ 请求广告并展示

------

## 示例（RN 思路）

[React Native Google Mobile Ads](https://docs.page/invertase/react-native-google-mobile-ads)

```tsx
// 加载广告
AdMobInterstitial.setAdUnitID("your-ad-unit-id");
await AdMobInterstitial.requestAdAsync();

// 展示广告
await AdMobInterstitial.showAdAsync();
```

------

```tsx
**import** Admod **from** './components/Admod';
```

```tsx
function App(): JSX.Element {
  return (
      <NavigationContainer ref={navigationRef}>
        <Stack.Navigator initialRouteName="MyComponent">
          {/* <Stack.Screen name="First" component={FirstPage} /> */}
          <Stack.Screen name="MyComponent" component={MyComponent} options={{headerShown: false}}/>
          <Stack.Screen name="Page" component={Page} options={{headerShown: false}}/>
          <Stack.Screen name="Admod" component={Admod} options={{headerShown: false}}/>
          <Stack.Screen name="Games" component={DemoGames} options={{headerShown: false}}/>
        </Stack.Navigator>
      </NavigationContainer>
  );
}
```

```tsx
// import React, { Component } from 'react'
import {
  View
} from 'react-native';
// import { BannerAd, BannerAdSize, TestIds } from 'react-native-google-mobile-ads';

// const adUnitId = __DEV__ ? TestIds.BANNER : 'ca-app-pub-9531203765146692/7400802315';

// export class Admod extends Component {
//   render() {
//     return (
//       <View>
//         <BannerAd
//           unitId={adUnitId}
//           size={BannerAdSize.ANCHORED_ADAPTIVE_BANNER}
//           requestOptions={{
//             requestNonPersonalizedAdsOnly: true,
//           }}
//         />
//       </View>
//     )
//   }
// }

// export default Admod

import React, { Component } from 'react'

export default class Admod extends Component {
  render() {
    return (
      <View>
        
      </View>
    )
  }
}
```
**安装依赖项**
首先，安装 react-native-google-mobile-ads 包及其对等依赖项：

# npm
```js
npm install react-native-google-mobile-ads @react-native-firebase/app
```
# yarn
```js
yarn add react-native-google-mobile-ads @react-native-firebase/app
2.链接本地依赖项
将本地依赖关联到你的项目：
```
# For iOS
```Swift
cd ios
pod install
cd ..

# For Android, no additional linking is needed if you are using React Native 0.60 or higher.
```
### 3. 配置 Firebase
Google Mobile Ads 需要 Firebase，因此需要为项目设置 Firebase。

**iOS 配置：**

1. 转到 Firebase 控制台，创建一个新项目，并向其中添加一个 iOS 应用。
2. 下载 GoogleService-Info.plist 文件并将其放在您的 ios/YourProjectName 目录中。
3. 将以下内容添加到 ios/YourProjectName/AppDelegate.m ：

```Swift
#import <Firebase.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [FIRApp configure];
  ...
}
```
**Android 配置：**

 1. 在 Firebase 控制台中，向项目添加一个 Android 应用。
 2. 下载 google-services.json 文件并将其放在您的 android/app 目录中。
 3. 修改 android/build.gradle 和 android/app/build.gradle ：

```Kotlin
// android/build.gradle
buildscript {
    dependencies {
        classpath 'com.google.gms:google-services:4.3.3'
    }
}
```
```Kotlin
// android/app/build.gradle
apply plugin: 'com.google.gms.google-services'
```
### 4. 设置 AdMob

通过将 AdMob 应用 ID 添加到 Info.plist 和 AndroidManifest.xml ，配置应用以使用 Google AdMob。

iOS 配置：将以下内容添加到您的 ios/YourProjectName/Info.plist ：
```Kotlin
<key>GADApplicationIdentifier</key>
<string>ca-app-pub-xxxxxxxxxxxxxxxx~xxxxxxxxxx</string> <!-- Replace with your AdMob App ID -->
```
Android 配置：将以下内容添加到你的 android/app/src/main/AndroidManifest.xml ：
```kotlin
<application>
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="ca-app-pub-xxxxxxxxxxxxxxxx~xxxxxxxxxx"/> <!-- Replace with your AdMob App ID -->
</application>
```
### 5. 实施广告组件
现在，可以开始在您的 React Native 应用程序中展示广告。 react-native-google-mobile-ads 包支持各种广告格式，包括横幅广告，插页式广告和奖励广告。

Displaying a Banner Ad: 显示横幅广告：
```tsx
import React from 'react';
import { View, StyleSheet } from 'react-native';
import { BannerAd, BannerAdSize, TestIds } from 'react-native-google-mobile-ads';

const App = () => {
  return (
    <View style={styles.container}>
      <BannerAd
        unitId={TestIds.BANNER} // Replace with your actual Ad unit ID
        size={BannerAdSize.FULL_BANNER}
        requestOptions={{
          requestNonPersonalizedAdsOnly: true,
        }}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```
显示一个插页广告：
```tsx
import React, { useEffect } from 'react';
import { Button } from 'react-native';
import { InterstitialAd, AdEventType, TestIds } from 'react-native-google-mobile-ads';

const interstitial = InterstitialAd.createForAdRequest(TestIds.INTERSTITIAL, {
  requestNonPersonalizedAdsOnly: true,
});

const App = () => {
  useEffect(() => {
    const unsubscribe = interstitial.onAdEvent((type) => {
      if (type === AdEventType.LOADED) {
        interstitial.show();
      }
    });

    interstitial.load();
    
    return unsubscribe;
  }, []);

  return (
    <Button title="Show Interstitial Ad" onPress={() => interstitial.load()} />
  );
};

export default App;
```
显示一个奖励广告：
```tsx
import React, { useEffect } from 'react';
import { Button } from 'react-native';
import { RewardedAd, RewardedAdEventType, TestIds } from 'react-native-google-mobile-ads';

const rewardedAd = RewardedAd.createForAdRequest(TestIds.REWARDED, {
  requestNonPersonalizedAdsOnly: true,
});

const App = () => {
  useEffect(() => {
    const unsubscribe = rewardedAd.onAdEvent((type, error, reward) => {
      if (type === RewardedAdEventType.LOADED) {
        rewardedAd.show();
      }
      if (type === RewardedAdEventType.EARNED_REWARD) {
        console.log('User earned reward of ', reward);
      }
    });

    rewardedAd.load();
    
    return unsubscribe;
  }, []);

  return (
    <Button title="Show Rewarded Ad" onPress={() => rewardedAd.load()} />
```

# 六、和 WebView / JSBridge 的关系（你可以这样理解）

如果你做的是：

👉 **RN + WebView + H5**

可以这样用：

- RN 负责接入 AdMob
- H5 通过 JSBridge 请求“展示广告”
- RN 调用 AdMob 显示

👉 流程：

```
H5 → JSBridge → RN → AdMob → 展示广告
```

------

# 七、优缺点

## ✅ 优点

- 接入简单
- 广告资源多（Google生态）
- 收益稳定

------

## ❌ 缺点

- 国内访问不稳定（需要特殊环境）
- 审核严格
- 收益依赖用户量

------

# 八、总结（直接用）

👉
 **AdMob 是 Google 提供的移动广告变现平台，开发者可以通过接入 SDK 在 App 中展示广告获取收益。在实际项目中可以结合 WebView 和 JSBridge，由前端触发广告展示请求，原生侧调用 AdMob SDK 完成广告加载与展示，实现广告能力的统一管理。**