# 01初级篇-快速入门文档



### 1. 入门介绍

#### 1.1 网盘资料

百度网盘地址：

OneDrive网盘地址：

* 网盘目录说明

```
├── 0-用前必读
│   └── 资料目录说明.md
├── 1-SDK源码
│   └── RK356X Linux
│       ├── linaro-rootfs-20230524.tar.gz
│       ├── rk356x-linux-20230524.tar.gz
│       └── ubuntu-rootfs-20230523.tar.gz
├── 2-系统镜像
│   └── RK3566 TX66
│       ├── Android11 TV
│       ├── Debian11
│       └── Ubuntu2004
├── 3-硬件资料
└── 4-开发工具
    ├── ADB工具
    ├── USB镜像烧录工具
    ├── USB驱动
    └── 串口调试工具
```

#### 1.2 板卡硬件

板卡照片（带功能标注）

#### 1.3 配件说明

**DC供电接口**

配件连接图片

**USB OTG(烧录USB口)**

配件连接图片

**调试串口**

硬件配件连接图片

### 2. 镜像烧录

开发板出厂默认烧录好镜像，若不需要更换系统的话，可直接跳过此步骤，启动系统

#### 2.1 安装镜像烧录工具

镜像烧录工具网盘目录：4-开发工具\USB镜像烧录工具\RKDevTool\_Release\_v2.96.zip

![image-20230526154741821](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154741821.png)

#### 2.2 安装USB驱动

USB驱动网盘目录：4-开发工具\USB驱动\DriverAssitant\_v5.12.zip

![image-20230526154517334](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154517334.png)

#### 2.3 获取镜像文件

镜像文件可通过以下两种方式获取：网盘下载、SDK编译

SDK编译详细步骤参考文档 \[02初级篇-sdk编译]

镜像文件网盘目录：

```
├── 2-系统镜像
    └── RK3566-TX66
        ├── Android11-TV
        ├── Debian11
        └── Ubuntu2004
```

#### 2.4 开发板进入烧录模式

操作步骤：

1. 板卡USB OTG 连接 PC USB接口
2. 长按 RECOVERY按键
3. 连接12V DC接口
4. 打开烧录工具，查看是否识别到LOADER设备

#### 2.5 烧录工具使用

操作步骤：

1. 烧录工具 成功识别板卡设备（若识别不到，返回上一步确认操作）
2. 进入完整镜像烧录页面
3. 选择镜像路径
4. 开始烧录镜像
5. 查看 烧录日志，烧录成功后自动重启进入系统

![image-20230519111357204](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230519111357204.png)

### 3. 系统启动

#### 3.1 图形化桌面登陆

连接HDMI或LCD

#### 3.2 调试串口登录

#### 3.4 Android ADB登录
