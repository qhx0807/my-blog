- [转:blog.csdn js获取ip地址的方法](http://blog.csdn.net/enweitech/article/details/52084346 "JavaScript获取IP地址的方法小结") -- `http://ip.chinaz.com/getip.aspx`

- [占位图](https://placeholder.com/ "placeholder")
    1.`http://via.placeholder.com/350x150`
    2.`http://iph.href.lu/150x150?text=hello`
    <small>
        fg: 设置文字颜色, fg没有指定的时候，自动根据bg生成
        bg: 设置图片背景色
        text: 设置文字内容
        [width]x[height]: 设置图片的宽高; 最大宽度2048，最大高度2048，如果超过返回404
    </small>

- [JSONPlaceholder](https://jsonplaceholder.typicode.com/ "jsonplaceholder") -- `GET https://jsonplaceholder.typicode.com/posts`

- 在线生成网址二维码的API接口: 
    1.`http://pan.baidu.com/share/qrcode?w=150&h=150&url=http://www.xxx.com`
    2.`http://b.bshare.cn/barCode?site=weixin&url=http://www.xxx.com`
    3.`http://s.jiathis.com/qrcode.php?url=http://www.xxx.com`
    4.`http://qr.liantu.com/api.php?text=http://www.xxx.com`
    5.`http://api.k780.com:88/?app=qr.get&data=http://www.xxx.com&level=L&size=6`
    6.`https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=http://xxx`

- 通过QQ号获取头像，QQ空间昵称： [转:blog.csdn](http://blog.csdn.net/lddtime/article/details/64590011 "csdn")
    1.`http://q1.qlogo.cn/g?b=qq&nk=820240134&s=100`
    2.`http://q2.qlogo.cn/headimg_dl?dst_uin=820240134&spec=100`
    3.`http://r.pengyou.com/fcg-bin/cgi_get_portrait.fcg?uins=820240134`
    4.`http://qlogo1.store.qq.com/qzone/820240134/820240134/100`

- [利用谷歌云平台GoogleCloudPlatform搭建vps -- facebook](https://www.facebook.com/notes/%E6%8A%80%E5%A4%9A%E4%B8%8D%E5%A3%93%E8%BA%AB/%E5%88%A9%E7%94%A8%E8%B0%B7%E6%AD%8C%E4%BA%91%E5%B9%B3%E5%8F%B0googlecloudplatform%E6%90%AD%E5%BB%BAvps/1447411805367407/)

- [css:Line-height 和 vertical-align 详解](http://www.zcfy.cc/article/deep-dive-css-font-metrics-line-height-and-vertical-align-vincent-de-oliveira-2616.html#user-evaluation-modal)

- HTML `<head>` 生成 不同浏览器/移动端/SEO -- [html-head-generator](http://work.dimpurr.com/lab/html-head-generator/)

- [QQ推广 网页发起QQ聊天](http://shang.qq.com/v3/widget.html)

- [正则表达式 解读](https://regexper.com/#%2F%5Cw%5Cd%5Cs%2Fig)

- [必应每日壁纸](http://cn.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1) `http://cn.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1` 
    1.`format:  xml 或者 js, 为js时输出json`
    2.`idx: 不存在或者等于0时，输出当天的图片,-1为已经预备用于明天显示的信息，1则为昨天的图片，idx最多获取到前16天的图片信息。`
    3.`n: 这是输出信息的数量。比如n=1，即为1条，以此类推，至多输出8条。`
    4.`http://s.cn.bing.net/ + 图片地址进行访问`
    5.`通过代理接口：https://bird.ioliu.cn/v1?url= 支持跨域。[https://bird.ioliu.cn/v1?url=http://www.bing.com/HPImageArchive.aspx?format=js&idx=1&n=1](https://bird.ioliu.cn/v1?url=http://www.bing.com/HPImageArchive.aspx?format=js&idx=1&n=1)`

- [MySql按周，按月，按日分组统计数据](https://blog.csdn.net/lqclh502/article/details/50157483)
    ```sql
        select DATE_FORMAT(createtime,'%Y-%m-%d') days,count(id) count from t_order_fictitious group by days
    ```