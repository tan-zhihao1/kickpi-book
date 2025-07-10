# RKNN-Toolkit2

本文面向零基础用户详细介绍如何快速在计算机上使用 RKNN-Toolkit2 完成模型转换，并通过 RKNPU2 部署到 Rockchip 开发板上。



## 准备开发环境

> Ubuntu 22.04



### 下载 **RKNN** 相关仓库

建议新建一个目录用来存放 RKNN 仓库，例如新建一个名称为 Projects 的文件夹，并将 RKNN-Toolkit2 和 RKNN Model Zoo 仓库存放至该目录下，参考命令如下：

```bash
# 新建 Projects 文件夹
mkdir Projects

# 进入该目录
cd Projects

# 下载 RKNN-Toolkit2 仓库
git clone https://github.com/airockchip/rknn-toolkit2.git --depth 1

# 下载 RKNN Model Zoo 仓库
git clone https://github.com/airockchip/rknn_model_zoo.git --depth 1

# 注意：
# 1.参数 --depth 1 表示只克隆最近一次 commit
# 2.如果遇到 git clone 失败的情况，也可以直接在 github 中下载压缩包到本地，然后解压至该目录
```



### 安装 **RKNN-Toolkit2** 环境

#### **安装 Python**

如果系统中没有安装 Python 3.8（建议版本），或者同时有多个版本的 Python 环境，建议使用Miniforge

Conda 创建新的 Python 3.8 环境。

**安装 Miniforge Conda**

在计算机的终端窗口中执行以下命令，检查是否安装Miniforge Conda，若已安装则可省略此节步骤。

```
conda -V
```

如果没有安装 Miniforge Conda，可以通过下面的链接下载 Miniforge Conda 安装包：

```
wget -c https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
```

然后通过以下命令安装 Miniforge Conda：

```
chmod 777 Miniforge3-Linux-x86_64.sh
bash Miniforge3-Linux-x86_64.sh
```

#### 使用 **Miniforge Conda** 创建 **Python** 环境

在计算机的终端窗口中，执行以下命令进入 Conda base 环境：

