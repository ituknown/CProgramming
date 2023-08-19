Qt 从 5.15 之后不再提供离线安装包（除非你氪金），只能通过安装器进行在线安装。在线安装器很小，只有几十兆而已。不过 Qt 安装起来有几十个G大小，显然直接使用官网在线安装不太可行，因此通常都会将安装器替换为国内镜像源进行安装。

首先需要下载 qt 在线安装器，可以直接在官网下载：[https://download.qt.io/official_releases/online_installers](https://download.qt.io/official_releases/online_installers)。

如果觉得网络不太行，推荐使用南京大学 Qt 镜像帮助页：[https://mirror.nju.edu.cn/mirrorz-help/qt/](https://mirror.nju.edu.cn/mirrorz-help/qt/)，该帮助页提供了安装器镜像地址下载链接。

在下完成后你会得到一个 installer：

| **操作系统** | **installer**                     |
| :----------- | :-------------------------------- |
| Linux        | qt-unified-linux-x64-online.run   |
| Windows      | qt-unified-windows-x64-online.exe |
| Mac          | qt-unified-mac-x64-online.dmg     |

如果直接双击启动，默认会使用官网通道。如果想要使用镜像源需要在 Terminal 中执行如下命令启动在线安装器：

```bash
installer --mirror [镜像地址]
```

比较推荐的镜像地址有如下几个：

| **镜像站** | **镜像地址**                                                 |
| :--------- | :----------------------------------------------------------- |
| 南京大学   | [https://mirror.nju.edu.cn/qt](https://mirror.nju.edu.cn/qt) |
| 清华大学   | [http://mirrors.tuna.tsinghua.edu.cn/qt](http://mirrors.tuna.tsinghua.edu.cn/qt) |
| 阿里云     | [https://mirrors.aliyun.com/qt](https://mirrors.aliyun.com/qt) |
| 腾讯云     | [https://mirrors.cloud.tencent.com/qt](https://mirrors.cloud.tencent.com/qt) |

以 Windows 为例，使用南京大学镜像地址启动安装器：

```powershell
.\qt-unified-windows-x64-online.exe --mirror https://mirror.nju.edu.cn/qt
```

之后就会出现安装器界面，登录后选择组件进行安装即可。


--


如果是 Mac 用户，无法直接使用上面的方法。需要按照如下步骤进行：

双击 **online-installer.dmg** 文件 - **右键 online-install.app** - 选择 **显示包内容**：

![show-package-contents.png](http://cpp-media.knowledge.ituknown.cn/qt/online_installers/mac_set_mirror/show-package-contents.png)

之后进入 MacOS 文件夹，将 qt-unified-macOS-x64-online 安装程序拖到终端去，再指定镜像地址即可：

![set-mirror.png](http://cpp-media.knowledge.ituknown.cn/qt/online_installers/mac_set_mirror/set-mirror.png)

之后就会出现安装器界面，登录后选择组件进行安装即可。





