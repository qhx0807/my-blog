---
title: python根据商户自动生成收款码
date: 2019-09-22 12:21:00
tags: [python]
categories: python
---

记录 `pillow` 操作图片的一些笔记

#### 图片上添加文字

需要下载一款支持中文的字体，如微软雅黑

```py
font_size = 55
font = ImageFont.truetype('./base/font.ttf', size=font_size)
```

```py
def text_to_img(text):
    target = Image.open('./base/02.jpg')
    draw = ImageDraw.Draw(target)
    text_width = font.getsize(text) # 获取文字宽度
    x = int((1217 - text_width[0]) / 2)
    draw.text(xy=(x, 19), text=text, font=font, fill='black')
    return target
```

为了让文字居中，获取文字宽度

```py
text_width = font.getsize(text)
```


#### 从json文件中读取数据

```py
import json

with open('data.json', encoding='utf-8') as d:
    data = json.load(d)
    print(data)
```

#### 生成二维码 并放置到图片中

`python-qrcode` 

```py
import qrcode

def qrcode_to_img(diviceid, city):
    target = Image.open('./base/03.jpg')
    url = code_url.format(diviceid, city)
    qr = qrcode.QRCode(
        version=10,
        error_correction=qrcode.constants.ERROR_CORRECT_M,
        box_size=11,
        border=2
    )
    qr.add_data(url)
    qr.make(fit=True)
    img = qr.make_image(fill_color="black", back_color="white")
    img = img.resize((668, 668))
    target.paste(img, (274, 0, img.width + 274, img.height))
    return target
```

#### 将图片拼接起来

```py
def combine_img(name, diviceid, city):
    target = Image.new('RGB', (1217, 1797), 'white')
    img1 = Image.open('./base/01.jpg')
    img2 = text_to_img(name)
    img3 = qrcode_to_img(diviceid, city)
    img4 = Image.open('./base/04.jpg')
    target.paste(img1, (0, 0, img1.width, img1.height))
    target.paste(img2, (0, img1.height, img2.width, img2.height + img1.height))
    target.paste(img3, (0, img1.height + img2.height, img3.width, img1.height + img2.height + img3.height))
    target.paste(img4, (0, img1.height + img2.height + img3.height, img4.width, img1.height + img2.height + img3.height + img4.height))
    target.save('./img/' + name + '.jpg')
    print(name + '：图片保存成功')
```

