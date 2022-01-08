# amp命令说明

## 命令总览
* 命令行登陆：`amp login <your-appKey> <your-appSecret>`
* 列出绑定的设备：`amp device list`
* 设置默认操作设备：`amp device active <device-name>`
* 推送应用代码到设备端：`amp put <app-dir> [device-name]`
* 列出系统的可用串口：`amp seriallist`
* 串口推送应用代码到设备（本地更新）：`amp serialput <app-dir> <port>`
* 版本号：`amp --version`
* 帮助：`amp --version`

<a name="tchCV"></a>
## 命令详解

<a name="74Hjs"></a>
### login - 登录

**命令参数：**
`amp login <your-appKey> <your-appSecret>`

**说明：**

该命令用于绑定用户appKey和appSecret，初次使用aos-app命令行工具的时候，需要绑定appKey和appSecret，之后使用其他命令的时候不再需要输入appKey和appSecret。

> appKey和appSecret和token获取方式请登陆`https://haas.iot.aliyun.com/quick-app/device-manage`。


**命令示例：**
```
amp login 32233333 33334babab589bb255a447333333333
```

### device list - 列出绑定的设备

**命令参数：**
`amp device list`

**说明：**

列出您的账号绑定的设备。

**命令示例：**
```
amp device list
```

### put - 推送本地应用代码到设备

**命令参数：**
`amp put <app-dir> `
**或**
`amp put <app-dir> <device-name>`

**说明：**

推送本地应用代码到设备。如果不指定device-name，会提示选择设备。

**命令示例：**
```
amp put ./app esp32_01
```

### seriallist - 列出系统的可用串口

**命令参数：**
`amp seriallist`

**说明：**

列出系统的可用串口

**命令示例：**
```
amp seriallist
```

### serialput - 串口推送应用代码到设备（本地更新）

**命令参数：**
`amp serialput <app-dir> <serial-port>`
**或**
`amp serialput <app-dir> <-serialport> -t <type>`

**说明：**

串口推送应用代码到设备（本地更新）。
`type`有：
• ymodem（默认）

**命令示例：**
```
amp serialput ./app /dev/tty.usb*
```

### version - 查看版本号

```
amp --version
```

### help - 帮助

```
amp --help
```