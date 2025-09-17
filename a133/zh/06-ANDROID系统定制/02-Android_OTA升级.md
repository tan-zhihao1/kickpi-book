# 02-Android_OTA升级



## 补丁信息

确保 build.sh 下有此修改

```diff
diff --git a/build.sh b/build.sh
index 95b62fe30b..13d54e0763 100755
--- a/build.sh
+++ b/build.sh
     echo "Start pack image"
     cd android
     pack
+    rm out/target/product/ceres-c3/ceres_c3-full_ota*.zip
+    pack4dist
+    mv out/target/product/ceres-c3/ceres_c3-full_ota-eng.*.zip out/target/product/ceres-c3/ceres_c3-full_ota-eng_$(date +%Y%m%d)_$(date +%H%M).zip
     cd -
 }
```

> pack4dist 执行后生成 OTA 包

编译后 OTA 整包最终生成路径：`android/out/target/product/ceres-c3/ceres_c3-full_ota-eng_*.zip`



## 文档参考

```
Android_10_OTA_开发指南.pdf
```



## 升级前提

1. 如果有电池，接入电池升级时保证电量充足。



2. 如果无电池，dts 需要关闭电池供电。

```diff
                                battery_power_supply: battery-power-supply {
                                        compatible = "x-powers,axp803-battery-power-supply";
-                                       status = "okay";
+                                       status = "disabled";
 
                                        pmu_chg_ic_temp = <0>;
                                        pmu_battery_rdc= <93>;
```

> 默认软件会打开此选项，用于兼容有电池和无电池两种开机场景。
>
> 如果不接电池，必须关闭此选项才能支持 OTA 升级。
>
> 否则 OTA 升级检测到电池电量过低，导致升级失败。



3. 保证 OTA 包的镜像日期高于主板原本的镜像。

```diff
--- a/build.sh
+++ b/build.sh
@@ -100,7 +100,7 @@ function build() {
         echo "Start build Android"
         rm -vf longan/out/*.img
         cd android
-        # make installclean
+        make installclean
         make BUILD_NUMBER=ido-a133 -j64
         if [ $? -eq 0 ]; then
             echo "Build android ok!"
```

> 编译保证 make installclean 打开，编译时 prop 信息正常更新。



## 升级流程

第一步，首先确保 OTA 包和主板信息满足升级前提



第二步，将 OTA 包存放到 /sdcard/ 路径下

> adb push ceres_c3-full_ota-eng_*.zip /sdcard/
>
> U盘拷贝到 /sdcard/ 路径下



第三步，系统打开 Setting -> System -> Advanced -> Local Update 

> 命令行打开方式 
>
> ```
> adb shell am start -n com.softwinner.update/com.softwinner.update.MainActivity
> ```

![image-20250916171430949](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250916171430949.png)



第四步，选择 OTA System Update 

![image-20250916171506303](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250916171506303.png)

第五步，选中对应的 OTA 包，等待认证

![image-20250916171547458](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250916171547458.png)

第六步，点击 Start install

![image-20250916171617182](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250916171617182.png)

6. 点击 ok，等待升级即可

![image-20250916171709862](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250916171709862.png)

7. 升级完成后，可通过验证功能或系统编译属性确认变更。

```
$ getprop ro.build.date
 [Tue Sep 16 16:44:38 CST 2025]
```



## 常见问题

### 升级失败问题

1. 确认是否符合升级前提
2. 查看对应的升级日志 /cache/recovery，主要查看 last_log ，里面包含主要的升级信息。

```
$ ls /cache/recovery/                                                
block.map    last_kmsg.1 last_kmsg.4 last_log   last_log.3 uncrypt_file 
last_install last_kmsg.2 last_kmsg.5 last_log.1 last_log.4 
last_kmsg    last_kmsg.3 last_locale last_log.2 last_log.5 
```



### 无法生成OTA包问题

查看补丁信息是否添加到源码中。



### 差分包如何生成

请参考文档

```
Android_10_OTA_开发指南.pdf
```

