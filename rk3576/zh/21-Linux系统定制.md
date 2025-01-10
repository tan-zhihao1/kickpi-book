# 21-Linux系统定制

* Debian12

* Ubuntu



## SSH 登录

```
ssh <用户名>@<IP>
```



## ADB 功能

```
adb shell
```

上传文件 1.txt 到主板 /data/ 下

```
adb push ./1.txt /data/
```

下载文件 2.txt 到PC本地当前目录

```
adb pull /data/2.txt .
```



## 屏幕显示方向配置

* Debian 支持 xrandr 进行配置屏幕

```
# 获取使用帮助
xrandr -help
```



* 查看屏幕信息

```
root@linaro-alip:/# xrandr
Screen 0: minimum 320 x 200, current 1920 x 1080, maximum 16384 x 16384
HDMI-1 connected 1920x1080+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
   1920x1080     60.00*+  60.00    50.00    50.00    59.94
   1920x1080i    60.00    50.00    50.00    59.94
   1680x1050     59.88
   1280x1024     75.02    60.02
   1440x900      59.90
   1280x960      60.00
   1360x768      60.02
   1152x864      75.00
   1280x720      60.00    50.00    50.00    59.94
   1024x768      75.03    70.07    60.00
   832x624       74.55
   800x600       72.19    75.00    60.32    56.25
   720x576       50.00    50.00    50.00
   720x480       60.00    60.00    59.94    59.94
   640x480       75.00    72.81    66.67    60.00    59.94    59.94
   720x400       70.08
DSI-1 connected (normal left inverted right x axis y axis)
   800x1280      60.51 +
DP-1 disconnected (normal left inverted right x axis y axis)
```



* 旋转控制

>根据xrandr，已知目前显示设备 HDMI-1

对 HDMI-1 进行旋转设置
```
$ xrandr --output HDMI-1 --rotate normal			// 画面正常显示
$ xrandr --output HDMI-1 --rotate right				// 向右旋转，顺时针旋转90度
$ xrandr --output HDMI-1 --rotate left				// 向左旋转，顺时针旋转270度
$ xrandr --output HDMI-1 --rotate inverted			// 反转，顺时针旋转180度
```



* 触摸校准

```shell
//第一歩 安装工具
$ apt update
$ apt install xinput
$ apt install xinput-calibrator

//第二步 查看 Device 和 ID，可知 goodix-ts 设备id 为 12
$ xinput_calibrator --list 
Device "goodix-ts" id=11

//第三歩 重置转置矩阵和校准矩阵，这里非常关键，转置矩阵需要根据屏幕旋转，校准矩阵设置默认值即可
$ xinput set-prop $id --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1

$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
// xinput set-prop 11 --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0

//修改触摸方向（可根据名字修改） 默认方向
$ xinput set-prop $id 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
 
//修改触摸方向（可根据名字修改） 向左90度
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1

//修改触摸方向（可根据名字修改） 向右90度
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
 
//修改触摸方向（可根据名字修改） 旋转180度
$ xinput set-prop $id 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1

//第四步 开始校准
$ xinput_calibrator -v --device $id
// xinput_calibrator -v --device 11

```









