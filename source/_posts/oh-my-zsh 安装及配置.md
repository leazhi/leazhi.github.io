---
title: oh-my-zsh 安装及配置
author: leazhi
tags:
  - [zsh]
  - [oh-my-zsh]
  - [bash-completion]
  - [terminal]
categories:
  - [terminal]
date: 2023-09-12 09:32:18
cover: 
discription: 
keywords: 
password: 
message: 
---

[oh-my-zsh](https://ohmyz.sh/#install)  是一个开源的命令行 shell 工具，它是 zsh 的一个扩展框架。它提供了许多有用的功能和插件，使得命令行的使用更加高效和愉悦。oh-my-zsh 的一些特性包括自动补全、语法高亮、自定义主题等。同时，它还支持许多第三方插件，可以方便地扩展其功能。

## 系统环境
- OS 版本：22.04.1 LTS (Jammy Jellyfish)
- Kernel 版本： 5.15.0-60-generic #66-Ubuntu SMP Fri Jan 20 14:29:49 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

## 安装 oh-my-zsh

### 安装 zsh

由于 oh-my-zsh 依赖于 zsh，因此首先需要安装 zsh。
```bash
sudo apt install -y zsh
```

### 安装 oh-my-zsh

根据官方提供的安装命令，直接在命令行下执行：
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 主题配置

编辑 `~/.zshrc` 文件，将 ZSH_THEME 变量设置为 `rkj-repos` 主题或者 `rkj` 主题（自己使用的），并保存。然后执行命令  `source ~/.zshrc` 使配置立即生效！

效果如下图：
![](../static/img/20230912/20230912001.jpg)

**对主题配置感兴趣的同学可以参考下** ：
- [oh my zsh主题列举(一部分)推荐@美化@随机主题配置脚本](https://blog.csdn.net/xuchaoxin1375/article/details/112059812)
- [Oh My Zsh 主题 终端显示效果样式表](https://www.cnblogs.com/skprimin/p/16048124.html)

## 插件安装

默认，zsh 的插件目录在 `~/.oh-my-zsh/plugins/` .


### [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

这是一个命令自动补全插件，当你输入命令的几个字母，它会自动根据历史输入进行自动补全，然后按→，安装也很简单：

1.将插件项目克隆到服务器的 zsh 插件目录中：
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/plugins/zsh-autosuggestions
```

2.编辑 `~/.zshrc` 文件，在 `plugins` 列表中添加 zsh-autosuggestions：
```bash
...

plugins=(
        git
        zsh-autosuggestions
)

...
```

3.保存配置。执行 `source ~/.zshrc` 命令，使配置生效。
```bash
source ~/.zshrc
```

### [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

这个插件的主要作用就是在提高颜值（高亮你的zsh可用命令）.安装如下：

1.将插件项目克隆到服务器的 zsh 插件目录中：
```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/plugins/zsh-syntax-highlighting
```

2.编辑 `~/.zshrc` 文件，在 `plugins` 列表中添加 zsh-syntax-highlighting：
```bash
...

plugins=(
        git
        zsh-autosuggestions
        zsh-syntax-highlighting
)
...
```

3.保存配置。执行 `source ~/.zshrc` 命令，使配置生效。
```bash
source ~/.zshrc
```


### [incr](https://mimosa-pudica.net/src/incr-0.2.zsh)

incr是一款自动提示插件，功能非常强大

1.在 zsh 的插件目录 `~/.oh-my-zsh/plugins/` 下新建一个名为 incr 的目录，然后将 incr-0.2.zsh 下载到创建的目录下：
```bash
mkdir ~/.oh-my-zsh/plugins/incr
wget -O ~/.oh-my-zsh/plugins/incr-0.2.zsh https://mimosa-pudica.net/src/incr-0.2.zsh
```

2.编辑 `~/.zshrc` 文件，在该文件最后添加如下内容：
```bash
...

source /home/leazhi/.oh-my-zsh/plugins/incr/incr*.zsh
```

3.执行 `source ~/.zshrc` 命令，使配置生效。
```bash
source ~/.zshrc
```

**对 zsh 感兴趣的同学，可以参考下：[oh-my-zsh：让终端飞
](https://zhuanlan.zhihu.com/p/62501175)**

## .zshrc 最终配置

```bash
# ~/.zshrc

export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="rkj"
plugins=(
        git
        pip
        sudo
        thefuck
        zsh-autosuggestions
        zsh-syntax-highlighting
)
source $ZSH/oh-my-zsh.sh
source /home/leazhi/.oh-my-zsh/plugins/incr/incr*.zsh
PATH=$PATH:/home/leazhi/.local/bin
eval $(thefuck --alias)
```