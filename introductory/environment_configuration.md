# 环境管理

本章节主要内容就是安装Python环境，这里我们以Python3中目前(2018.9)应用最广泛的版本Python3.6位基础。

## 1、安装Python

我们书写的文件是**xxx.py**文件，然后会通过Python的解释器来运行这个py文件，或者是Python项目，如果没有解释器，否则始终只是一个代码而已了。

我们这里会讲解在Windows安装Python3.6 和 CentOS7上安装Python3.6 。

### 1.1 Windows安装Python3.6

鉴于Windows安装过于简单，这里不在阐述，推荐阅读网友写的，传送门: https://www.cnblogs.com/xzc1995/p/7039855.html

### 1.2 CentOS7安装Python3.6

首先我们需要去官方获取或者下载你需要的源码部分，我们通过在服务器进行编译安装即可（获取途径: https://www.python.org/downloads/source/）。

最终我这里获取的版本连接是: https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz

**安装之前我们需要先安装依赖**：

```shell
$ sudo yum -y install zlib-devel \
               bzip2-devel openssl-devel ncurses-devel \
               sqlite-devel readline-devel tk-devel \
               gdbm-devel db4-devel libpcap-devel xz-devel
```

**下载Python源码**：

```shell
$ cd /usr/local/src/         #<--这里是我存放源码包的地方，纯属个人习惯，用户可随意，建议有一个习惯
$ sudo wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz   #<--下载到当前目录
```

**解压安装包**：

```shell
$ tar xf Python-3.6.5.tgz    #<-- 这个是直接解压到当前目录，如果需要解压到指定目录需要 —C dir
$ cd Python-3.6.5
```

**进行编译安装**：

```shell
$ ./configure --prefix=/usr/local/python3.6.5   #<--如果是普通用户，可以安装到自己家目录，不过更推荐使用虚拟环境(后面会有讲解)
$ sudo make -j 4 && sudo make install           #<--这个4是CPU的核心数量，建议根据自己环境适当调整，加快编译速度
```

**收尾工作**：

```shell
$ sudo ln -s /usr/local/python3.6.5 /usr/local/python3  #<--方便后期进行版本升级
$ echo -e '# Python PATH\nexport PATH=$PATH:/usr/local/python3/bin' >>/etc/profile 
$ source /etc/profile
```

> 配置环境变量，这里我将$PATH放置到前面，防止新安装的这个python和系统的Python2冲突，所以需要注意了。

升级pip:（非必须）

```shell
$ python3 -m pip install --upgrade pip
```

配置国内pip源：（非必须，主要是加快pip的下载速度）

```shell
$ cat /etc/pip.conf 
[global]
trusted-host =  pypi.douban.com
index-url = http://pypi.douban.com/simple
```

> 编辑内容如上，可通过vim或者vi来完成。

## 2、虚拟环境

为啥需要使用虚拟环境呢，如果刚开始用Python你可能还没有感觉，我希望当你使用一段时间后重新来考虑这个问题。

我简单举两个例子吧

- 开发人员需要在服务器上安装一个软件，但是如果给与pip的超级权限就会破坏全局Python的影响所有用户，这样给用户自己家目录里面搞一个Python，就防止破坏全局的Python同时权限也给限制住了，推荐工具**pipenv**；
- 一台服务器可能部署两个Python的应用，一个应用基于Django1.11开发(Python中很牛*的一款WEB框架)，而另外一款则是基于Django2.1开发的，但是Django1.11和Django2.1的语法有冲突不能一起使用，所以就尴尬了，这里我推荐使用python3 自带的**venv**模块；

### 2.1 初级使用pipenv

pipenv 是 Python 项目的依赖管理器。如果您熟悉 Node.js 的 npm 或 Ruby 的 bundler，那么它们在思路上与这些工具类似。尽管 pip 可以安装 Python 包， 但仍推荐使用 Pipenv，因为它是一种更高级的工具，可简化依赖关系管理的常见使用情况。

使用pip来安装pipenv：

```shell
$ sudo pip3 install pipenv 
```

> 如果是安装当前用户的话可以使用 sudo pip install --user pipenv

给一个项目创建虚拟环境：

```shell
$ cd myproject
$ pipenv install
```

> 这里默认就会在当前用户的家目录位置: ~/.local/share/virtualenvs 下创建一个Python的虚拟环境。

在安装完成后，我们操作则就是这个虚拟环境的python的，如何激活这个虚拟环境呢: 

```shell
$ pipenv shell   #<--激活虚拟环境
$ exit           #<--或者Ctrl+d退出
```

安装第三方库和删除第三方库：

```shell
$ pipenv install requests
$ pipenv install django==1.11
$ pipenv uninstall django==1.11
```

> **特别注意：** 执行pipenv install 或者pipenv shell时，如果没有在项目目录下的话，将会在当前目录创建一个虚拟环境。
>
> 语法基本是和pip一样的，这个就是pip + venv的结合产物，非常推荐开发者使用，这里只是冰山一角，详细可以去官网了解: https://pypi.org/project/pipenv/

### 2.2 python3 venv使用

venv多用于服务器的一些使用，下面简单介绍下。

安装venv：venv是Python3的一个官方包，不需要而外安装。

创建一个虚拟环境:

```shell
$ cd myproject
$ python3 -m venv venv  #<-- 最后这个venv是一个目录
```

激活终端：

```shell
$ source bin/activate
```

> Linux上可以通过 which python 来看看你python解释器的位置，聪明的你一定发现了不一样的地方了。
>
> 虚拟化环境就是虚化的另外一个python解释器，你可以通过激活终端让他帮你重载环境变量，或者你亦可全路径直接直接python，比如: **myproject/venv/Scripts/python**

## 3、编程工具推荐

下面推荐我常用的编程工具如下：

- Git: 代码版本管理；
- Pycharm：属于Python自己的IDE；
- shadowsock：帮助你加速访问Github，和国外的一些好的编程网站及Google搜索；
- vscode：专业强悍的编辑器，我想有机会熟练使用一个编辑器；
- SecureCRT：一款SSH连接工具，方便你登录服务器进行远程管理和调试应用；
- Typora：书写Markdown笔记工具，可以支持导出PDF，Html等多种格式；

等等，还有很多，上面是比较常用的了。

> 好了我们工具和环境准备好了，开启我们Python远航之路吧。



