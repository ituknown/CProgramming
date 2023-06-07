Linux 各发行版默认都会 GCC 编译器，不过版本通常都较低。Ubuntu 还好，软件库比较前沿，可以直接使用 apt 命令安装制定版本 GCC。但如果是 CentOS 或者 Debian 就没办法，软件库通常都比较老旧，就没办法直接使用 yum 或者 apt 命令安装新版 GCC 了，这种清理下也只能选择编译安装了。下面就以 Debian 作说明。

|**Note**|
|:-------|
|编译安装很慢很慢，通常需要两到三个小时才能完成。|

# 安装前准备

在开始之前我们需要做些准备工作，安装下后续需要使用的命令：

```bash
$ sudo apt install -y gcc make wget
```


# 下载指定版本 GCC

编译安装 GCC 需要提前到 GCC 官网镜像站（[https://gcc.gnu.org/mirrors.html](https://gcc.gnu.org/mirrors.html)）下载指定版本的 GCC 源码安装包。需要说明的是，每个版本通常都包含 2 种格式的压缩包，分别为 tar.gz 和 tar.xz。只是压缩格式不同，随意选择即可。这里以 tar.gz 压缩包为例。

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
$ sudo mkdir gcc-11.3.0-build
$ cd gcc-11.3.0-build
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

## configure 参数说明

上述命令执行完成之后，会将应用程序安装到 /usr/local 目录下，对应的二进制文件会安装到 bin 目录下。因此，当你构建完成之后就会发现 gcc 和 g++ 等软件都在 /usr/local/bin 目录下：

```bash
$ which gcc g++ cpp
/usr/local/bin/gcc
/usr/local/bin/g++
/usr/local/bin/cpp
```

这些都是 configure 参数的默认选项，具体可以使用 `--help` 命令查看，下面是部分需要注意的可选参数：

```bash
$ sudo ../gcc-11.3.0/configure --help

Installation directories:
  --prefix=PREFIX         install architecture-independent files in PREFIX
                          [/usr/local]
  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
                          [PREFIX]

Fine tuning of the installation directories:
  --bindir=DIR            user executables [EPREFIX/bin]
  --sbindir=DIR           system admin executables [EPREFIX/sbin]
  --libdir=DIR            object code libraries [EPREFIX/lib]
  --includedir=DIR        C header files [PREFIX/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]

Program names:
  --program-prefix=PREFIX            prepend PREFIX to installed program names
  --program-suffix=SUFFIX            append SUFFIX to installed program names
  --program-transform-name=PROGRAM   run sed PROGRAM on installed program names
```

现在就解释了为什么构建后的应用程序会在 /usr/local/bin 目录下了。在通常情况下这些参数直接使用默认值即可，不过如果需要构建多个版本的 GCC 我觉得有必要使用 `--program-suffix` 参数。

比如我们现在构建了 GCC-11 这个版本，对应的应用程序安装到 /usr/local/bin 目录下。之后如果想再构建一个 GCC-12 呢？应用程序也被安装到 /usr/local/bin 目录下，等等... 似乎好像有些问题？现在 /usr/local/bin 目录下的 gcc/g++ 到底是哪个版本呢？具体就要去验证下了。

解决这个问题我觉得很简单，在构建时使用 `--program-suffix=` 参数为构建后的程序名加个后缀即可。譬如上面构建的 GCC-11，我加上一个 `-11` 后缀，生成的应用程序就不是 gcc 而是 gcc-11 了：

```bash
$ sudo ../gcc-11.3.0/configure --program-suffix=-11 --enable-checking=release --enable-languages=c,c++ --disable-multilib
```

之后如果想要构建 GCC-12 继续增加一个后缀就能解决名字冲突的问题了：

```bash
$ sudo ../gcc-12.3.0/configure --program-suffix=-12 --enable-checking=release --enable-languages=c,c++ --disable-multilib
```

当然，在使用时就没办法直接使用 `gcc` 命令了，而是 `gcc-11` 命令。解决办法也很简单，建一个软链接不就好了：

```bash
# 使用 gcc-11
$ sudo ln -sf /usr/local/bin/gcc-11 /usr/local/bin/gcc

# 使用 gcc-12
$ sudo ln -sf /usr/local/bin/gcc-12 /usr/local/bin/gcc
```

# 查看 GCC 版本

编译安装完成之后就可以看到构建的版本信息了：

```bash
$ gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/local/libexec/gcc/x86_64-pc-linux-gnu/11.3.0/lto-wrapper
Target: x86_64-pc-linux-gnu
Configured with: ../gcc-11.3.0/configure --enable-checking=release --enable-languages=c,c++ --disable-multilib
Thread model: posix
Supported LTO compression algorithms: zlib
gcc version 11.3.0 (GCC)
```