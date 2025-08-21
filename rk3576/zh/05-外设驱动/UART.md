# UART

UART（Universal Asynchronous Receiver/Transmitter，通用异步收发传输器）是一种广泛用于串行通信的硬件接口协议，用于在两个设备之间异步传输数据（无需时钟信号同步，仅通过约定的波特率等参数实现通信）。它是嵌入式系统、单片机、计算机与外设（如传感器、蓝牙模块、GPS 模块等）之间常用的通信方式。



## 扩展引脚

**K7扩展引脚**

![K7_PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN.png)

**K7引脚电压**

![K7_PIN_DOMAIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN_DOMAIN.png)

> **注意：** K7的UART默认配置的都是1.8V的电平，不能与3.3V电平的UART通讯，请确定好后使用。
>
> [不同电平有什么区别，为什么不能通讯？](#uart_ttl)



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