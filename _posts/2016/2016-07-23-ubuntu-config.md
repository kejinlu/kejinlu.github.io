---
layout: post
title: Ubuntu使用环境配置
categories:
- Programming
- Linux
tags:
- Linux
- Ubuntu
- pyenv
- jdk
---
<img src="http://wx4.sinaimg.cn/mw1024/65cc0af7gw1f67pfmy2y2j21hc0xc1kx.jpg" style="width: 80%; height: 80%"/>

还记得大学那会儿，特别爱折腾系统，装的最多估计就是Ubuntu系统了，也是从那时候起开始接触Linux，工作之后也就懒得折腾了，选择了同属UNIX系得macOS 。所以很长的时间里就一直使用macOS了。

去年家里买了一个NAS，当时考虑的因素有做工，盘位，系统。最后选择QNAP TS-453 Pro，当时为了便宜选择了从computeruniverse海淘，即使加上关税到手也比国内行货便宜一两千。虽然QNAP的系统比不上群晖，但是这两年已经有很大的改观，用户体验有追赶群晖的趋势。 

QNAP TS-453 Pro 四核，四盘位，四千兆网口，内存升级到8G。内置的HD Station支持HDMI显示，HD Station支持一些简单的App，比如Chrome，Kodi，还是比较方便的。TS-453 Pro还支持虚拟化技术，通过Virtualization Station可以安装各种虚拟机，应该是基于KVM技术的；另外Container Station还支持Docker容器，所以使用起来想象空间很大。 没想到今年新的系统开始支持Linux Station，可以有完整的Linux的桌面体验了，当然Linux Station开启后，HD Station 会被关闭，因为都是通过HDMI接口来进行显示的输出。Linux Station是基于LXC来实现的，所以性能优越和原生基本无异。

目前Linux Station还只支持Ubuntu 14.04和16.04这两个LTS版本。想想NAS基本24小时开机，配置个简单的Ubuntu的工作环境，方便平时写写代码，写写文档。在QTS的Linux Station中安装好Ubuntu 16.04，以及确认开启Linux Station后，便可以在显示器上进行操作了。

简单的想了下，想搭建下python和Java的开发环境，以及装一些必要的软件。


### 1. python工作环境配置
Ubuntu 16.04安装好了之后默认就带了2.x和3.x的python版本，但是为了更方便的进行版本管理，决定通过pyenv来安装和管理python版本。
安装pyenv之前先装下git

```bash
sudo apt-get install git
```

然后进行pyenv的安装

```bash
git clone https://github.com/yyuu/pyenv.git ~/.pyenv
```

然后在～/.bashrc 末尾加上下面三行代码

```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

然后重启Terminal，输入pyenv看命令是否起作用。

下面便可以通过pyenv来安装你所需要的python的版本了，比如

```bash
pyenv install 3.5.1
```

这个时候你会发现 悲剧发生了，由于国内的特定网络原因，python安装的下载速度及其缓慢，基本是10k以内的速度无法忍受（突然想到以前python官网一度无法访问），好在还是由办法解决这个问题的，你可以先想办法到官网把安装包单独下载好，比如我讲下载好的包`Python-3.5.1.tar.xz`放到`～/Downloads`目录下，然后指定build缓存目录后，再运行install命令

```bash
export PYTHON_BUILD_CACHE_PATH=~/Downloads
pyenv install 3.5.1
pyenv rehash
```

安装多个版本的python之后可以通过下面的命令来切换全局的python的版本

```bash
pyenv global 3.5.1
```

安装后对应版本的pip也是安装好了的。

最近对深度学习有点感兴趣，所以正好打算装个TensorFlow玩一玩，找到官方的安装指南，选择正确的版本进行安装， [https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#pip-installation](https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#pip-installation), NAS的配置注定只能使用CPU的，然后选择对应的python版本和Ubuntu的架构版本

```bash
export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.9.0-cp35-cp35m-linux_x86_64.whl
pip install --upgrade $TF_BINARY_URL
```

这个时候你会发现老问题又来了，pip的库在国内访问慢，所以这个时候你最好指定一个国内的pip源进行安装，这里选择豆瓣的源

```bash
pip install --upgrade $TF_BINARY_URL  -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
```

另外如果你想选择一款python的ide可以选择Pycharm的社区版，一般情况下社区版够用了。

### 2.配置Java环境
Java目前一般有open jdk，还有oracle jdk，这里安装oracle jdk，你可以自己到oracle官网下载，手动进行安装，也可以通过添加源的方式使用apt-get工具进行安装

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

安装好了之后可以进行配置，比如如果你系统中安装有多个版本的jdk，那么就可以通过下面的方式来指定默认使用的jdk的版本

```bash
sudo update-alternatives --config java
```

### 3.其他常用软件安装
- 对应一些独立的第三方下载的deb包，推荐使用GDebi Package Installer （`sudo apt-get install gdebi`）
-  中文输入法推荐搜狗输入法，安装搜狗输入法前先安装依赖的包 （`sudo apt-get install fcitx libssh2-1`），系统设置中将输入法系统设置为fcitx，然后下载搜狗输入法的包，可以从这里下载 [http://download.pchome.net/utility/lan/ime/download-3955.html](http://download.pchome.net/utility/lan/ime/download-3955.html) ,官网的包安装有问题
- 编辑器可以使用github的Atom或者Sublime Text 3
- markdown编辑器推荐使用haroopad
- 系统优化配置可以使用Unity Tweak Tool （`sudo apt-get install unity-tweak-tool`）， 此工具可以设定诸如Launcher的位置等
- 实时顶端状态栏显示系统cpu内存等信息可以通过 indicator multiload 来shi实现 （`sudo apt-get install indicator-multiload`）
- 中文字体可以选择微软雅黑，首先到Windows系统下拷贝过来字体文件，进行下列操作之后，便可以选择微软雅黑字体了

	```bash
sudo cp msyh.ttf /usr/share/fonts/
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -fv
```


