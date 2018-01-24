---
title: baidu-doc百度云文档服务
date: 2017-09-02 09:30:50
tags: [javascript,html5]
categories: 前端
---

#### 百度云文档服务，在线查看各种文档，移动端友好。

![](1.jpg)
![](2.jpg)

```html
<!DOCTYPE html>
<html>
<head>
    <title>baidu-doc</title>
</head>
<body>
<style type="text/css">
    *{
        margin: 0;
        padding: 0;
    }
</style>
<div id="reader"></div>
<script src="http://static.bcedocument.com/reader/v2/doc_reader_v2.js"></script>
<script type="text/javascript">
(function () {
    var option = {
        docId: "*************",
        token: "TOKEN",
        host: "BCEDOC",
        width: 610, // 文档容器宽度
        pn: 1, // 定位到第几页，可选
        ready: function (handler) { // 设置字体大小和颜色, 背景颜色（可设置白天黑夜模式）
            handler.setFontSize(2);
            handler.setBackgroundColor("#fff");
            handler.setFontColor("#000");
        },
        flip: function (data) { // 翻页时回调函数, 可供客户进行统计等
            console.log(data.pn);
        },
        fontSize: "big",
        toolbarConf: {
            page: true, // 上下翻页箭头图标
            pagenum: true, // 几分之几页
            full: true, // 是否显示全屏图标,点击后全屏
            copy: true, // 是否可以复制文档内容
            zoom:true,
            position: "center",// 设置 toolbar中翻页和放大图标的位置(值有left/center)
        } //文档顶部工具条配置对象,必选
    };
    new Document("reader", option);
})();
</script>
</body>
</html>
```