---
title: Shadowsocks-setup-summary
date: 2019-07-21 13:39:14
tags:  [shadowsocks]
description: 使用Digital Ocean创建shadowsocks连接
keyword: shadowsocks, vps
comment: true
categories: Blog
---


# 使用Digital Ocean的新加坡droplet
## 环境
debian 8.6  
shadowsocks

## Digital Ocean
现在比较出名的vps服务商有<a href='https://aws.amazon.com'>Amazon web services </a>和<a href='https://www.linode.com/'>Linode </a>和<a href='https://www.digitalocean.com/'>Digital Ocean </a>.  
Amazon web service看官网有新注册免费使用一年的<a href='https://aws.amazon.com/cn/free/?sc_channel=PS&sc_campaign=acquisition_CN&sc_publisher=baidu&sc_medium=cloud_computing_b&sc_content=aws_e&sc_detail=aws_708&sc_category=cloud_computing_b&sc_segment=708&sc_matchtype=phrase&sc_country=CN&s_kwcid=AL!4422!88!5293109925!!16914971671&ef_id=WGUnMgAABMpVQg5w:20170111074724:s'>免费套餐 </a>，不过因为本人没有信用卡只能放弃.  
Linode没有使用过，不做评价。  
现在用的是Digital Ocean,选用最低配置一个月5美元，
这配置用来做shadowsocks服务器也绰绰有余了，
虽然标称带宽是1Mbps,但是配合4G的Android手机配合shadowsocks
用的新加坡的节点使用上youtube能达到4MB/s的下载速度，
没用专门测过。具体还是要看使用的网络运营商，移动联通电信出口路由的不同会对使用效果造成差异。  
选择Digital Ocean的另一个原因是可以使用paypal支付，注册paypal然后绑定国内的银联银行卡
就可以使用，支付时的美元折算成人民币根据当天的汇率计算。  
新用户注册DO时，使用网友的分享链接，注册完会有$10的奖励，同时分享的网友可以获得奖励，
这种奖励机制在各个vps服务商都有。  
在这里也附上我的<a href='https://m.do.co/c/3089c71e176d'>分享链接: https://m.do.co/c/3089c71e176d</a>  
注册后充值$5就可以新建一个vps了.  
在DO这里，一个vps叫做droplet，大概是水滴的意思。
在新建新的droplet之前还有一个步骤，就是在电脑上创建SSH key.  
### 创建SSH key
在建好vps后，我们是通过ssh来控制vps的，因此我们在当前的电脑上要有一个ssh客户端，
在windows上可以使用Putty,是免安装的，如果经常使用的话，可以安装Xshell 5,
可以同时使用几个标签页，方便在不同目录下操作，而且字体也比较好看，
为了以后方便我们使用Xshell。
Xshell 5对于个人和学生用户是免费的，可以在官网下载。
ssh客户端连接服务器有两种方式，一种是使用ssh key连接，另一种是使用密码连接。
新建的droplet是使用ssh key连接，所以我们要在新建droplet之前先创建ssh key。
安装好xshell后，打开xshell 的工具可以看到"新建用户密钥生成想到"，
点击后如下：  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/ssh_key_1.PNG" />  
密钥类型为RSA,密钥长度为2048，我们默认即可。生成后继续下一步。  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/ssh_key_2.PNG" />  
在这里我们输入密码。然后点击完成。  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/ssh_key_3.PNG" />  
在这里我们就可以看到我们生成的密钥了，然后我们选中新生成的密钥，点击属性,在弹出的窗口再选择公钥，可以看到：  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/ssh_key_4.PNG" />  
公钥格式默认为SSH2-OpenSSH,框中的一串字符就是我们要用到的密钥了。  
### 在DO 账户中添加ssh key
我们用新注册的账户登录上DO，点击账户-setting-security
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/add_sshkey_to_DO.PNG" />  
我们点击Add SSH Key就可以添加ssh key了  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/add_sshkey_to_DO_4.PNG" />  
我们把创建的ssh key那一串字符完整的复制到SSH key content这一框中，
在name这一框中输入一些xshell,然后点击蓝色的Add SSH Key，网页便会提示
Add SSH key successfully.  

<img src="http://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/add_sshkey_to_DO_3.PNG" />  
上图中红线框中的就是我们新添加的ssh key.

#### create droplet
下一步我们就可以开始创建dropletle,
在首页右上角点击Create Deoplet  
<img src="http://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/create_droplet_detail_1.PNG" />  
在这里我们选择droplet的Linux发行版，配置，vps机房的国家位置。
对于机房位置的选择，DO有一个全球各个机房的测速网站<a href='http://speedtest-sgp1.digitalocean.com/'>测速网站</a>，
我们可以先用自己常用的网络测速，选择一个网络延迟最小的、网络速度较好的机房位置，
在这里是强调一下网络延迟，xshell默认延迟是300ms，当连接主机时延迟大于300ms时会连不上，虽然可以更改xshell设置大于300ms，不过这样
在终端输命令时延迟大也会影响使用，使用shadowsocks代理以后浏览网页也要点了等很久才有反应。一般延迟小的节点速度都不会很慢。  
<img src="http://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/DO_speedtest_sgp.PNG" />  
我们在Add your SSH keys中勾选我们添加的Xshell.  

<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/create_droplet_detail_2.PNG" />  
最后，点击create 就可以创建一个droplet了。  
<img src="http://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/droplet_list.PNG" />  
创建完回到droplets,就可以看到droplets列表，在这里，我们可以看到droplet的名字、状态、主机IP地址、和启动时间。  

<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/droplet_detail_2.PNG" />  
点击进去后可以看到详细的运行状态，在这里我们在这里创建snapshots.  

到这里vps就创建好了。  
接下来我们通过xshell连接到vps。  
首先打开xshell 5， 新建会话属性。  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/new_xshell_config_1.PNG" />  
在这里我们填写名称，协议选择SSH, 将新建的droplet中的ip地址复制到主机中，端口默认为20。  
然后点击“用户身份登录”  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/new_xshell_config_2.PNG" />  
方法选择Public Key， 用户名为root, 用户密钥为前面生成的，及密码。
完成后确定。  
然后点击xshell 5的打开会话。  
<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/new_xshell_config_3.PNG" />  
选择我们创建的会话连接，点击连接后，连接成功后就能看到下面的提示了。  

<img src="https://1040563090.oss-cn-shenzhen.aliyuncs.com/markdown/20170110_shadowsocks/new_xshell_config_4.PNG" />  

## shadowsocks
### 在服务器安装shadowsocks
在Debian/Ubuntu安装shadowsocks
``` bash
$ sudo apt-get update
$ sudo apt-get install -y python-pip
$ sudo pip install shadowsocks
```
在CentOS安装shadowsocks
``` bash
$ sudo yum install python-setuptools && easy_install pip
$ sudo pip install shadowsocks
```
### 启动shadowsocks服务
``` bash
$ ssserver -p 8836 -k 'password' -m aes-256-cfb

# 或者可以通过以下指令后台启动shadowsocks的服务
ssserver -p 8836 -k 'password' -m aes-256-cfb -d start
ssserver -p 8836 -k 'password' -m aes-256-cfb -d stop
```

使用配置文件的方法
首先创建一个json配置文件：/etc/shadowsocks.json,内容如下：
``` bash
{
  "server": "server_ip_address",
  "server_port": 8836,
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "password": "password",
  "timeout": 300,
  "method": "aes-256-cfb",
  "fast_open": false
}
```

接下来就可以使用下面的指令启动服务：
``` bash
$ ssserver -c /etc/shadowsocks.json

# 或者在后台运行
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```
