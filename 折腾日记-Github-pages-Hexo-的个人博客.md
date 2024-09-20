---
title: 折腾日记-Github pages + Hexo 的个人博客
date: 2024-08-25 17:24:58
tags: 折腾日记
categories: 折腾日记
cover: https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/v2-6c3e3b3687634640e8b8223f66aab0d0_720w.86tm13rrmt.webp
---

## 本文以Linux为演示环境

# 1. 准备阶段
### 1. 注册github账号
打开[github](github.com)
>由于github在CDN域名遭到**DNS污染**
>国内用户**可能**需要自备**梯子**

{% notel blue 提示 %}
在注册账号之前,需要准备一个**可用的邮箱**
如果没有，请先去注册一个**可用的邮箱**
{% endnotel %}

点击**github**主页右上角的```Sign up```,然后根据引导注册账号
![注册github](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/屏幕截图_20240825_174549.syvr4lre9.webp)

### 2. 创建仓库
在注册好账号后，我们需要创建一个```username.github.io```格式的仓库

### 3. 创建ssh密钥
{% notel red 提示 %}
github已经不再支持输入账号密码的方式push代码了
{% endnotel %}
打开终端,输入命令
```bash
ssh-keygen -t rsa -C "your_email@example.com"
```

>**ssh-keygen**的参数含义:
>> -t 参数指代密钥类型，默认为**rsa**，可省略  
>> -C 参数设置注释文字，如: 你的邮箱

接下来一路回车
当你看到如下提示时，密钥就创建成功了  

![创建ssh密钥](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.77dhmqxhz4.webp)

> 如果没有指定```-f```参数，ssh密钥对的默认名称为```id_rsa```(私钥)和```id_rsa.pub```(公钥)  

接下来将```id_rsa.pub```添加到你的github账户中  
点击```Settings```->```SSH keys and GPG keys```->```New SSH Key```  
在当前界面中的```Title```文本框中输入标题  
> 建议输入有一定**含义的名字**  

![添加ssh key](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.13lpkb9qqp.webp)

在```Key```文本框中输入```id_rsa.pub```文件中的内容

![添加ssh key](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.5j44pkqzw9.webp)

接着点击```Add SSH Key```完成key的添加  


>在终端中输入
>```bash
>ssh -T git@github.com
>```
>如若返回
>```text
>Hi HuaLiMao-AQ! You've successfully authenticated, but GitHub does not provide shell access.
>```
>则ssh密钥添加无误  

***
# 2. 开始搭建Hexo
首先创建项目目录
```bash
cd ~
mkdir hexo
cd hexo
```
通过**npm**安装**hexo-cli**
```bash
npm install hexo-cli
```
> 如若提示权限不足, 请在命令前加**sudo**后再执行  

接着运行```hexo init blog``` 初始化博客

在**blog**目录下执行
```bash
hexo s -g
```
接下来打开<localhost:4000>就可以看到博客内容了

***
# 3. 将博客部署到Github Pages
接下来打开```hexo```根目录的**_config.yml**文件
找到```deploy```配置
```yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type:
  repo:
  branch:

...
```
像如下填入内容
```yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: main

...
```
执行```hexo d -g``` 就可以将博客部署到Github Pages了  

静待几分钟,就可以通过<username.github.io>访问你的博客了!

# 4. 国内访问加速
> 这需要你拥有一个域名  

首先在**cloudflare**注册一个账号
![注册cloudflare](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.5xakgkf4p5.webp)
输入你的域名后继续
![输入域名](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.6ik82vcorj.webp)
选择免费计划
![Free Plan](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.73tvp68qft.webp)
继续下一步
![DNS](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.4xuh3ek4zu.webp)
修改你域名的DNS服务器
将域名服务商默认的DNS服务器替换为从**cloudflare**获得的**nameservers**
![修改DNS服务器](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.41xznyc2nb.webp)
![image](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.pf9tl5k24.webp)
然后打开**cloudflare仪表盘**左侧的**SSL/TLS**,将模式改为**Full (strict)**
> **Flexible** 可能会出现 **重定向次数过多** 的问题，进而导致网站打不开
![image](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.1zi6zxesss.webp)

接着进入**Github**->**Repositories**->**username.github.io**->**Setting**->**Pages**
在**Custom domain**中填入你的域名   
并打开**Enforce HTTPS**
![image](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.1aoxfwgb8r.webp)
通过域名提供商，修改刚刚的域名解析，通过 A 记录分别解析到以下 4 个 IP：
```txt
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
![image](https://github.com/HuaLiMao-AQ/picx-images-hosting/raw/master/20240825/image.6t71w1tdo1.webp)
> 主记录输入 **@**  

接下来你就可以通过你的域名访问你的博客了!!!