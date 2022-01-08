## JS

- 脚本烧录方式：将固件包解压，进入aos_burn_tool目录，打开cmd或者powershell终端，执行以下命令

  > ```
  > python flash_program.py --bin=ota_rtos.bin
  > ```
  >

### 开发与调试

- 编译固件

  

问题一 ：在linux下开发 发现1.0.66版本自动拉取的amp路径错误 并且 缺少quickjs