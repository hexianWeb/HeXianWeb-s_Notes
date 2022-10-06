# Nginx教程

> 作者：何贤Web

## 安装pm2

### pm2 单个启动常用指令

```bash
pm2 start app.js --watch  # 启动并监听项目文件变化
pm2 start app.js -i 4 # 启动4个应用实例，自动负载均衡
pm2 start app.js  # 启动
```

### pm2 多个启动

```json
{
    "apps": [{
        "name": "appA",
        "script": "./appA.js",
        "watch": false
    }, {
        "name": "appB",
        "script": "./appB.js",
        "watch": false
    }]
}
----------------------------------

# 再执行：
$ pm2 start server.json
```

### pm2重启

```bash
$ pm2 restart app_name|app_id  # 重启
$ pm2 restart all  # 重启所有进程，相当stop+start
$ pm2 reload all  # 0秒停机重载进程 (用于不间断进程)
```

### pm2查看

```bash
$ pm2 list # 查看进程
$ pm2 logs # 查看日志
$ pm2 show app_name|app_id # 查看进程详情
$ pm2 monit  # 查看CPU和内存资源占用
```

### pm2停止

```bash
$ pm2 stop app_name|app_id
$ pm2 stop all  # 停止所有
```

### pm2删除

```bash
$ pm2 delete app_name|app_id  # 从列表中删除指定的进程
$ pm2 delete all # 从列表中删除全部进程
$ pm2 kill # 杀死守护进程
```

### pm2开机自启动

```bash
$ pm2 startup  # 创建开机自启动命令
$ pm2 save  # 保存当前应用列表
$ pm2 resurrect  # 重新加载保存的应用列表
$ pm2 unstartup  # 移除开机自启动
```

### pm2更新

```bash
$ pm2 save # 保存当前应用列表
$ npm install pm2 -g
$ pm2 update
```

pm2

## 安装Nginx 

### 第0步 为什么安装Nginx

> 版权声明：本文为CSDN博主「小满zs」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/qq1195566313/article/details/123958967

Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。2011年6月1日，nginx 1.0.4发布。

高并发，大流量：需要面对高并发用户，大流量访问。举个例子，去往迪拜的飞机有200张票，但是有100w人都挤进系统买票，如何让这100w人能够看到票务的实时更新，以及顺利的买到一张票，都是一个网站架构师应该考虑的问题。这也许对于淘宝的“双十一”1000w的一分钟独立访问用户量来说，是个微不足道的数字，但是对于用户的体验以及网站的口碑来说，都是一项不小的挑战

Nginx 是一个安装非常的简单、配置文件非常简洁（还能够支持perl语法）、Bug非常少的服务。Nginx 启动特别容易，并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够不间断服务的情况下进行软件版本的升级。

Nginx代码完全用C语言从头写成。官方数据测试表明能够支持高达 50,000 个并发连接数的响应。

#### 1.正向代理 反向代理

nginx不仅可以做反向代理，还能用作正向代理来进行上网等功能。如果把局域网外的Internet想象成一个巨大的资源库，则局域网中的客户端要访问Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理（也就是大家常说的，通过正向代理进行上网功能）


 反向代理实际运行方式是代理服务器接受网络上的连接请求。它将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给网络上请求连接的客户端，此时代理服务器对外就表现为一个服务器。



#### 2.负载均衡（upstream）

轮询

轮询方式是Nginx负载默认的方式，顾名思义，所有请求都按照时间顺序分配到不同的服务上，如果服务Down掉，可以自动剔除

权重

指定每个服务的权重比例，weight和访问比率成正比，通常用于后端服务机器性能不统一，将性能好的分配权重高来发挥服务器最大性能

#### 3.动静分离

在Web开发中，通常来说，动态资源其实就是指那些后台资源，而静态资源就是指HTML，JavaScript，CSS，img等文件。

一般来说，都需要将动态资源和静态资源分开，将静态资源部署在Nginx上，当一个请求来的时候，如果是静态资源的请求，就直接到nginx配置的静态资源目录下面获取资源，如果是动态资源的请求，nginx利用反向代理的原理，把请求转发给后台应用去处理，从而实现动静分离。

在使用前后端分离之后，可以很大程度的提升静态资源的访问速度，同时在开过程中也可以让前后端开发并行可以有效的提高开发时间，也可以有些的减少联调时间 。


### 第1步 安装Nginx

因为 Nginx 在 Ubuntu 的默认存储库中可用，所以可以使用`apt`打包系统从这些存储库安装它。

由于这是我们在本次会议中与包装系统的第一次交互，因此`apt`我们将更新本地包装索引，以便我们可以访问最新的包装清单。之后，我们可以安装`nginx`：

```bash
sudo apt update
sudo apt install nginx
```

接受该过程后，`apt`将安装 Nginx 和任何所需的依赖项到您的服务器。

### 第 2 步 – 调整防火墙

在测试 Nginx 之前，需要调整防火墙软件以允许访问该服务。Nginx 在安装时将自己注册为服务`ufw`，从而可以直接允许 Nginx 访问。

通过键入以下内容列出`ufw`知道如何使用的应用程序配置：

```bash
sudo ufw app list
```

您应该获得应用程序配置文件的列表：

```
OutputAvailable applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```

如输出所示，Nginx 有三个可用的配置文件：

- **Nginx Full**：此配置文件同时打开端口 80（正常、未加密的 Web 流量）和端口 443（TLS/SSL 加密流量）
- **Nginx HTTP**：此配置文件仅打开端口 80（正常、未加密的网络流量）
- **Nginx HTTPS**：此配置文件仅打开端口 443（TLS/SSL 加密流量）

建议您启用最严格的配置文件，该配置文件仍将允许您配置的流量。现在，我们只需要允许端口 80 上的流量。

您可以通过键入以下内容启用此功能：

```bash
sudo ufw allow 'Nginx HTTP'
```



您可以通过键入以下内容来验证更改：

```bash
sudo ufw status
```



输出将指示允许哪些 HTTP 流量：

```
OutputStatus: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

### 第 3 步 – 检查您的 Web 服务器

在安装过程结束时，Ubuntu 20.04 启动 Nginx。Web 服务器应该已经启动并运行。

我们可以`systemd`通过输入以下命令检查 init 系统以确保服务正在运行：

```bash
systemctl status nginx
```



```
Output● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2020-04-20 16:08:19 UTC; 3 days ago
     Docs: man:nginx(8)
 Main PID: 2369 (nginx)
    Tasks: 2 (limit: 1153)
   Memory: 3.5M
   CGroup: /system.slice/nginx.service
           ├─2369 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
           └─2380 nginx: worker process
```

正如所证实的那样，服务已成功启动。然而，最好的测试方法是从 Nginx 实际请求一个页面。

您可以访问默认的 Nginx 登录页面，通过导航到您的服务器的 IP 地址来确认该软件是否正常运行。如果您不知道服务器的 IP 地址，您可以使用[icanhazip.com](http://icanhazip.com/)工具找到它，该工具会为您提供从 Internet 上的其他位置收到的公共 IP 地址：

```bash
curl -4 icanhazip.com
```



获得服务器的 IP 地址后，将其输入浏览器的地址栏中：

```
http://your_server_ip
```

您应该会收到默认的 Nginx 登录页面：

![Nginx 默认页面](https://assets.digitalocean.com/articles/nginx_1604/default_page.png)

如果您在此页面上，则表明您的服务器运行正常并且可以进行管理。

### 第 4 步 - 管理 Nginx 进程

现在您已经启动并运行了 Web 服务器，让我们回顾一些基本的管理命令。

要停止您的 Web 服务器，请键入：

```bash
sudo systemctl stop nginx
```

要在 Web 服务器停止时启动它，请键入：

```bash
sudo systemctl start nginx
```

要停止然后再次启动服务，请键入：

```bash
sudo systemctl restart nginx
```

如果您只是进行配置更改，Nginx 通常可以重新加载而不会断开连接。为此，请键入：

```bash
sudo systemctl reload nginx
```

默认情况下，Nginx 配置为在服务器启动时自动启动。如果这不是您想要的，您可以通过键入以下内容来禁用此行为：

```bash
sudo systemctl disable nginx
```

要重新启用服务以在启动时启动，您可以键入：

```bash
sudo systemctl enable nginx
```

您现在已经学习了基本的管理命令，并且应该准备好将站点配置为托管多个域。



### 第 6 步 - 熟悉重要的 Nginx 文件和目录

现在您已经知道如何管理 Nginx 服务本身，您应该花几分钟来熟悉一些重要的目录和文件。

### 内容

- `/var/www/html`：实际的 Web 内容，默认情况下仅包含您之前看到的默认 Nginx 页面，是从`/var/www/html`目录中提供的。这可以通过更改 Nginx 配置文件来更改。

### 服务器配置

- `/etc/nginx`: Nginx 配置目录。所有 Nginx 配置文件都驻留在此处。
- `/etc/nginx/nginx.conf`: **主要的 Nginx 配置文件**。可以对其进行修改以更改 Nginx 全局配置。

- `/etc/nginx/sites-available/`：可以存储每个站点服务器块的目录。Nginx 不会使用在该目录中找到的配置文件，除非它们链接到该`sites-enabled`目录。通常，所有服务器块配置都在此目录中完成，然后通过链接到另一个目录来启用。
- `/etc/nginx/sites-enabled/`：存储启用的每站点服务器块的目录。通常，这些是通过链接到`sites-available`目录中的配置文件来创建的。
- `/etc/nginx/snippets`：此目录包含可以包含在 Nginx 配置中的其他位置的配置片段。潜在可重复的配置段是重构为片段的良好候选者。

# Nginx.conf配置文件

## 1.全局块

全局块是默认配置文件从开始到events块之间的一部分内容，主要设置一些影响Nginx服务器整体运行的配置指令，因此，这些指令的作用域是Nginx服务器全局。

```
user [user] [group]  指定可以运行nginx服务的用户和用户组，只能在全局块配置 user指令在Windows上不生效，如果你制定具体用户和用户组会报警告

worker_processes nginx进程数量worker_processes 比如设置为2 nginx将会开启一个master进程和2两个worker进程

pid  logs/nginx.pid 存放pid文件

error_log  logs/error.log;  全局错误日志类型 debug info warn error 存放地址
```

## 2.events块

events块涉及的指令主要影响Nginx服务器与用户的网络连接。常用到的设置包括是否开启对多worker process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型处理连接请求，每个worker process可以同时支持的最大连接数等

```
accept_mutex 默认开启-开启之后nginx 的多个worker将会以串行的方式来处理，只会有一个worker将会被唤起，其他的worker继续睡眠，如果不开启将会造成惊群效应多个worker全部唤起不过只有一个Worker能获取新连接，其它的Worker会重新进入休眠状态

worker_connections 单个进程最大连接数（最大连接数=连接数+进程数）
```



## 3.http块(重要)

http块是Nginx服务器配置中的重要部分，代理、缓存和日志定义等绝大多数的功能和第三方模块的配置都可以放在这个模块中。

```bash
include指令，用于引入其他的配置文件

default_type 如果Web程序没设置，Nginx也没对应文件的扩展名，就用Nginx 里默认的default_type定义的处理方式。default_type application/octet-stream; #nginx默认文件类型

log_format指令，用于定义日志格式，此指令只能在http块中进行配置

sendfile 简单来说就是启用sendfile()系统调用来替换read()和write()调用，减少系统上下文切换从而提高性能，当 nginx 是静态文件服务器时，能极大提高nginx的性能表现(下图是没开启与开启的对比)

keepalive_timeout HTTP 有一个 KeepAlive 模式，它告诉 webserver 在处理完一个请求后保持这个 TCP 连接的打开状态。若接收到来自客户端的其它请求，服务端会利用这个未被关闭的连接，而不需要再建立一个连接。

gzip 开启Gzip压缩功能， 可以使网站的css、js 、xml、html 文件在传输时进行压缩，提高访问速度, 进而优化Nginx性能
```



<center class="half">
    <img src="https://cdn.nlark.com/yuque/0/2022/png/2755207/1650453674846-0994f4ff-ad07-402a-a3d5-1bbeee7b8a67.png" alt="未开启sendfile" style="zoom: 33%;" align=left/>
    <img src="https://cdn.nlark.com/yuque/0/2022/png/2755207/1650453701846-d6d97bca-c0e1-4e92-930f-c653814ef9bf.png" alt="开启sendfile" style="zoom: 33%;" />
</center>
## 4server块

**每一个http块都可以包含多个server块，而每个server块就相当于一台虚拟主机，它内部可有多台主机联合提供服务，一起对外提供在逻辑上关系密切的一组服务**

listen指令的配置非常灵活，可以单独制定ip，单独指定端口或者同时指定ip和端口

```
listen 127.0.0.1:8000;  #只监听来自127.0.0.1这个IP，请求8000端口的请求
listen 127.0.0.1; #只监听来自127.0.0.1这个IP，请求80端口的请求（不指定端口，默认80）
listen 9999; #监听来自所有IP，请求9999端口的请求
listen *:9999; #和上面效果一样
listen localhost:8000; #和第一种效果一致
```

server_name nginx 允许一个虚拟主机有一个或多个名字，也可以使用通配符"*"来设置虚拟主机的名字  支持 ip 域名 通配符 正则等

```
 server_name  localhost;
```

## 5location块

**每个server块中可包含多个location块。在整个Nginx配置文档中起着重要的作用，而且Nginx服务器在许多功能上的灵活性往往在location指令的配置中体现出来**

location 指令可以分为以下 3 类：

- 前缀字符串匹配

- 正则表达式匹配

- 用于内部跳转的命名location

#### 前缀字符串匹配

  精确匹配 =
  前缀匹配 ^~（立刻停止后续的正则搜索）
  按文件中顺序的正则匹配 ~或~*
  匹配不带任何修饰的前缀匹配。

####   location root 

  root 指定目录的上级目录，并且该上级目录要含有locatoin指定名称的同名目录。

```
location /img/ {
	root /var/www/image;
}
```

若按照这种配置的话，则访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件



### 服务器日志

- `/var/log/nginx/access.log`：对您的 Web 服务器的每个请求都记录在此日志文件中，除非 Nginx 配置为不这样做。
- `/var/log/nginx/error.log`: 任何 Nginx 错误都会记录在这个日志中。

## 结论

# Nginx反向代理