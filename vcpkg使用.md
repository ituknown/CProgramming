# 安装 vcpkg

从 GitHub 上克隆 vcpkg 仓库，命令如下：

```bash
$ git clone git@github.com:microsoft/vcpkg.git

# 不过我一般都是安装到指定目录
$ mkdir -p /opt/vcpkg
$ git clone git@github.com:microsoft/vcpkg.git /opt/vcpkg
```

进入 vcpkg 目录：

```bash
$ cd vcpkg

# 或指定目录
$ cd /opt/vcpkg
```

使用 Bootstrap 脚本安装 vcpkg：

```bash
# UNIX-Like 执行
$ ./bootstrap-vcpkg.sh

# Windwos 执行
.\bootstrap-vcpkg.bat
```

命令执行完成后，会在当前目录下生成 vcpkg 或 vcpkg.exe 文件，之后直接使用该程序即可。不过我比较喜欢直接配置到环境变量中，以便于在任何位置执行，下面是 UNIX-Like 环境变了配置示例：

```bash
export VCPKG_ROOT=/Users/ituknown/Vcpkg
export PATH=$PATH:$VCPKG_ROOT
```

# vcpkg目录说明

当vcpkg安装完成后，在目录下通常会多出如下文件夹：

| **目录**   | **说明**                                                     |
| :--------- | :----------------------------------------------------------- |
| buildtrees | 包含从中生成每个库的源的子文件夹，一般在xxxx.clean文件夹下。 |
| docs       | 文档和示例。                                                 |
| downloads  | 所有已下载的工具或源的缓存副本（运行安装命令时，vcpkg 会首先搜索此处）。 |
| installed  | 包含每个已安装库的标头和二进制文件（与 Visual Studio 集成时，实质上是相当于告知它将此文件夹添加到其搜索路径）。 |
| packages   | 在不同的安装之间用于暂存的内部文件夹。                       |
| ports      | 用于描述每个库的目录、版本和下载位置的文件（如有需要，可添加自己的端口）。 |
| scripts    | 由 vcpkg 使用的脚本（CMake、PowerShell），另外与 IDE（如 CLion 集成时也需要在 CMake options 中指定该目录）。 |
| toolsrc    | vcpkg 和相关组件的 C++ 源代码。                              |
| triplets   | 包含每个受支持目标平台（如 x86-windows 或 x64-uwp）的设置。  |

# 常用命令

- 查找库，不加库名可以查看全部

  ```bash
  $ vcpkg search openssl
  ```

- 查看支持的架构平台

  ```bash
  $ vcpkg help triplet
  Available architecture triplets:
  vcpkg built-in triplets:
    x64-linux
    x64-windows
    x64-windows-static
    x64-osx
  ...
  ```

- 安装依赖

  ```bash
  $ vcpkg install openssl
  # 安装指定架构平台
  $ vcpkg install openssl:x64-linux
  ```

- 列出已安装的开源库

  ```bash
  $ vcpkg list
  ...
  jsoncpp:x64-osx                                   1.9.5               jsoncpp is an implementation of a JSON
  zlib:x64-osx                                      1.2.13              A compression library
  ```



# 集成到CLion

针对项目配置，依次打开：Preferences | Build, Execution, Deployment | CMake。

在 CMake options 中增加如下配置：

```
-DCMAKE_TOOLCHAIN_FILE=${VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake
```

| **NOTE**                                                   |
| :--------------------------------------------------------- |
| 需要将 `${VCPKG_ROOT}` 替换为你的安装路径，如 /opt/vcpkg。 |

之后就可以在项目根目录下执行 vcpkg 命令安装依赖了，如 安装 zlib：

```bash
$ vcpkg install zlib
Computing installation plan...
The following packages will be built and installed:
    zlib:x64-osx -> 1.2.13
Detecting compiler hash for triplet x64-osx...
...
Total install time: 6.2 s
The package zlib is compatible with built-in CMake targets:

    find_package(ZLIB REQUIRED)
    target_link_libraries(main PRIVATE ZLIB::ZLIB)

```

安装成功后会在最后打印具体的使用使用方法，将最后两行拷贝到项目的  CMakeLists.txt 中（记得将 main 替换为项目名）：

```cmake
# 项目名
project(example_vcpkg)

# 添加如下内容
find_package(ZLIB REQUIRED)
target_link_libraries(example_vcpkg PRIVATE ZLIB::ZLIB) # 将 main 替换为 example_vcpkg
```

之后就可以在项目中 include ZLIB 的头文件了~



# 常见问题

- Could not find pkg-config.  Please install it via your package manager...

缺少 pkg-config，一般会在后面提示具体的安装命令，安装即可。如 mac：

```bash
$ brew install pkg-config
```



