###图片成webp格式

####1.前提

我们在创建游戏的时候，传统的图片（jpg,png等）体积会很大，所以我们会进行压缩处理，这样会一定程度上减小图片的体积，同时带来的后果就是图片失真，在一些大屏幕的设备上游戏的皮肤会有点糊

####2.怎么做

* 检查设备是否支持webp格式图片

  ```javascript
  function check_webp_feature(feature, callback) {
      var kTestImages = {
          lossy: "UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA",
          lossless: "UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==",
          alpha: "UklGRkoAAABXRUJQVlA4WAoAAAAQAAAAAAAAAAAAQUxQSAwAAAARBxAR/Q9ERP8DAABWUDggGAAAABQBAJ0BKgEAAQAAAP4AAA3AAP7mtQAAAA=="
      };
      var img = new Image();
      img.onload = function () {
          var result = (img.width > 0) && (img.height > 0);
          callback(feature, result);
      };
      img.onerror = function () {
          callback(feature, false);
      };
      img.src = "data:image/webp;base64," + kTestImages[feature];
  }
  ```

  大致的原理就是，先加载一个小的webp格式的图片，给这个img绑定onload和onerror两个事件，如果在图片展示的过程中进入了onload函数中，并且图片的`width`和`height`都大于`0`，那么说明该设备支持webp格式的图片

  反之，如果进入了onerror中，说明该设备不支持

* 将传统图片转为webp图片，阿里云cdn已经支持，在原来地址后面加上参数`x-oss-process=image/format,webp`

  ```javascript
  //png格式图片
  http://static.adbaitai.com/cow/2017/07/12/750w_359h_82B5E1499847409_origin.png 
  //webp格式图片
  http://static.adbaitai.com/cow/2017/07/12/750w_359h_82B5E1499847409_origin.png?x-oss-process=image/format,webp
  ```

  ​

####3.带来什么效果

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fktfg3iorgj31li0660v2.jpg)

从图上可以看出同一张图片在png和webp格式下，webp所带来的优势：

1.图片体积会减少将近80%

2.请求时间也会减少将近80%

3.图片不会失真