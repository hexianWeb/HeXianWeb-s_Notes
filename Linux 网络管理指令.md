# Linux 应该知道

> 作者 何贤

## 初始化服务器

1. 以root身份登录

2. 创建新的用户

   **以root**身份登录后，您将能够添加新用户帐户。将来，我们将使用这个新帐户而不是**root**登录。

   ```bash
   adduser hexian
   ```

   

3. 授予管理权限

   现在我们有了一个具有常规帐户权限的新用户帐户。但是，我们有时可能需要执行管理任务。

   **为了避免退出我们的普通用户并以root**帐户重新登录，我们可以为我们的普通帐户设置所谓的*超级用户*或**root**权限。这将允许我们的普通用户通过将单词放在命令前来以管理权限运行命令`sudo`。

   要将这些权限添加到我们的新用户，我们需要将用户添加到**sudo**组。默认情况下，在 Ubuntu 20.04 上，属于**sudo**组成员的用户可以使用该`sudo`命令。

   以**root**身份运行此命令将您的新用户添加到**sudo**组（用您的新用户替换突出显示的用户名）

   ```
   usermod -aG usdo hexian	
   ```

   

4. <a href="#append">设置基本防火墙</a>

   Ubuntu 20.04 服务器可以使用 UFW 防火墙来确保只允许连接到某些服务。我们可以使用这个应用程序设置一个基本的防火墙。

   应用程序可以在安装时向 UFW 注册其配置文件。这些配置文件允许 UFW 按名称管理这些应用程序。OpenSSH，允许我们现在连接到我们的服务器的服务，有一个在 UFW 注册的配置文件。

   您可以通过键入以下内容来查看：

   ```bash
   ufw app list
   ```

   ```
   Output
   Available applications:
     OpenSSH
   ```

   我们需要确保防火墙允许 SSH 连接，以便我们下次可以重新登录。我们可以通过键入以下内容来允许这些连接：

   ```bash
   ufw allow OpenSSH
   ```

   之后，我们可以通过键入以下命令启用防火墙：

   ```bash
   ufw enable
   ```

   键入`y`并按下`ENTER`以继续。您可以通过键入以下内容看到仍然允许 SSH 连接：

   ```bash
   ufw status
   ```

   ```
   OutputStatus: active
   
   To                         Action      From
   --                         ------      ----
   OpenSSH                    ALLOW       Anywhere
   OpenSSH (v6)               ALLOW       Anywhere (v6)
   ```

   由于**防火墙当前阻止除 SSH 之外的所有连接**，如果您安装和配置其他服务，则需要调整防火墙设置以允许流量进入。您可以在我们的[*UFW Essentials*指南](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)中了解一些常见的 UFW 操作。

5. 





## Linux 网络管理指令

> 作者：何贤

### 端口监听命令（常用）

**netstat**

| options | 参数说明                                          |
| ------- | ------------------------------------------------- |
| -a      | 显示所有连接的Socket                              |
| -p      | programs 显示正在使用Socket的程序识别码和程序名称 |
| -n      | numeric 直接使用IP地址，而不通过域名服务器        |
| -t      | TCP传输协议                                       |
| -u      | UDP传输协议                                       |
| -c      | 持续列出网络状态                                  |
| -l      | 显示监听的套接口                                  |
| -h      | help                                              |

****

**ifconfig**

```
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:4b:9e:02:17  txqueuelen 0  (以太网)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp8s0f1: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 80:fa:5b:71:38:b0  txqueuelen 1000  (以太网)
        RX packets 888730  bytes 1089550358 (1.0 GB)
        RX errors 0  dropped 2915  overruns 0  frame 0
        TX packets 544511  bytes 62180151 (62.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (本地环回)
        RX packets 725600  bytes 754414804 (754.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 725600  bytes 754414804 (754.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp0s20f3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.4.106  netmask 255.255.254.0  broadcast 192.168.5.255
        inet6 fe80::32c9:fe51:e0e6:46aa  prefixlen 64  scopeid 0x20<link>
        ether dc:71:96:b4:f7:7b  txqueuelen 1000  (以太网)
        RX packets 221420  bytes 257544857 (257.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 94274  bytes 13974693 (13.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```



### 连接监测命令（常用）



| command | 功能                     |
| ------- | ------------------------ |
| ping    | ip地址连接测试           |
| telnet  | ip地址的某个端口连接测试 |



#### **ping命令**

```
ping www.4399.com
PING www.4399.com.lxdns.com (59.54.253.75) 56(84) bytes of data.
64 比特，来自 75.253.54.59.broad.fz.jx.dynamic.163data.com.cn (59.54.253.75): icmp_seq=1 ttl=58 时间=6.05 毫秒
64 比特，来自 75.253.54.59.broad.fz.jx.dynamic.163data.com.cn (59.54.253.75): icmp_seq=2 ttl=58 时间=8.51 毫秒
64 比特，来自 75.253.54.59.broad.fz.jx.dynamic.163data.com.cn (59.54.253.75): icmp_seq=3 ttl=58 时间=6.61 毫秒

```



#### **telnet命令**

```
telnet www.4399.com 80
Trying 182.106.174.18...
Connected to www.4399.com.lxdns.com.
Escape character is '^]'.
^CConnection closed by foreign host.

```



#### **curl命令**

> curl是一个利用URL规则在命令行下工作的传输工具

```
curl[optoins][url]
```



#### 常用用法

> curl [	目标网站	]

```
curl http://www.baidu.com
//结果
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>

```


2、保存访问的网页

2.1:使用linux的重定向功能保存

```
# curl http://www.linux.com >> linux.html
```

2.2:可以使用curl的内置option:-o(小写)保存网页

```
$ curl -o linux.html http://www.linux.com
```

执行完成后会显示如下界面，显示100%则表示保存成功

```
% Total    % Received % Xferd  Average Speed  Time    Time    Time  Current
                                Dload  Upload  Total  Spent    Left  Speed
100 79684    0 79684    0    0  3437k      0 --:--:-- --:--:-- --:--:-- 7781k
```

2.3:可以使用curl的内置option:-O(大写)保存网页中的文件
要注意这里后面的url要具体到某个文件，不然抓不下来

```
# curl -O http://www.linux.com/hello.sh
```

3、测试网页返回值

```
# curl -o /dev/null -s -w %{http_code} www.linux.com
```

Ps:在脚本中，这是很常见的测试网站是否正常的用法

4、指定proxy服务器以及其端口
很多时候上网需要用到代理服务器(比如是使用代理服务器上网或者因为使用curl别人网站而被别人屏蔽IP地址的时候)，幸运的是curl通过使用内置option：-x来支持设置代理

```
# curl -x 192.168.100.100:1080 http://www.linux.com
```

5、cookie
有些网站是使用cookie来记录session信息。对于chrome这样的浏览器，可以轻易处理cookie信息，但在curl中只要增加相关参数也是可以很容易的处理cookie
5.1:保存http的response里面的cookie信息。内置option:-c（小写）

```
# curl -c cookiec.txt  http://www.linux.com
```

执行后cookie信息就被存到了cookiec.txt里面了

5.2:保存http的response里面的header信息。内置option: -D

```
# curl -D cookied.txt http://www.linux.com
```

执行后cookie信息就被存到了cookied.txt里面了

注意：-c(小写)产生的cookie和-D里面的cookie是不一样的。


5.3:使用cookie
很多网站都是通过监视你的cookie信息来判断你是否按规矩访问他们的网站的，因此我们需要使用保存的cookie信息。内置option: -b

```
# curl -b cookiec.txt http://www.linux.com
```

6、模仿浏览器
有些网站需要使用特定的浏览器去访问他们，有些还需要使用某些特定的版本。curl内置option:-A可以让我们指定浏览器去访问网站

```
# curl -A "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.0)" http://www.linux.com
```

这样服务器端就会认为是使用IE8.0去访问的

7、伪造referer（盗链）
很多服务器会检查http访问的referer从而来控制访问。比如：你是先访问首页，然后再访问首页中的邮箱页面，这里访问邮箱的referer地址就是访问首页成功后的页面地址，如果服务器发现对邮箱页面访问的referer地址不是首页的地址，就断定那是个盗连了
curl中内置option：-e可以让我们设定referer

```
# curl -e "www.linux.com" http://mail.linux.com
```

这样就会让服务器其以为你是从www.linux.com点击某个链接过来的

8、下载文件
8.1：利用curl下载文件。
\#使用内置option：-o(小写)

```
# curl -o dodo1.jpg http:www.linux.com/dodo1.JPG
```

\#使用内置option：-O（大写)

```
# curl -O http://www.linux.com/dodo1.JPG
```

这样就会以服务器上的名称保存文件到本地

8.2：循环下载
有时候下载图片可以能是前面的部分名称是一样的，就最后的尾椎名不一样

```
# curl -O http://www.linux.com/dodo[1-5].JPG
```

这样就会把dodo1，dodo2，dodo3，dodo4，dodo5全部保存下来

8.3：下载重命名

```
# curl -O http://www.linux.com/{hello,bb}/dodo[1-5].JPG
```

由于下载的hello与bb中的文件名都是dodo1，dodo2，dodo3，dodo4，dodo5。因此第二次下载的会把第一次下载的覆盖，这样就需要对文件进行重命名。

```
# curl -o #1_#2.JPG http://www.linux.com/{hello,bb}/dodo[1-5].JPG
```

这样在hello/dodo1.JPG的文件下载下来就会变成hello_dodo1.JPG,其他文件依此类推，从而有效的避免了文件被覆盖

8.4：分块下载
有时候下载的东西会比较大，这个时候我们可以分段下载。使用内置option：-r

```
# curl -r 0-100 -o dodo1_part1.JPG http://www.linux.com/dodo1.JPG
# curl -r 100-200 -o dodo1_part2.JPG http://www.linux.com/dodo1.JPG
# curl -r 200- -o dodo1_part3.JPG http://www.linux.com/dodo1.JPG
# cat dodo1_part* > dodo1.JPG
```

这样就可以查看dodo1.JPG的内容了

8.5：通过ftp下载文件
curl可以通过ftp下载文件，curl提供两种从ftp中下载的语法

```
# curl -O -u 用户名:密码 ftp://www.linux.com/dodo1.JPG
# curl -O ftp://用户名:密码@www.linux.com/dodo1.JPG
```

8.6：显示下载进度条

```
# curl -# -O http://www.linux.com/dodo1.JPG
```

8.7：不会显示下载进度信息

```
# curl -s -O http://www.linux.com/dodo1.JPG
```

9、断点续传
在windows中，我们可以使用迅雷这样的软件进行断点续传。curl可以通过内置option:-C同样可以达到相同的效果
如果在下载dodo1.JPG的过程中突然掉线了，可以使用以下的方式续传

```
# curl -C -O http://www.linux.com/dodo1.JPG
```

10、上传文件
curl不仅仅可以下载文件，还可以上传文件。通过内置option:-T来实现

```
# curl -T dodo1.JPG -u 用户名:密码 ftp://www.linux.com/img/
```

这样就向ftp服务器上传了文件dodo1.JPG

11、显示抓取错误

```
# curl -f http://www.linux.com/error
```



