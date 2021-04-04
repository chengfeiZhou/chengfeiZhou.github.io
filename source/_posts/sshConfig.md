---
title: 服务器--ssh 配置与使用
date: 2021-04-02 11:31:58
tags: 
    - 服务器
    - linux
    - ssh
categories: 
    - 服务器
    - linux
    - ssh
---
# 一、目的:
每次登陆ssh都需要手动输一遍用户名密码, 太麻烦了!!
# 二、步骤：
## 1. 客户端：
- 生成ssh秘钥：
```bash
ssh-keygen -t rsa
```
ps: 回车即可, 使用默认配置
- 创建config文件
```bash
cd ~/.ssh
touch config
ls
```
此时该目录下至少存在: 
```
config: 文件, 用来配置ssh
id_rsa: 私钥
id_rsa.pub: 公钥
known_hosts: 登陆过的主机记录
```
ps:公私钥原理(对称加密)自行百度
- 编辑config文件:
格式如下:
```sh
Host: 别名
    HostName: IP
    Port: 端口
    User: 用户名
    IdentityFile: 自己ID私钥
```
示例:
```sh
Host nas
    HostName 192.168.1.109
    User chengfei
    Port 22
    IdentityFile ~/.ssh/id_rsa
```

## 2. 配置服务器:
- 添加认证文件:
```sh
cd ~/.ssh 
```
把客户端的公钥(`id_rsa.pub`)加入到`authorized_keys`文件中, 没有这个文件的话需要自己创建;

## 3. 测试:
在客户端使用 `ssh 别名`就可以快速登陆了
![](/images/sshConfig/01_sshTest.png)

## 4. 小技巧:
- 客户端配置IdentityFile的私钥是可配置, 不一定要使用本机的私钥; 而且不懂主机可以使用不同的私钥, 虽然这样的方法并不推荐;
- 参考: https://linuxize.com/post/using-the-ssh-config-file/
- 参考: https://www.ssh.com/ssh/config/


