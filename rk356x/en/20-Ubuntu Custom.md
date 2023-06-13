# 20-Ubuntu Custom







## Language

To change the system language, open the command line terminal and execute the following commands.



### English Language

```
echo "export LC_ALL=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
```



### China Language

```
echo "export LC_ALL=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
```

