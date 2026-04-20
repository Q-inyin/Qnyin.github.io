---
title: canvas
date: 2022-12-20 21:19:41
categories:tags: ['JavaScript']
tags: ['分享']
---

# <font color=red face="黑体">此文章只适合微信小程序，Taro组件矿建</font>

> https://taro-docs.jd.com/docs
>
> https://developers.weixin.qq.com/miniprogram/dev/framework/

## 小程序单位为rpx 为了适配屏幕，用到 Taro.getSystemInfoSync()获取设备信息

### <font color=red face="黑体">小程序标准的屏幕大小开发是iPhone6 【 375*667 】 除去375得到相对单位来适配屏幕</font>

------

## **微信使用getSystemInfoSync()**

```
const temp = Taro.getSystemInfoSync().windowWidth / 375
```

## 1. 自定义方法 参数为自定义props

```
const mycanvans = (props) => {
````
    #要渲染的组件 参数组件（别名）ID
    const context = Taro.createCanvasContext('canvas_poster')
    
    #   Canvas内容
    ````
}
```

------

## 2. 自定义一个绘制文字的方法 参数props

```
const drawText = (context, color, text, x, y, font = 14) => {
      context.setFontSize(font)
      context.setFillStyle(color)
      context.setTextAlign('left')
      context.fillText(text, x, y)
      context.stroke()
      context.closePath()
    }
```

## 3. 自己写一个能够绘制矩形，正方形和圆角、矩形、圆形的方法或背景组件

------

```
const imgfillet = (ctx, img, left, top, width, height, fillet) => {
      //     ctx   图片  起始点X Y   图片宽  高   适配单位  圆角半径
      ctx.beginPath();
      ctx.save();
      ctx.setLineWidth(1);
      ctx.setStrokeStyle('#ffffff');
      ctx.moveTo(left + fillet, top); // 创建开始点
      ctx.lineTo(left + width - fillet, top); // 创建水平线
      ctx.arcTo(left + width, top, left + width, top + fillet, fillet); // 创建弧
      ctx.lineTo(left + width, top + height - fillet);         // 创建垂直线
      ctx.arcTo(left + width, top + height, left + width - fillet, top + height, fillet); // 创建弧
      ctx.lineTo(left + fillet, top + height);         // 创建水平线
      ctx.arcTo(left, top + height, left, top + height - fillet, fillet); // 创建弧
      ctx.lineTo(left, top + fillet);         // 创建垂直线
      ctx.arcTo(left, top, left + fillet, top, fillet); // 创建弧
      ctx.stroke(); // 这个具体干什么用的？
      ctx.clip();

      let bl = img.width / img.height
      let bl2 = width / height
      if (bl > bl2) {
        let w = Math.floor(img.width / (bl / bl2))
        let sx = Math.floor((img.width - w) / 2), sy = 0, sWidth = w, sHeight = img.height
        ctx.drawImage(img.path, sx, sy, sWidth, sHeight, left, top, width, height);
      } else if (bl < bl2) {
        let h = Math.floor(img.height / (bl2 / bl))
        let sx = 0, sy = (img.height - h) / 2, sWidth = img.width, sHeight = h
        ctx.drawImage(img.path, sx, sy, sWidth, sHeight, left, top, width, height);
      } else {
        ctx.drawImage(img.path, left, top, width, height);
      }
      ctx.restore();
      ctx.closePath();
    }
```

## 4. 创建画布和绘制

------

**Taro.getImageInfo相当于wx.getImageInfo用于保存网络的临时路径**

```
//创建画布
Taro.getImageInfo({
  src: '',
  success: (res) => {
    # 绘制第一张背景图
    #                 图片路径  x  y  宽       高
    context.drawImage(res.path, 0, 0, 345*rpx, 600*rpx);
    Taro.getImageInfo({
        src: '',
        success: (result) => {
            console.log(result)
            Taro.getImageInfo({
                src: props.photo,
                success: (result) => {
                    console.log(result)
                }
            }) 
        }
    })}
)}
```

### 注意要先下载图片在绘制文本

------

## 5.1 授权下载Canvas

```
Taro.getSetting({
  success(res) {
    // console.log(res);
    // 查看所有权限 是否包含下载
    let status = res.authSetting[""]; 
    if (!status) {
      // 如果是首次授权(undefined)或者之前拒绝授权(false)
      Taro.authorize({
        // 发起请求用户授权
        scope: "n",
        success(res) {
          // console.log(res);
          setStatu(status);
          console.log("授权成功");
        },
        fail() {
          console.log("授权失败");
          dataTime(status);
        }
      });
    } else {
      status = true;
      setDatabtn("true");
    }
  }
});
```

------

## 5.2 如果用户拒绝授权第二次需要重新拉取授权

```
#自定义方法
# 用到hooks Usestate判断用户之前放弃授权，再次让用户授权
function dataTime(status) {
      Taro.showModal({
        title: "提示",
        content:
          "提示",
        success: function (res) {
          if (res.cancel) {
            //点击取消
            Taro.showToast({ title: "提示失败，请重试", icon: "none" });
            console.log("取消授权");
            // settinglocation(status);
          } else if (res.confirm) {
            //点击确定
            Taro.openSetting({
              success(data) {
                if (data.authSetting["scope.userLocation"] == true) {
                  Taro.showToast({
                    title: "授权成功",
                    icon: "success",
                    duration: 2000,
                    success() {
                      // 授权成功就会调用该接口，再次给status赋值
                      status = true;
                      if (status == true) {
                        setDatabtn("true");
                      }
                    }
                  });
                }
              }
            });
          }
        }
      });
    }
  };
```

------

## 6.渲染画布 下载

```
context.draw(true, () => {
    let ccc = Taro.canvasToTempFilePath({
        x: 0,
        y: 0,
        width: 690,
        height: 1000,
        fileType: 'jpg',
        canvasId: 'canvas_poster',
    success: (res) => {
        ccc.then((result) => {
            console.log(result)
        })
        Taro.showShareImageMenu({
            path: res.tempFilePath,
        })
    )}, fail(err) {
        console.log(err)
        }
    })
}

```

```