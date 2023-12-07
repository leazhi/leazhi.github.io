---
title: 爬取 pexels 站点上网站背景图片
author: leazhi
tags:
  - python
  - spider
categories:
  - [spider]
  - [python]
date: 2023-07-17 11:45:22
cover: https://hexo.linuser.com/static/img/top_img1.jpg
discription: 爬取 pexels.com 关键字为 网站背景 的图片
keywords: 爬虫, spider, python, 背景图片
---

这几天正好在美化 hexo, 发现 pexels 上有好多合适做背景的图片，于是就写了个小爬虫，爬取 pexels.com 上关键字为 网站背景 的前10页的图片。程序如下：

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-

'''
# @Time: 7/17/23 9:36 AM
# @Author: leazhi
# @Emal: leazhi@outlook.com
# @Filename: spider.py
# @Project: python
'''

import requests
import jsonpath
import os


class Pexels():
    def __init__(self, url):
        self.url = url
        self.head = {
            'Content-Type': 'application/json',
            'Dnt': '1',
            'Referer': 'https://www.pexels.com/zh-cn/search/%E7%BD%91%E7%AB%99%E8%83%8C%E6%99%AF/',
            'Sec-Ch-Ua': '"Not.A/Brand";v="8", "Chromium";v="114", "Google Chrome";v="114"',
            'Sec-Ch-Ua-Mobile': '?0',
            'Sec-Ch-Ua-Platform': '"Linux"',
            'Secret-Key': 'H2jk9uKnhRmL6WPwh89zBezWvr',
            'Sentry-Trace': '7e26e72395084fdcacd8f577f2cd213a-8ec4a419844604d2-0',
            'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36',
            'X-Client-Type': 'react',
        }

    # 解析网页内容
    def get_html(self):
        response = requests.get(url=self.url, data=payload, headers=self.head)
        # print(response.json())
        data_json = response.json()
        data_list = data_json['data']
        return data_list

    # 从网页内容中获取想要的数据
    def get_data(self, data_list):
        for index, data in enumerate(data_list):
            img_url = jsonpath.jsonpath(data, '$..download_link')[0]
            img_name = jsonpath.jsonpath(data, '$..id')[0]
            print(f'第 {page} 页的第 {index+1} 张图片名为 {img_name},地址为：' ,img_url)
            res_img = requests.get(url=img_url, headers=self.head, data=payload)
            self.save_data(img_name, res_img)

    # 保存数据
    def save_data(self, img_name, res_img):
        with open(f'{data_dir}/{img_name}.jpg', 'wb') as f:
            f.write(res_img.content)

    def main(self):
        data_list = self.get_html()
        self.get_data(data_list)

if __name__ == '__main__':
    # 创建图片保存目录
    data_dir = 'imgs'
    if not os.path.exists(data_dir):
        os.makedirs(data_dir)

    # 爬取10页数据
    page = 1
    while page < 10:
        payload = {
            'page': page,
            'per_page': '24',
            'query': '%E7%BD%91%E7%AB%99%E8%83%8C%E6%99%AF',
            'orientation': 'all',
            'size': 'all',
            'color': 'all',
            'seo_tags': 'true',
        }
        P = Pexels(f'https://www.pexels.com/zh-cn/api/v3/search/photos?page={page}&per_page=24&query=%E7%BD%91%E7%AB%99%E8%83%8C%E6%99%AF&orientation=all&size=all&color=all&seo_tags=true')
        P.main()

        page += 1
```

运行程序，效果如下：
![](/static/img/spider-20230717001.png)

