# 04-DeepSeek Large Model

This article will explain how to use RKLLM to deploy the refined model DeepSeek-R1-Distill-Qwen-1.5B to the Rockchip platform and utilize the NPU for hardware-accelerated inference.

Chip platform: RK3576/RK3588

System version: Debian12/Debian11

# I. Development Environment Setup

## RKLLM SDK Documentation

```
doc
└──Rockchip_RKLLM_SDK_CN.pdf
```

## RKNPU Driver

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

## Model Download

## Runtime Download

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
│ └── librkllmrt.so # RKLLM Runtime Library
│ └──include
│ └── rkllm.h # Runtime Header File
│ └── Linux
│ └── librkllm_api
│ └──aarch64
│ └── librkllmrt.so
│ └──include
│ └── rkllm.h
```

# II. Deployment and Operation

## Board-side Deployment Environment

Baidu Netdisk directory: 3-SoftwareData Software Materials / rk35xx-rkllm-deepseek.tar.gz

```
$ ls /rkllm-DeepSeek/
    DeepSeek-R1-Distill-Qwen-1.5B.rkllm
    lib/librkllmrt.so
    llm_demo
```

> Test package description:
>
> `DeepSeek-R1-Distill-Qwen-1.5B.rkllm` is the converted model.
>
> `llm_demo` is the compiled LLM test program.

* Copy the test package to RK3588.

- Set environment variables

```
$ export LD_LIBRARY_PATH=./lib
```

## Run Tests

```
$ ./llm_demo DeepSeek-R1-Distill-Qwen-1.5B.rkllm 10000 10000
```

## Performance Analysis

For the math problem: `Solve the equations x+y=12, 2x+4y=34, find the values of x and y`, RK3588 achieves 14.93 tokens per second.

| Stage | Total Time (ms) | Tokens | Time per Token (ms) | Tokens per Second |
| ---- | -------------- | ---- | ---------------------- | ---------- |
| Pre-fill | 429.63         | 81   | 5.30                   | 188.53     |
| Generation | 56103.71       | 851  | 66.99                  | 14.93      |

export RKLLM_LOG_LEVEL=1
