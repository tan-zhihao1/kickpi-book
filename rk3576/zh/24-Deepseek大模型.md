# 24-DeepSeek大模型

本文将讲解如何使用 RKLLM 将提炼后的模型DeepSeek-R1-Distill-Qwen-1.5B部署到Rockchip平台，利用 NPU 进行硬件加速推理。

芯片平台：RK3576/RK3588

系统版本：Debian12/Debian11



# 一、开发环境搭建

## RKLLM SDK 说明文档

```
doc
└──Rockchip_RKLLM_SDK_CN.pdf
```



## RKNPU驱动

```
rknpu-driver
└──rknpu_driver_0.9.6_20240322.tar.bz2
```



## RKLLM-Toolkit

```
rkllm-toolkit
├──examples
│ └── huggingface
│ └── test.py
├──packages
│ └── md5sum.txt 
│ └── rkllm_toolkit-1.0.0-cp38-cp38-linux_x86_64.whl
```





## 模型下载





## runtime下载

```
rkllm-runtime
├──example
│ └── src
│ └── main.cpp
│ └── build-android.sh
│ └── build-linux.sh
│ └── CMakeLists.txt
│ └── Readme.md
├──runtime
│ └── Android
│ └── librkllm_api
│ └──arm64-v8a
│ └── librkllmrt.so # RKLLM Runtime 库
│ └──include
│ └── rkllm.h # Runtime 头文件
│ └── Linux
│ └── librkllm_api
│ └──aarch64
│ └── librkllmrt.so
│ └──include
│ └── rkllm.h
```





# 二、部署运行

## 板端部署环境

```
$ ls /rkllm-DeepSeek/
	DeepSeek-R1-Distill-Qwen-1.5B.rkllm
	lib/librkllmrt.so
	llm_demo
```

> 测试包说明：
>
> `DeepSeek-R1-Distill-Qwen-1.5B.rkllm` 是转换后的模型
>
> `llm_demo` 是编译后的llm测试程序



* 拷贝测试包到RK3588




- 设置环境变量

```
$ export LD_LIBRARY_PATH=./lib
```



## 运行测试

```
$ ./llm_demo DeepSeek-R1-Distill-Qwen-1.5B.rkllm 10000 10000
```



## 性能分析

对于数学问题：`Solve the equations x+y=12, 2x+4y=34, find the values of x and y`，RK3588 实现每秒 14.93 个令牌。

| 阶段 | 总时间（毫秒） | 代币 | 每个令牌的时间（毫秒） | 每秒令牌数 |
| ---- | -------------- | ---- | ---------------------- | ---------- |
| 预填 | 429.63         | 81   | 5.30                   | 188.53     |
| 产生 | 56103.71       | 851  | 66.99                  | 14.93      |





export RKLLM_LOG_LEVEL=1













