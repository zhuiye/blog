---
title: jump-wall
date: 2019-08-07 22:28:47
tags: jump wall
---
# 科学上网

## 方式1

1. 置备一台国外的服务器

   阿里云的香港服务器  [https://cn.aliyun.com/product/swas](qq://txfile/#)

2. 进入系统创建一个 ssr.sh 脚本文件

   内容如下:https://github.com/ToyoDAdoubi/doubi/blob/master/ssr.sh
   
   ```
     mkdir test
     cd test 
     vim ssr.sh
     i:进入编辑模式
     ......输入内容
     ese：返回一般模式
     :wq 保存退处
     bash ssr.sh :执行...
     
   ```
   
3. 开启服务器（SSR服务器端口）也就是在配置SSR， 需要的端口号



## 方式2
进入服务器，执行复制下列代码,傻瓜式操作即可
```l
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh

chmod +x shadowsocksR.sh

./shadowsocksR.sh 2>&1 | tee shadowsocksR.log
```

ssh root@ipaddress

## notice
系统要求: ***centos7***

一般情况下就可以轻松happy访问,但你会发现即使100M的网络，也如龟速，这个时候就要安装BBR 加速.
```
wget –no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh 
chmod +x bbr.sh 
./bbr.sh
```

### relative link following

https://www.zhuangold.com/vultr-vps%e4%b8%bb%e6%9c%ba%e5%bf%ab%e9%80%9f%e5%ae%89%e8%a3%85shadowsocks%ef%bc%88ss%ef%bc%89%e6%95%99%e7%a8%8b-2-2/










