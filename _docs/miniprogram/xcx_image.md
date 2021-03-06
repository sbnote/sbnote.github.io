---
title: image
description: image
---

#### image

图片。

| 属性名     | 类型        | 默认值        | 说明                                                         | 最低版本                                                     |
| ---------- | ----------- | ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| src        | String      |               | 图片资源地址，支持云文件ID（2.2.3起）                        |                                                              |
| mode       | String      | 'scaleToFill' | 图片裁剪、缩放的模式                                         |                                                              |
| lazy-load  | Boolean     | false         | 图片懒加载。只针对page与scroll-view下的image有效             | [1.5.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| binderror  | HandleEvent |               | 当错误发生时，发布到 AppService 的事件名，事件对象event.detail = {errMsg: 'something wrong'} |                                                              |
| bindload   | HandleEvent |               | 当图片载入完毕时，发布到 AppService 的事件名，事件对象event.detail = {height:'图片高度px', width:'图片宽度px'} |                                                              |
| aria-label | String      |               | 无障碍访问，（属性）元素的额外描述                           | [2.5.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |

**注1：image组件默认宽度300px、高度225px** **注2：image组件中二维码/小程序码图片不支持长按识别。仅在wx.previewImage中支持长按识别。**

**mode 有效值：**

mode 有 13 种模式，其中 4 种是缩放模式，9 种是裁剪模式。

| 模式 | 值           | 说明                                                         |
| ---- | ------------ | ------------------------------------------------------------ |
| 缩放 | scaleToFill  | 不保持纵横比缩放图片，使图片的宽高完全拉伸至填满 image 元素  |
| 缩放 | aspectFit    | 保持纵横比缩放图片，使图片的长边能完全显示出来。也就是说，可以完整地将图片显示出来。 |
| 缩放 | aspectFill   | 保持纵横比缩放图片，只保证图片的短边能完全显示出来。也就是说，图片通常只在水平或垂直方向是完整的，另一个方向将会发生截取。 |
| 缩放 | widthFix     | 宽度不变，高度自动变化，保持原图宽高比不变                   |
| 裁剪 | top          | 不缩放图片，只显示图片的顶部区域                             |
| 裁剪 | bottom       | 不缩放图片，只显示图片的底部区域                             |
| 裁剪 | center       | 不缩放图片，只显示图片的中间区域                             |
| 裁剪 | left         | 不缩放图片，只显示图片的左边区域                             |
| 裁剪 | right        | 不缩放图片，只显示图片的右边区域                             |
| 裁剪 | top left     | 不缩放图片，只显示图片的左上边区域                           |
| 裁剪 | top right    | 不缩放图片，只显示图片的右上边区域                           |
| 裁剪 | bottom left  | 不缩放图片，只显示图片的左下边区域                           |
| 裁剪 | bottom right | 不缩放图片，只显示图片的右下边区域                           |

**示例：**

[在开发者工具中预览效果](https://developers.weixin.qq.com/s/3Kcaatmc7Z5s)

```html
<view class="page">
  <view class="page__hd">
    <text class="page__title">image</text>
    <text class="page__desc">图片</text>
  </view>
  <view class="page__bd">
    <view class="section section_gap" wx:for="{{array}}" wx:for-item="item">
      <view class="section__title">{{item.text}}</view>
      <view class="section__ctn">
        <image
          style="width: 200px; height: 200px; background-color: #eeeeee;"
          mode="{{item.mode}}"
          src="{{src}}"
        ></image>
      </view>
    </view>
  </view>
</view>
Page({
  data: {
    array: [{
      mode: 'scaleToFill',
      text: 'scaleToFill：不保持纵横比缩放图片，使图片完全适应'
    }, {
      mode: 'aspectFit',
      text: 'aspectFit：保持纵横比缩放图片，使图片的长边能完全显示出来'
    }, {
      mode: 'aspectFill',
      text: 'aspectFill：保持纵横比缩放图片，只保证图片的短边能完全显示出来'
    }, {
      mode: 'top',
      text: 'top：不缩放图片，只显示图片的顶部区域'
    }, {
      mode: 'bottom',
      text: 'bottom：不缩放图片，只显示图片的底部区域'
    }, {
      mode: 'center',
      text: 'center：不缩放图片，只显示图片的中间区域'
    }, {
      mode: 'left',
      text: 'left：不缩放图片，只显示图片的左边区域'
    }, {
      mode: 'right',
      text: 'right：不缩放图片，只显示图片的右边边区域'
    }, {
      mode: 'top left',
      text: 'top left：不缩放图片，只显示图片的左上边区域'
    }, {
      mode: 'top right',
      text: 'top right：不缩放图片，只显示图片的右上边区域'
    }, {
      mode: 'bottom left',
      text: 'bottom left：不缩放图片，只显示图片的左下边区域'
    }, {
      mode: 'bottom right',
      text: 'bottom right：不缩放图片，只显示图片的右下边区域'
    }],
    src: '../resources/cat.jpg'
  },
  imageError(e) {
    console.log('image3发生error事件，携带值为', e.detail.errMsg)
  }
})
```

##### 原图

![image](assets/xcx_image/0.jpg)

##### scaleToFill

不保持纵横比缩放图片，使图片完全适应

![image](assets/xcx_image/1.png)

##### aspectFit

保持纵横比缩放图片，使图片的长边能完全显示出来

![image](assets/xcx_image/2.png)

##### aspectFill

保持纵横比缩放图片，只保证图片的短边能完全显示出来

![image](assets/xcx_image/3.png)

##### top

不缩放图片，只显示图片的顶部区域

![image](assets/xcx_image/4.png)

##### bottom

不缩放图片，只显示图片的底部区域

![image](assets/xcx_image/5.png)

##### center

不缩放图片，只显示图片的中间区域

![image](assets/xcx_image/6.png)

##### left

不缩放图片，只显示图片的左边区域

![image](assets/xcx_image/7.png)

##### right

不缩放图片，只显示图片的右边边区域

![image](assets/xcx_image/8.png)

##### top left

不缩放图片，只显示图片的左上边区域

![image](assets/xcx_image/9.png)

##### top right

不缩放图片，只显示图片的右上边区域

![image](assets/xcx_image/10.png)

##### bottom left

不缩放图片，只显示图片的左下边区域

![image](assets/xcx_image/11.png)

##### bottom right

不缩放图片，只显示图片的右下边区域

![image](assets/xcx_image/12.png)

[原文](https://developers.weixin.qq.com/miniprogram/dev/component/image.html)

