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
+    rm out/target/product/ceres-c3/ceres_c3-full_ota-eng.*.zip
+    pack4dist
+    mv out/target/product/ceres-c3/ceres_c3-full_ota-eng.*.zip out/target/product/ceres-c3/ceres_c3-full_ota-eng_$(date +%Y%m%d)_$(date +%H%M).zip
     cd -
 }
```

> pack4dist 执行后生成 OTA 包

编译后 OTA 整包最终生成路径：`android/out/target/product/ceres-c3/ceres_c3-full_ota-eng_$(date +%Y%m%d)_$(date +%H%M).zip`



## 文档参考

```
Android_10_OTA_开发指南.pdf
```



## 升级前提

1. 确认电池是否接入，若接入电池需要保证电量充足。
2. 如果无电池，dts 需要关闭电池供电。

```diff
                                battery_power_supply: battery-power-supply {
                                        compatible = "x-powers,axp803-battery-power-supply";
-                                       status = "okay";
+                                       status = "disabled";
 
                                        pmu_chg_ic_temp = <0>;
                                        pmu_battery_rdc= <93>;
```

> 默认软件会打开此选项，软件兼容有电池和无电池两种开机场景。
>
> 如果不接电池，必须关闭此选项才能支持 OTA 升级。否则 OTA 检测到电池电量过低，导致升级失败。

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

> 编译保证 make installclean 打开，对应的 prop 信息正常更新。



