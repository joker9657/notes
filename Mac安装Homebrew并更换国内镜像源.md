# Homebrew 概念
[Homebrew](https://brew.sh/index_zh-cn) 是Mac的包管理工具，相当于`yum`和`apt`。使用 Homebrew 安装 Apple（或您的 Linux 系统）没有预装但你需要的东西。
```shell
$ brew install wget
```
# Homebrew 安装
```shell
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" // 脚本会在执行前暂停，并说明它将做什么。
```
一般来说在国内由于 GFW 的原因是无法直接安装的，报错如下，必须在终端开启**代理**才可使用命令
```shell 
curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused
```
# 更换国内源
Homebrew 是由 ruby 编写的软件，当执行`brew update`时会先自我更新，我们可以将它的源修改为国内源，则速度更快。维护这些开源镜像的有清华大学与中科大。
```shell
$ git -C "$(brew --repo)" remote set-url origin git://mirrors.ustc.edu.cn/brew.git // 这是中科大的源

$ git -C "$(brew --repo homebrew/core)" remote set-url origin git://mirrors.ustc.edu.cn/homebrew-core.git // 官方维护的核心Formula
```
