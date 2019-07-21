---
title: Linux environment setup
date: 2019-07-21 13:39:14
tags:  linux, vim
description: linux environment setup
keyword: linux, vim,
comment: true
categories: Blog
---

# Linux environment setup
## directory colors
install git
```
$ sudo apt-get install -y git
```
使用solarized theme for GNU ls
```
$ git clone https://github.com/seebi/dircolors-solarized.git
```
使用dircolors.256dark配色
```
$ cp ~/dircolors-solarized/dircolors.256dark ~/.dircolors
$ eval 'dircolors .dircolors'
```
设置Terminal支持256色， vim .bashrc并添加以下内容
```
export TERM=xterm-256color
alias lsa='ls -alh'
```
最后再source一下.bashrc
```
$ source .bashrc
```
或者重新登录一下终端

## vimrc
新建一个工作目录vim
```
$ mkdir ~/vim
$ cd ~/vim
```
安装Vundle
```
$ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```
vim使用molokai主题
```
$ git clone https://github.com/tomasr/molokai.git ~/vim
$ sudo cp molokai.vim /usr/share/vim/vim74/colors/
```
安装ctags
```
$ sudo apt-get install -y ctags
```
从github上获取我的vimrc文件
```
$ git clone https://github.com/laishanhai1040/vimrc.git ~/vim
$ cp ~/vimrc/.vimrc ~/.vimrc
```
然后打开vim, 在命令模式使用PluginInstall安装vim插件。
