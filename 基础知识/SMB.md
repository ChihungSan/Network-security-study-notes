# SMB

服务器消息快协议（SMB）用于在局域网上直接共享文件，使用445端口。不同的版本叫法不一样。

而Microsoft-DS 是一种基于SMB协议的服务，允许文件和打印机共享。

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.9rjxoghsrm.webp)

如何进入靶机的SMB进行文件共享：

首先确认靶机是否开启SMB文件共享。使用net share查看靶机Windows server 开启了哪些共享。

靶机ip: 192.168.235.132

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.8dxekf6v64.webp)

在物理机上尝试连接，使用\\ip的方式连接靶机

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.4ubgum48lc.webp)

填写正确的账户和密码即可（即登陆的账号）

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.3goxqkt9rt.webp)

然后输入C$即可访问其共享的C盘

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.70avgdwbre.webp)

如何关闭SMB文件共享：

管理员权限，关闭默认共享命令如下：

![img](https://ChihungSan.github.io/picx-images-hosting/jichuzhishi/image.4qruwwbp8r.webp)