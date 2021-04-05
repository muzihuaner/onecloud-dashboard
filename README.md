# Onecloud-Dashboard



Onecloud-Dashboard (玩客云-仪表盘) 是一个开源的 IoT 设备监控工具，目前主要针对玩客云平台，也尽可能兼容其他矿渣硬件产品。你只需要在玩客云上安装好 PHP 服务器环境，即可方便的部署一个 玩客云仪表盘，通过炫酷的 WebUI 来监控玩客云的状态！

目前已加入的监测项目有：

- CPU 基本信息、状态和使用率等实时数据
- 内存、缓存、SWAP分区使用的实时数据
- SD卡（磁盘）的占用情况
- 实时负载数据
- 实施进程数据
- 网络接口的实时数据
- 玩客云IP、运行时间、操作系统、HOST 等基础信息

### 演示地址

http://nas.quickso.cn/onecloud-dashboard/index.php

#### 仪表盘预览

![image-20210405105130216](https://cdn.jsdelivr.net/gh/muzihuaner/huancdn/img/20210405105130.png)

#### 安装方法

安装共2步，首先安装 Nginx（或 Apache）和 PHP。然后在 Nginx 目录通过 SFTP 或 GitHub 部署好本项目的程序。

##### 1.安装 Nginx 和 PHP（如果你安装了，跳过这步）

在 玩客云 的终端运行以下命令：

```
sudo apt-get update
sudo apt-get install nginx php7.3-fpm php7.3-cli php7.3-curl php7.3-gd php7.3-cgi
sudo service nginx start
sudo service php7.3-fpm restart
```

如果安装成功，可通过 `http://玩客云IP` 访问到 Nginx 的默认页。Nginx 的根目录在 `/var/www/html`。
进行以下操作来让 Nginx 能处理 PHP。
`sudo nano /etc/nginx/sites-available/default`

将其中的如下内容

```
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
```

替换为

```
location / {
index  index.html index.htm index.php default.html default.htm default.php;
}
 
location ~\.php$ {
fastcgi_pass unix:/run/php/php7.3-fpm.sock;
#fastcgi_pass 127.0.0.1:9000;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
include fastcgi_params;
}
```

Ctrl + O 保存再 Ctrl + X 退出。

```
sudo service nginx restart
```

最后重启 Nginx 即可，以上步骤在玩客云 Ubuntu 20.04 focal 5.9.0-rc7-aml-s812 系统版本上测试通过。

##### 2.部署 Onecloud-Dashboard

这里介绍两种方法将 Onecloud-Dashboard 部署在 Nginx 上。
**2.1. SFTP 上传**
在 GitHub 下载本项目源码。通过 FileZilla 等 FTP 软件将解压出来的目录上传到玩客云的 `/var/www/html` 目录下。
那么可以通过 `http://玩客云IP/onecloud-dashboard` 访问部署好了的 Onecloud-Dashboard。

如果页面无法显示，可以尝试在玩客云终端给源码添加运行权限，例如你上传之后的路径是 `/var/www/html/onecloud-dashboard`，则运行。

```
cd /var/www/html
sudo chown -R www-data onecloud-dashboard
```

**2.2. GitHub 部署**
如果你了解过 GitHub 的基本操作，通过 GitHub 来下载本项目到 玩客云上会相当方便。

```
#如果已安装过 git 客户端可以跳过下一行
sudo apt-get install git
cd /var/www/html
sudo git clone https://github.com/muzihuaner/onecloud-dashboard.git
```

即可通过 `http://玩客云IP/pi-dashboard` 访问部署好了的 Onecloud-Dashboard。

同样如果页面无法显示，可以尝试在树莓派终端给源码添加运行权限，例如你上传之后的路径是 `/var/www/html/onecloud-dashboard`，则运行。

```
cd /var/www/html
sudo chown -R www-data onecloud-dashboard
```

本项目基于https://github.com/nxez/pi-dashboard