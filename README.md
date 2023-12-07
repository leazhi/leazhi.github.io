# hexo

### 开始

1.在本地创建一个空目录，

2.将本项目克隆到创建的目录中

### hexo 管理


进入网站根目录：
```bash
cd /data/hexo/blog
```
#### 清理缓存
执行命令：
```bash
hexo clean && hexo g
```

##### 重启 hexo
```bash
pm2 stop hexo_run.js && pm2 start hexo_run.js
```

### 所需插件

#### 样式渲染插件：
```bash
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

#### 音乐插件:
```bash
npm install --save hexo-tag-aplayer
```

#### 字数统计插件：
```bash
npm install hexo-wordcount --save
```

#### 本地搜索插件：
```bash
npm install hexo-generator-searchdb --save
```

#### 文章加密插件：
```bash
npm install --save hexo-blog-encrypt
```

#### 电影页面插件：
```bash
npm install hexo-douban --save
```

