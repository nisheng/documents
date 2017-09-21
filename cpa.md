## cpa埋点文档

#### 步骤一

代码嵌入到页面中，尽量靠前

<script
src="//oss.ltcdn.cc/monitor/formal/js/monitor_btm.js"
type="text/javascript"></script>



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





