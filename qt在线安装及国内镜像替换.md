Qt 从 5.15 之后不再提供离线安装包（除非你氪金成为商业用户），只能通过安装器（installer）进行在线安装。在线安装器很小，只有区区几十兆而已。

不过 Qt 安装起来有几十个G大小（Qt5.12有55G），直接使用安装器在线安装显然不太可行，普遍的做法是将安装器官方网络通道替换为国内镜像地址进行安装。

在线安装器可以直接在 Qt 官网下载（[https://download.qt.io/official_releases/online_installers](https://download.qt.io/official_releases/online_installers)）。不过，如果你对自己的网络不够自信的话也可以直接到国内的镜像站进行下载。推荐使用南京大学 Qt 镜像帮助页（[https://mirror.nju.edu.cn/mirrorz-help/qt/](https://mirror.nju.edu.cn/mirrorz-help/qt/)），该帮助页提供了安装器下载链接。

下面是安装器 installer 在不同平台对应的名称：

| **操作系统** | **installer**                     |
| :----------- | :-------------------------------- |
| Linux        | qt-unified-linux-x64-online.run   |
| Windows      | qt-unified-windows-x64-online.exe |
| Mac          | qt-unified-mac-x64-online.dmg     |

如果直接双击启动，默认会使用官网通道（并不推荐）。我们需要通过 Terminal 执行如下命令启动在线安装器：

```bash
installer --mirror [镜像地址]
```

将镜像地址替换为国内源即可，比较推荐的镜像地址有如下几个：

| **镜像站** | **镜像地址**                                                 |
| :--------- | :----------------------------------------------------------- |
| 南京大学   | [https://mirror.nju.edu.cn/qt](https://mirror.nju.edu.cn/qt) |
| 清华大学   | [https://mirrors.tuna.tsinghua.edu.cn/qt](http://mirrors.tuna.tsinghua.edu.cn/qt) |
| 阿里云     | [https://mirrors.aliyun.com/qt](https://mirrors.aliyun.com/qt) |
| 腾讯云     | [https://mirrors.cloud.tencent.com/qt](https://mirrors.cloud.tencent.com/qt) |

以 Windows 为例，使用南京大学镜像地址启动安装器：

```powershell
.\qt-unified-windows-x64-online.exe --mirror https://mirror.nju.edu.cn/qt
```

之后就会出现安装器界面，登录后选择组件进行安装即可，可以看到使用镜像地址后网络带宽可以直接拉满：

![windows_installing.png](http://cprogramming-media.knowledge.ituknown.cn/qt/online_installers/windows_installing.png)

--


如果是 Mac 用户，无法直接使用上面的方法。需要按照如下步骤进行：

双击 **online-installer.dmg** 文件 » **右键 online-install.app** » 选择 **显示包内容**：

![mac_show_package_contents.png](http://cprogramming-media.knowledge.ituknown.cn/qt/online_installers/mac_show_package_contents.png)

之后进入 MacOS 文件夹，将 qt-unified-macOS-x64-online 安装程序拖到终端去，再指定镜像地址即可：

![mac_set_mirror.png](http://cprogramming-media.knowledge.ituknown.cn/qt/online_installers/mac_set_mirror.png)

之后就会出现安装器界面，登录后选择组件进行安装即可。