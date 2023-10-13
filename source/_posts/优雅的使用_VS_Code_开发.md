---
title: 优雅的使用 VS Code 开发
toc: true
date: 2020-03-06 12:12:08
categories: 开发
tags:
- Git
- Nrm
- Oh-My-Zsh
- Remote - WSL
- VS Code
cover: https://i.loli.net/2020/03/22/cfkVXAKFWHdM7ZC.jpg
---
用 VS Code 做一些有趣♂的事。
<!-- more -->

![QQ截图20200306120919.png](https://piccdn.freejishu.com/images/2020/03/06/Pn5d3p.png)
<center>先来一张配置好的图！</center>

# 配置 WSL
> 适用于 Linux 的 Windows 子系统是一个为在 Windows 10 和 Windows Server 2019 上能够原生运行Linux二进制可执行文件的兼容层。 [维基百科](https://zh.wikipedia.org/zh-cn/%E9%80%82%E7%94%A8%E4%BA%8E_Linux_%E7%9A%84_Windows_%E5%AD%90%E7%B3%BB%E7%BB%9F)

// 把 Windows 的开发环境都卸载了，正好学习下 Linux ，还不怕折腾坏 XD ~

![QQ截图20200306124543.png](/images/QQ截图20200306124543.png)

控制面板 -> 程序和功能 -> 启用或关闭Windows功能 -> **勾选** 适用于 Linux 的 Windows 子系统 安装完重启一下电脑。

> ubuntu的LTS版本和普通版本在更新的时候到底有什么区别？ [知乎](https://www.zhihu.com/question/54699176)

![QQ截图20200306202625.png](https://piccdn.freejishu.com/images/2020/03/06/Pn5uhr.png)

我安装的 ![QQ截图20200306124720.png](/images/QQ截图20200306124720.png) Ubuntu 18.04 LTS ，VS Code 需要安装 [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)。

![QQ截图20200306130610.png](/images/QQ截图20200306130610.png)

好像默认安装了 Node 和 Git ？没太注意，如果没有的话手动安装下。

![QQ图片20200306132630.png](/images/QQ图片20200306132630.png)

到这里 WSL 就差不多配置完成了，点击 **左下角** 测试一下。

## 代理本机
> WSL 内 clone Github 项目无问题可跳过。

``` shell
sudo vi ~/.bashrc
```

添加如下内容

``` shell
export http_proxy="http://localhost:1080"
export https_proxy="http://localhost:1080"
```

![QQ截图20200307123251.png](/images/QQ截图20200307123251.png)

保存，重启下 VS Code

## WSL1 升级 WSL2
> WSL升级WSL2，WSL2安装说明 [刘明野的博客](https://www.liumingye.cn/archives/326.html)

这个教程挺多的，需要注意的是，转换的时候把傻逼微软的杀毒软件关了。

## 将 WSL 移到别的盘
> A full-featured utility for managing Windows Subsystem for Linux (WSL) [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline/releases)

拯救爆满的 C 盘，下载 <code>LxRunOffline-v3.4.1-msvc.zip</code> ，解压到本地。

此电脑/我的电脑 右键属性 -> 高级系统设置 -> 高级 -> 环境变量 -> 系统变量 -> 双击 Path

![QQ截图20200321185641](/images/QQ截图20200321185641.png)

添加 <code>LxRunOffline</code> 的路径，确定保存。

![QQ截图20200321185912](/images/QQ截图20200321185912.png)

输入命令对 WSL 的目录进行移动。

``` shell
lxrunoffline m -n <WSL名称> -d <路径>
```

等一会就好了。

# 配置终端

## 安装 Zsh

``` shell
sudo apt install zsh
```
顶部 **终端 -> 新建终端 -> 选择默认Shell** 或 **F1 -> Shell -> 终端:选择默认 Shell** 不出意外应该就能看到 **zsh** 了（这也是我最喜欢 VS Code的地方，对我这种系统命令太多记不住的人来说太方便了）。

![QQ截图20200306204902.png](/images/QQ截图20200306204902.png)

## 安装 Oh My Zsh
> Your terminal never felt this good before. [Oh My Zsh](https://ohmyz.sh)

``` shell
xxxx% sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
Cloning Oh My Zsh...
Cloning into '/Users/xxxx/.oh-my-zsh'...
remote: Counting objects: 831, done.
remote: Compressing objects: 100% (700/700), done.
remote: Total 831 (delta 14), reused 775 (delta 10), pack-reused 0
Receiving objects: 100% (831/831), 567.67 KiB | 75.00 KiB/s, done.
Resolving deltas: 100% (14/14), done.
Looking for an existing zsh config...
Found ~/.zshrc. Backing up to ~/.zshrc.pre-oh-my-zsh
Using the Oh My Zsh template file and adding it to ~/.zshrc
             __                                     __
      ____  / /_     ____ ___  __  __   ____  _____/ /_
     / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \
    / /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / /
    \____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/
                            /____/                       ....is now installed!
Please look over the ~/.zshrc file to select plugins, themes, and options.
p.s. Follow us at https://twitter.com/ohmyzsh.
p.p.s. Get stickers and t-shirts at http://shop.planetargon.com.
```

### 配置插件

``` shell
cd ~/.oh-my-zsh/custom/plugins

git clone https://github.com/wting/autojump.git
# https://github.com/wting/autojump

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
# https://github.com/zsh-users/zsh-syntax-highlighting
```

### 配置主题

``` shell
sudo vi ~/.zshrc
```

修改/添加 如下内容

``` shell
DEFAULT_USER=$USER

ZSH_DISABLE_COMPFIX=true

ZSH_THEME="agnoster"

plugins=(
 git
 autojump
 zsh-syntax-highlighting
)
```

**Ctrl + Shift + `** 新建一个终端，好看~

# 其它
> nrm —— 快速切换 NPM 源 （附带测速功能） [segmentfault 思否](https://segmentfault.com/a/1190000000473869)