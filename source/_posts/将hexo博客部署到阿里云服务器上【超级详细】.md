---
title: 建站系列之---部署网站到云服务器
tags: hexo
categories: Hexo建站系列
toc: true
abbrlink: 3010328778
date: 2020-03-17 16:49:31
---
由于这次的疫情，最爱的马云爸爸的阿里云推出了学生在家上机实践的活动，免费领六个月的ECS云服务器，天呐，2核4G啊，六个月之后还可续费六个月，整整一年呐，市场价780软妹币呢，不过投放量少，而且还是每天早上八点领，连续7:55闹钟起早，终于，在第三个早上，马云爸爸眷顾了我，我领到了。领到了就不能让他吃灰吧，于是先部署个博客再说。因为之前博客是托管在github上面的。而github的服务器又在国外，所以访问速度十分感人，但是有了国内的服务器就不一样了，瞬间嗖嗖嗖。。。
<!--more-->

**写在开头的话：** 对于一个刚接触的门外汉来说，搭建博客，到部署到服务器真的让人崩溃，但是在不断地排错过程中，也渐渐弄懂了一些东西。很值得！由于我的域名还在备案中，DNS解析已经停掉了，所以暂时只能用公网IP访问：[123.57.60.151](https://123.57.60.151)，后期等备案成功后再做修改，并且进一步优化。还想做一点SEO的东西。
# 一、云服务器配置
## ①建立博客存放的目录

- 我的目录是：`home/www/blog`


```bash
cd /home
mkdir www
cd /www
mkdir /blog
```

## ②安装nginx
- **搭建服务器环境，以nginx做服务器。**

```bash
1.安装
yum install -y nginx

2.启动服务器
systemctl start nginx
systemctl enable nginx

3.在浏览器地址栏中输入你的公网ip，如果打开了有内容网页，说明成功了。
```

- **配置服务器的路由：** 
通过公网ip访问，这个地址指向的是nginx，得让他指向我们的博客，就要修改配置文件，找到配置文件`etc/nginx/nginx.conf`,并不建议直接修改配置文件，应该先创建一个新的文件，然后采用`include`的方式，将这个文件包含进`nginx.conf`中。

1.新建配置文件


```bash
cd /etc/nginx/
mkdir vhost
cd vhost
vim blog.conf
```

2.编写配置文件

```bash
编辑blog.conf 如下：
server{
	listen  80;
	root /home/www/blog;这里填博客目录存放的地址
	server_name 这里填域名如(www.baidu.com) 如果暂时没有域名就填阿里云的公网ip，以后有了再改回来;
	location /{
	}
}
```
3.用`include`插入到`nginx.conf`中


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317193028631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

4.如果以后还想添加新的网站，也可以在vhost目录下新建一个conf配置文件。然后再用`include`装入到`nginx.conf`中。
5.（**先跳过这一步，等到最后有问题了，再回来**）最后可能会出现只能访问centOS的怪相，所以，如果出现那个问题了，请你回到这一步，增加一个操作，就是上图中那个`server`里面的`root`后面的路径，替换成你的博客存放位置：例如我的是`/home/www/blog`（这是我经验所得，可能你就会卡在这一步）。
## ③安装Nodejs
```bash
1.换源
curl -sL https://rpm.nodesource.com/setup_10.x | bash -

2.安装
yum install -y nodejs

3.检查版本
node -v
npm -v
```

## ④安装Git以及配置仓库
- **目的：** 使本地主机可以通过`ssh`方式连接到云服务器，我们就可以在本地使用`git`将我们的博客部署到服务器上。
1.安装`git`并查看版本。

```bash
1.安装
yum install git
2.查看版本
git --version
git version 1.8.3.1
```
2.新建`git`用户，并修改权限

```bash
1.新建git用户
adduser -m  git
2.修改用户权限
chmod 740 /etc/sudoers
vi /etc/sudoers
```
3.添加`Git  ALL=(ALL)   ALL`


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317200019229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

4.保存退出后，将权限改回去。

```bash
chmod 400 /etc/sudoers
```
5.设置`git`密码

```bash
passwd git 
```

6.切换到`git`用户，在`~`目录下，可以看到有一个`.ssh`文件夹

```bash
su git
cd ~
mkdir .ssh
cd /.ssh
```
7.生成公钥密钥文件

```bash
ssh-keygen
此时在目录下就会有两个文件，分别是id_rsa 和 id_rsa.pub
```
8.id_rsa.pub 就是公钥文件，将他复制一份。目录下就会多出一个authorized_keys文件，它和id_rsa.pub一模一样。
```bash
cp id_rsa.pub authorized_keys
```
9.修改权限

```bash
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```
10.在**本地主机**上打开`cmd`，使用`ssh`方式连接云服务器。

```bash
ssh -v git@123.57.60.151(这里填的是服务器公网ip)
```
11.输入密码之后，看到如图，即代表成功！！！！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317202421490.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
12.创建一个`git`的仓库，并且新建一个`post-receive`文件。

```bash
1.创建并初始化Git仓库
cd ~
git init --bare blog.git

2.新建post—receive文件
vi ~/blog.git/hooks/post-receive

3.输入以下内容：
git --work-tree=/home/www/website --git-dir=/home/git/blog.git checkout -f

4.保存退出之后赋予可执行权限
chmod +x ~/blog.git/hooks/post-receive
```
**至此，服务器端的配置完成！！！！！！！！**

 
# 二、本地主机配置
**说明：** 因为我之前没有云服务器的时候，是把博客部署在**github**仓库中的，所以，在下面的`config.yml`中会出现两个仓库，这并不影响，他会同时部署到`github`中和云服务器上，想要了解的话可以去看这篇文章：[https://blog.csdn.net/weixin_44861399/article/details/104673527](https://blog.csdn.net/weixin_44861399/article/details/104673527)，下面就是正式的，将本地主机与服务器连接起来的具体步骤了。
## ①安装git
下载地址：[https://git-scm.com/download/win](https://git-scm.com/download/win)
## ②安装Node.js
下载地址：[http://nodejs.org/download/](http://nodejs.org/download/)
- 安装的时候，选择`add to path`，他会自动配置环境变量。

```bash
node -v
npm -v
出现版本号说明安装成功
```
- 换镜像（亲身体验，可以省去以后好多麻烦）

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```


## ③安装Hexo
 - 用cnpm全局安装，在桌面右键打开`Git bash here`输入：


```bash
cnpm install -g hexo-cli
```
 - 安装完成后，在命令行里输入`hexo -v`检查是否安装成功。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305155050299.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
## ④hexo本地部署
 - 在本地新建一个文件夹，我命名它为blog
 - 在这个文件夹下，右键打开`Git Bash here`，输入`hexo init`，我在这个地方出现了一个`WARN`，暂时不用管它。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305135143147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 继续输入`cnpm install`可以看到blog文件夹里出现：


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305155919286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 - 输入：`hexo g`
 - 输入：`hexo s` 


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305135917128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

 - 打开浏览器输入`localhost：4000`，可以看到部署好了，有一篇`hello World`的博客


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200305154439378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
## ⑤连接到远端服务器
 - 安装 `deployer` 插件


```bash
cnpm install hexo-deployer-git --save
```
 - 打开`F:\blog\_config.yml`,找到 `deploy`。填写你自己的仓库名字。不能同时又相同的`repo`，需要删除一个。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317204903306.png)
**至此，本地主机的配置也完毕了！！！！**
# 三、发布文章
- 写一篇文章：

```bash
1.新建文章，并编写，如何编写，百度
hexo new "Hello My First Blog"

2. 发布(要在blog文件夹下打开git)
hexo clean && hexo generate --deploy
```
- 重启服务器的nginx

```bash
nginx -s reload
```
- 浏览器地址栏中输入公网ip查看成果。

# 四、美化博客
请参考我的这篇文章：[https://blog.csdn.net/weixin_44861399/article/details/104646946](https://blog.csdn.net/weixin_44861399/article/details/104646946)
