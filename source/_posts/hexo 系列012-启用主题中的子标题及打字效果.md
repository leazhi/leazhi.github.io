---
title: hexo 系列012-启用主题中的子标题及打字效果
author: leazhi
tags:
  - [hexo]
categories:
  - [hexo]
date: 2023-07-19 15:19:17
cover: https://hexo.linuser.com/gallery/wallpaper/1044085.png
discription: 
keywords: hexo, 博客， blog
password: 
message: 
---

1.进入 hexo 站点根目录：
```bash
cd /data/hexo/blog
```

2.编辑主题配置文件 `vim theme/butterfly/_config.yml`, 找到关键字 `subtitle:` ,配置如下：
```bash
subtitle:
  enable: true              # 启用字标题
  # Typewriter Effect (打字效果)
  effect: true
  # Customize typed.js (配置typed.js)
  # https://github.com/mattboldt/typed.js/#customization
  typed_option:
  # source 調用第三方服務
  # source: false 關閉調用
  # source: 1  調用一言網的一句話（簡體） https://hitokoto.cn/
  # source: 2  調用一句網（簡體） https://yijuzhan.com/
  # source: 3  調用今日詩詞（簡體） https://www.jinrishici.com/
  # subtitle 會先顯示 source , 再顯示 sub 的內容
  source: false
  loop: false
  # 如果關閉打字效果，subtitle 只會顯示 sub 的第一行文字
  sub:
    - 此生为人，实属意外<p align="right">--- 范雨素</font><p tex-indent="2m">本人出身耕农之家，落地时天无异象。父母识字略过百，茅屋三间遮身，非书香门第，几亩>瘦田刨食，得以苟活幸存，全仗上天垂青得意母爱父恩，方能蜷缩在万丈红尘。八年学堂混个初本，弃笔从容未见盖世功勋。<p style="2m">虽命比纸薄心恨天高，匍匐人间百折不
挠，引入烟尘偶得逍遥，学富虽无五车却喜舞文弄墨，居于市井窥视庙堂，偶尔故作高深、装模作样，人前不敢卸下伪装，人后不敢直视内心肮脏。做好人不能由始及终，行坏事不
敢彻底放纵，贪财，又恐世俗不容，好色，时常胆怂，既非混世魔王，也非盖世英雄。见庙烧香，遇佛也拜，酒肉均沾。放生时慈悲为怀，杀生时手起刀快，既辜负了观音，也辜负
了如来。蹉跎半生，无傲人过往可寻，展望未来，亦缺少年雄心。苟且红尘偷生，虚度年华光阴。<p> 愧疚为人子，未能尽人之孝行让父母安心，不安为人父，为担起父之责任，承
子女欢情。自责为人友，为队友肝胆赤诚，空谈事件知己难寻，此生为人实属意外，因无经验参照，不尽仁义之处，还请多多担待。
```

3.打开 hexo 首页，就可以看到大字效果了！