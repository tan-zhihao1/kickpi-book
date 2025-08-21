# UART

**K7扩展引脚**

![K7_PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN.png)

**K7引脚电压**

![K7_PIN_DOMAIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN_DOMAIN.png)

K7主板扩展引脚可配置 UART 接口，通过[扩展引脚章节](#ExpansionPin-K7)查看对应的 UART 位置及通道号。

**注意：** K7的UART默认配置的都是1.8V的电平，不能与3.3V电平的UART通讯，请确定好后使用。

[不同电平有什么区别，为什么不能通讯？](#uart_ttl)

**回环测试**

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