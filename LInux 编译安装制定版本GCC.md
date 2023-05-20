Linux 各发行版默认都会 GCC 编译器，不过版本通常都较低。Ubuntu 还好，软件库比较前沿，可以直接使用 apt 命令安装制定版本 GCC。但如果是 CentOS 或者 Debian 就没办法，软件库通常都比较老旧，就没办法直接使用 yum 或者 apt 命令安装新版 GCC 了，这种清理下也只能选择编译安装了。下面就以 Debian 作说明。

|**Note**|
|:-------|
|编译安装很慢很慢，通常需要两到三个小时才能完成。|

# 安装前准备

在开始之前我们需要做些准备工作，安装下后续需要使用的命令：

```bash
$ sudo apt install -y gcc make wget
```


# 下载制定版本 GCC

编译安装 GCC 需要提前到 GCC 官网镜像站（https://gcc.gnu.org/mirrors.html）下载指定版本的 GCC 源码安装包。需要说明的是，每个版本通常都包含 2 种格式的压缩包，分别为 tar.gz 和 tar.xz。只是压缩格式不同，随意选择即可。这里以 tar.gz 压缩包为例。

GCC 镜像站，根据自己网络情况自行选择，比如这里我选择的是美国洛杉矶镜像站，GCC 版本为 11.3.0：

```bash
$ wget https://bigsearcher.com/mirrors/gcc/releases/gcc-11.3.0/gcc-11.3.0.tar.gz
```

下载完成后将将其解压至 /usr/local/ 目录下：

```bash
$ sudo tar xvf gcc-11.3.0.tar.gz -C /usr/local
```

之后在 /usr/local 目录下就能看到对应的 gcc 文件夹了：

```
$ ls
bin  etc  games  gcc-11.3.0  go  include  lib  man  sbin  share  src
                [----------]
```

# 下载GCC 所需要的依赖包

按照下面的命令开始一步一步执行即可：

```bash
$ cd /usr/local/gcc-11.3.0
$ sudo ./contrib/download_prerequisites
```

# 建立编译后文件存储目录

完成以上准备工作之后，就正式进入安装 GCC 编译器的环节。首先，我们需要手动创建一个目录（目录名任意），用于存放编译 GCC 源码包生成的文件。执行如下命令：

```bash
$ cd /usr/local
$ sudo mkdir gcc-build-11.3.0
$ cd gcc-build-11.3.0
```

#  编译安装

需要特别强调一点，GCC 是一个编译器集合，支持多种编成语言（如 c、c++、java、objc、obj-c++、go）。而实际情况中我们可能只需要编译 1~2 种编程语言，因此需要对其进行做些必要的配置。这里我只选择 C 和 C++ 语言（`--enable-languages`）：

```bash
$ sudo ../gcc-11.3.0/configure --enable-checking=release --enable-languages=c,c++ --disable-multilib
```

然后就可以开始安装了：

```bash
$ sudo make && sudo make install
```

之后就是一个漫长的等待过程，Two Thousand Years Later～

# 查看 GCC 版本

```bash
$ gcc --version
```