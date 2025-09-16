# 02-Android_OTA升级



## 补丁信息

确保 build.sh 下由此修改

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

> pack4dist 生成 OTA 包
>
> 最终生成路径：
>
> android/out/target/product/ceres-c3/ceres_c3-full_ota-eng_$(date +%Y%m%d)_$(date +%H%M).zip



