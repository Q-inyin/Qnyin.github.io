---
title: RN杂写
date: 2026-04-23 00:12:31
tags: ['分享','Android']
---

**获取屏幕尺寸**

```tsx
获取屏幕尺寸
const window = Dimensions.get('window');
const screenHeight = Platform.OS === 'ios' ? window.height : window.height - StatusBar.currentHeight;
const screenWidth = window.width;

在代码中动态获取View的尺寸
ref={(ref) => this.buttonRef = ref}
UIManager.measure(findNodeHandle(this.buttonRef), (x, y, width, height, pageX, pageY) => {
    console.log('width:::::' + width);
    this.setState({
        popWidth: width,
    });

});
Dimensions,
StatusBar,
UIManager,
findNodeHandle
都是'react-native'的导入
```

**什么时候使用双引号,/ 单引号**
```tsx
两种方式有什么不同
renderImage = ({item}) => (
    <TouchableHighlight>
        <View style={styles.imageContainer}>
            <Image style={styles.image} source={{uri: item}}/>
        </View>
    </TouchableHighlight>
);

spinner() {
    return (
        <ActivityIndicator size="small" color="#00ff00"/>
    );
}
```