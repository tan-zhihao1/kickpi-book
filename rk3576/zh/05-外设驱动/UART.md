# UART

UART（Universal Asynchronous Receiver/Transmitter，通用异步收发传输器）是一种广泛用于串行通信的硬件接口协议，用于在两个设备之间异步传输数据（无需时钟信号同步，仅通过约定的波特率等参数实现通信）。它是嵌入式系统、单片机、计算机与外设（如传感器、蓝牙模块、GPS 模块等）之间常用的通信方式。



## 扩展引脚

UART 一般位于扩展引脚。



**K7扩展引脚**

![K7_PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN.png)

**K7引脚电压**

![K7_PIN_DOMAIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN_DOMAIN.png)

> **注意：** K7的UART默认配置的都是1.8V的电平，不能与3.3V电平的UART通讯，请确定好后使用。
>
> [不同电平有什么区别，为什么不能通讯？](#uart_ttl)



## 硬件连接

### 基本信号线（核心连接）

UART 通信最少需要 3 根线即可工作（全双工通信）：

TXD（Transmit Data）：发送数据端，设备通过此线向外发送数据。
RXD（Receive Data）：接收数据端，设备通过此线接收外部数据。
GND（Ground）：地线，用于保证两个设备的电平参考一致（共地）。

连接原则：

两个设备之间需要交叉连接：

设备 A 的 TXD → 设备 B 的 RXD
设备 A 的 RXD → 设备 B 的 TXD
设备 A 的 GND → 设备 B 的 GND（必须共地，否则可能无法通信或数据错乱）



## **回环测试**

软件默认已配置 UART8，下面以 UART8 进行示例说明。

RX、TX 进行硬件短接。

microcom 指定 UART8 进行通讯

```
console$ microcom -s 115200 /dev/ttyS8
```

示例：

```
root@linaro-alip:/# microcom -s 115200  -p /dev/ttyS8
[ 4218.137343] of_dma_request_slave_channel: dma-names property of node '/serial@2adb0000' missing or empty
connected to /dev/ttyS8
[ 4218.137431] dw-apb-uart 2adb0000.serial: failed to request DMA, use interrupt mode
Escape character: Ctrl-\
Type the escape character to get to the prompt.
hello uart word!
```

> 回环测试下，microcom 能够同时接收输出的字符



## STTY

`stty` 是 Linux 系统中用于配置和查询终端设备（如终端、串口等）参数的命令行工具，全称为 "set tty"。它可以调整终端的输入输出模式、控制字符、波特率等设置，常用于终端行为定制和串口调试。


### **一、基本用法**
#### 1. 查看终端参数
```bash
# 显示当前终端的基本配置
stty

# 显示所有参数（详细模式，包括控制字符）
stty -a

# 查看指定设备的参数（如串口 /dev/ttyS0）
stty -F /dev/ttyS0 -a
```


#### 2. 修改终端参数
##### （1）控制输入回显
```bash
# 关闭输入回显（输入内容不显示在终端，常用于密码输入场景）
stty -echo

# 恢复输入回显
stty echo
```

##### （2）配置串口参数（波特率、数据位等）
```bash
# 设置串口 /dev/ttyS0 的波特率为 9600（常用波特率：9600、115200 等）
stty -F /dev/ttyS0 9600

# 完整配置：9600 波特率，8 数据位，1 停止位，无校验（8N1 模式）
stty -F /dev/ttyS0 9600 cs8 -parenb -cstopb
# 说明：
# cs8 → 8 数据位
# -parenb → 无校验位（parenb 表示启用校验，加 - 表示禁用）
# -cstopb → 1 停止位（cstopb 表示 2 停止位，加 - 表示禁用）
```

##### （3）修改控制字符（如中断、退出键）
```bash
# 查看当前控制字符（如 Ctrl+C 对应 intr）
stty -a | grep intr

# 临时修改中断字符为 Ctrl+T（原 Ctrl+C 失效）
stty intr '^T'

# 恢复默认中断字符（Ctrl+C）
stty intr '^C'
```

##### （4）设置终端尺寸
```bash
# 设置终端为 24 行，80 列
stty rows 24 cols 80
```


#### 3. 重置终端配置
当终端因异常设置（如乱码、无法输入）导致无法正常使用时，可重置为默认状态：
```bash
stty sane
```


### **二、常用参数说明**
| 参数                | 功能描述                                                                 |
|---------------------|--------------------------------------------------------------------------|
| `-a`                | 显示所有终端参数（详细模式）                                             |
| `-F <设备路径>`     | 指定操作的设备文件（默认是当前终端 `/dev/tty`，串口常用 `/dev/ttyS0` 等） |
| `echo`/`-echo`      | 开启/关闭输入回显                                                       |
| `intr <字符>`       | 设置中断字符（默认 `^C`，即 Ctrl+C）                                    |
| `quit <字符>`       | 设置退出字符（默认 `^\`，即 Ctrl+\）                                    |
| `rows <n> cols <m>` | 设置终端行数为 `n`，列数为 `m`                                           |
| `speed <速率>`      | 设置波特率（如 9600、115200）                                            |
| `cs8`               | 设置 8 位数据位（cs7 为 7 位）                                           |
| `parenb`/`-parenb`  | 启用/禁用校验位（配合 `parodd` 可设置奇校验）                            |
| `cstopb`/`-cstopb`  | 启用 2 停止位/禁用（即 1 停止位）                                        |
| `sane`              | 恢复终端默认配置                                                         |


### **三、实际应用场景**
1. **串口调试**：配置单片机、传感器等设备的串口通信参数（波特率、数据位等）。  
   示例：与波特率为 115200 的设备通信  
   ```bash
   stty -F /dev/ttyUSB0 115200 cs8 -parenb -cstopb
   ```

2. **脚本中隐藏输入**：如读取密码时关闭回显  
   ```bash
   echo "请输入密码："
   stty -echo  # 关闭回显
   read password
   stty echo   # 恢复回显
   echo "密码已接收"
   ```

3. **修复终端乱码**：当终端因异常设置导致乱码时，执行 `stty sane` 重置。


通过 `man stty` 可查看完整的参数说明和更多用法示例。



## 常见问题

**不同电平有什么区别，为什么不能通讯？**<a id="uart_ttl"></a>

电平标准不匹配

- 1.8V UART的逻辑电平：
  - **高电平（逻辑1）**：通常需要输出≥1.35V（根据LVCMOS/LVTTL标准）。
  - **低电平（逻辑0）**：通常需要输出≤0.45V。

- 3.3V UART的逻辑电平：
  - **高电平（逻辑1）**：需要输入≥2.0V才能被识别为有效高电平。
  - **低电平（逻辑0）**：需要输入≤0.8V才能被识别为有效低电平。


要实现1.8V和3.3V UART的通信，必须通过**电平转换电路**进行适配。以下是常见方案：

使用双向电平转换器（推荐）

- **芯片示例**：TXB0108、TXS0108E、74LVC1T45等。
- 原理：
  - 1.8V侧与3.3V侧分别供电（如1.8V和3.3V）。
  - 芯片根据电源电压自动调整输出电平，确保信号兼容性。