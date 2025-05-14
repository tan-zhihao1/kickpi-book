# 01-RK_Chip_Comparison_Introduction



## 1. Comparison between RK3568 and RK3562

|            |                            RK3562                            |                            RK3568                            |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|    CPU     |                      Quad-core ARM Cortex-A53                      |                      Quad-core ARM Cortex-A55                      |
|    Clock Frequency    |                            1.8 GHz                            |                            2.0 GHz                            |
|    GPU     |                           Mali-G52                           |                           Mali-G52                           |
|    NPU     |                            1 TOPS                             |                            1 TOPS                             |
|  Memory Support  |          DDR3/DDR3L/DDR4/LPDDR4/LPDDR4X, up to 8G           |      DDR3/DDR3L/DDR4/LPDDR3/LPDDR4/LPDDR4X, up to 8G       |
|  Storage Interface  |                  eMMC 5.1, SD3.0, Nor Flash                  |          eMMC 5.1, SD3.0, Nor Flash, LBA Nand Flash          |
|  Display Interface  |                       MIPI DSI, LVDS                        |                 HDMI 2.0, MIPI DSI *2, LVDS                  |
|  Interface Support  | Dual network ports (100M + 1000M), PCIe 2.1, USB 3.0, USB 2.0, I2S, SDIO 3.0, UART, SPI, I2C, SPDIF, PWM | **Dual Gigabit network ports**, **PCIe 3.0**, PCIe 2.1, **SATA 3.0**, USB 3.0, USB 2.0, I2S, SDIO 3.0, UART, SPI, I2C, SPDIF, PWM, CAN |
| Camera Interface |                      MIPI CSI 4Lane *2                       |                        MIPI CSI 4Lane                        |
|  Image Processing  |    13M ISP supports 4K@30fps H.265 decoding and 1080P@60fps H.264 encoding     |  8M ISP supports 4K@60fps H.265/H.264 decoding, and the encoding ability is 1080P@60fps  |

> Overall, the performance of RK3568 is about 20% stronger than that of RK3562, and it has more interfaces. RK3562 has lower power consumption and lower cost.