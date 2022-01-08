# Haas Python 开发

## helloworld工程

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
'''
@File       :    main.py
@Author     :    ethan.lcz
@version    :    1.0
@Description:    helloworld案例 - 周期性打印"helloworld"到console中
                 board.json - 硬件资源配置文件，详情请参考：https://haas.iot.aliyun.com/haasapi/index.html#/Python/docs/zh-CN/haas_extended_api/driver/driver
'''


import utime   # 延时函数在utime库中

if __name__ == '__main__':
    # while True:             # 无限循环
    print("helloworld")  # 打印"helloworld"字串到console中
    # utime.sleep(1)      # 打印完之后休眠1秒

```



将脚本推送到机器上即可

