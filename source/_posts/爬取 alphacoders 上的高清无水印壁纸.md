---
title: 爬取 alphacoders 上的高清无水印壁纸.md
author: leazhi
tags:
  - [python]
  - [spider]
categories:
  - [python]
  - [spider]
date: 2023-07-19 12:55:22
cover: https://hexo.linuser.com/static/img/top_img1.jpg
discription: 
keywords: 爬虫，python, spider
# password: 
# message: 抱歉，需要输入正确密码方可查看内容
---

又找到一个高清无水印壁纸网站，爬取关键字为 技术 的高清壁纸，代码如下：

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-

'''
# @Time: 7/17/23 2:01 PM
# @Author: leazhi
# @Emal: leazhi@outlook.com
# @Filename: spider_request.py
# @Project: python
'''

import os
import requests
from lxml import etree
import urllib.request

class Wallpaper():
    def __init__(self, url):
        self.url =url
        self.head = {
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
            'Accept-Encoding': 'gzip, deflate, br',
            'Accept-Language': 'zh-CN,zh;q=0.9',
            'Cache-Control': 'no-cache',
            'Cookie': 'wa_session=kf5lh72t1p3o6g3vt47qa82q8t; __cf_bm=X4fQwoD0nPNstu5zZaRLG1k9SNxLHKCRF1_VRSeTVCE-1689741757-0-AffixoaY8/vRVWZ4ZUAnTx/sp8y79spVMWT7OQnP4Zn++g57/e3Uh84LFKGPm+0UtpWd0U4v5i8ivucAvkClUHE=; cf_clearance=NsNvazsgM2xg5.7GCMLZjsNODyU4yB39F2qp7zW5rkQ-1689741758-0-0.2.1689741758',
            'Dnt': '1',
            'Pragma': 'no-cache',
            'Sec-Ch-Ua': '"Not.A/Brand";v="8", "Chromium";v="114", "Google Chrome";v="114"',
            'Sec-Ch-Ua-Mobile': '?0',
            'Sec-Ch-Ua-Platform': '"Linux"',
            'Sec-Fetch-Dest': 'document',
            'Sec-Fetch-Mode': 'navigate',
            'Sec-Fetch-Site': 'none',
            'Sec-Fetch-User': '?1',
            'Upgrade-Insecure-Requests': '1',
            'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36',
            'referer': 'https://wall.alphacoders.com/by_category.php?id=30&name=%E6%8A%80%E6%9C%AF+%E5%A3%81%E7%BA%B8&lang=Chinese'
        }

    def get_html(self):
        response = requests.get(url=self.url, headers=self.head)
        # print(response.text)
        return response

    def get_data(self, response, data_dir):
        tree = etree.HTML(response.text)

        tags_div = tree.xpath('//div[contains(@class,"thumb-container-big ")]')
        # print(len(tags_div))

        for index,data in enumerate(tags_div):
            img_url = 'https://wall.alphacoders.com' + data.xpath('.//div[@class="boxgrid"]/a/@href')[0]
            img_name = img_url.split('=')[1].split('&')[0]
            print(img_name, img_url)

            res = requests.get(img_url, headers=self.head)
            tee = etree.HTML(res.text)
            link = tee.xpath(f'//a[@id="wallpaper_{img_name}_download_button"]/@href')[0]
            print(f'正在保存第 {page} 页的第 {index+1} 张名为 {img_name}的图片，地址为：', img_url)
            urllib.request.urlretrieve(link, f'{data_dir}/{img_name}.jpeg')

    def main(self):

        data_dir = 'images'
        if not os.path.exists(data_dir):
            os.makedirs(data_dir)

        response = self.get_html()
        self.get_data(response, data_dir)

if __name__ == '__main__':
    page = 1
    while page < 6:
        W = Wallpaper(f'https://wall.alphacoders.com/by_category.php?id=30&name=%E6%8A%80%E6%9C%AF+%E5%A3%81%E7%BA%B8&lang=Chinese&quickload=5211&page={page}')
        W.main()
        page += 1
```