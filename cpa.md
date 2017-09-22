## cpa埋点文档

#### 步骤一

代码嵌入到页面中，尽量靠前

```
<script src="//oss.ltcdn.cc/monitor/formal/js/monitor_btm.js"></script>
```



#### 步骤二

在需要统计CPA按钮的绑定事件接口回调中添加以下代码

```javascript
monitor_btm.request({
    type:"post",
    url:monitor_btm.data_url,
    data:monitor_btm.url_parameter,
    success:function(){
    }
})
```



### 注意

`本地测试`的时候页面链接必须含有`monitor`这个参数，这个参数上线后走百泰广告会默认带上。

