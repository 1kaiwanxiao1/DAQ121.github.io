---
title: Ubuntu18.04系统安装，修改源
toc: true
date: 2020-02-14 22:51:27
tags: linux
categories: linux学习笔记
abbrlink: linux

---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于疫情在家，看着家里有一台吃了一两年灰的台式电脑，大概是初三的时候买的，抹一下灰就跟新的一样，哈哈，于是突然心生一计，就开始动手整理这台电脑，首先重装系统，不过这次，我装的是win10+Ubuntu18.04双系统。
<!--more-->

# 一. 准备：


- **终端如何复制粘贴？**
 在文本区域左键复制，到终端里面直接按下鼠标滚轮即可。
- **如何切换root权限？**
 安装之后root是不设置密码的，以普通用户输入su  root按照步骤设置自己的密码

## 工具：

 - 大于4G的U盘一只，事先将里面的文件备份到电脑中。

### 第一步：

下载Ubuntu系统镜像-------后缀.iso的配置文件。因为是外网，所以去官网下载需要自备**（真是把我累伤心了），于是我找到了国内的开源网站：[https://blog.csdn.net/davidhzq/article/details/102575343](http://mirrors.163.com/ubuntu-releases/14.04/)

 ### 第二步：

  制作U盘启动盘-----需要下载USBWiter这个工具用来写入ISO镜像，下载地址：[http://mydown.yesky.com/pcsoft/413551662.html](http://mydown.yesky.com/pcsoft/413551662.html)
 解压后打开小工具：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209220859288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)


 ### 第三步：

 还要在硬盘腾出一块空间用来安装Ubuntu系统，具体操作如下：以管理的身份打开“我的电脑”，如图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209221926433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209222255142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

 压缩完之后，就不用管了，会看到你刚才压缩的那个卷之后，会有一块空闲的空间。

 # 二.开始安装：
 

### 第四步：

 - 拿着你做好的U盘，插到你要安装系统的电脑上，在开机的时候，会看到：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020021012440214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

 - 如果没有的话，可能是你开机设置里不允许用U盘启动，我当时就遇到了这种情况，然后狂点F12，进入BIOS，主要是为了进入BIOS，不同型号的电脑按键不一样，自己上网查，按如下图设置后，按F10，保存并重启。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020021012462934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

### 第五步：

![在这里插入图片描述](https://img-blog.csdnimg.cn/202002092238476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209223948185.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209224129521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209224527382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209224741212.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/2020020922495535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

**后面就一步一步点击继续就可以了，最后等待安装完成**

## 在安装过程中遇到的问题：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210125455148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210125505598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210125606591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

最后的最后，还是一个人抗下了所有，安装崩溃了，我心态也奔溃了，但是重振旗鼓，找到问题：GRUB的引导问题，也找到了解决方案：[https://blog.csdn.net/weixin_44123547/article/details/103243238](https://blog.csdn.net/weixin_44123547/article/details/103243238)
最终还是安装完成了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210125944723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg2MTM5OQ==,size_16,color_FFFFFF,t_70)

# 三. 修改源：

因为Ubuntu默认下载源是国外源，我们修改为国内源，我改的是清华的源。
打开终端，**切换到root权限**输入以下命令：

## 1、备份

```bash
cp /etc/apt/sources.list /etc/apt/sources.list.bak
```
## 2、修改sources.list文件

```bash
vi sources.list
```
## 3、修改源（复制进去）

```bash
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security multiverse
```
## 4、 更新源

```bash
sudo apt-get update
```

完成后，输入：

```bash
sudo apt-get upgrade
```
## 5、大功告成

