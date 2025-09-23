# UART

UART（Universal Asynchronous Receiver/Transmitter，通用异步收发传输器）是一种广泛用于串行通信的硬件接口协议，用于在两个设备之间异步传输数据（无需时钟信号同步，仅通过约定的波特率等参数实现通信）。它是嵌入式系统、单片机、计算机与外设（如传感器、蓝牙模块、GPS 模块等）之间常用的通信方式。



## UART位置

UART 一般位于扩展引脚，通过扩展引脚图查看扩展引脚中带有 UART 标志的引脚。

> 扩展引脚上的 UART 接口与设备文件的对应关系为：UART 编号直接对应 `/dev/ttyS` 后的数字，例如 UART6 对应设备文件 `/dev/ttyS6`。

**K7扩展引脚**

![K7_PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN.png)

**K7引脚电压**

![K7_PIN_DOMAIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_PIN_DOMAIN.png)

> **注意：** K7的UART默认配置的都是1.8V的电平，不能与3.3V电平的UART通讯，请确定好后使用。
>
> [不同电平有什么区别，为什么不能通讯？](#uart_ttl)



## 硬件连接

**基本信号线（核心连接）**

UART 通信最少需要 3 根线即可工作（全双工通信）：

TXD（Transmit Data）：发送数据端，设备通过此线向外发送数据。

RXD（Receive Data）：接收数据端，设备通过此线接收外部数据。

GND（Ground）：地线，用于保证两个设备的电平参考一致（共地）。

**连接原则**

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



## Android SerialPort API

“Android SerialPort API” 是早期开源的 Android 串口通信工具类项目。其核心功能是为 Android 设备提供**串口（如 RS232、UART）通信的封装接口**，解决 Android 系统原生缺乏串口操作统一 API 的问题，方便开发者实现设备与外部硬件（如传感器、单片机、POS 机等）的串口数据交互。

```
https://code.google.com/archive/p/android-serialport-api/source/default/source
```

网盘路径





## 常用测试工具

### microcom

`microcom` 是一款轻量级的串口通信工具，常用于嵌入式系统和Linux环境下的串口调试，支持通过命令行配置串口参数并进行数据收发。以下是其基本用法和常见场景：

**源码**

```
https://github.com/pengutronix/microcom
```

**一、安装 microcom**

在 Ubuntu/Debian 系统中，可直接通过包管理器安装：
```bash
sudo apt update
sudo apt install microcom
```

在嵌入式系统（如 OpenWRT）中，通常已预装，若未安装可通过系统包管理工具安装（如 `opkg install microcom`）。

**二、基本用法**

1. 连接串口设备

基本语法：
```bash
microcom [选项] <串口设备路径>
```

示例：连接到 `/dev/ttyUSB0` 串口（常见的USB转串口设备）：
```bash
microcom /dev/ttyUSB0
```

默认参数：波特率 9600、8位数据位、1位停止位、无校验（8N1）。

2. 自定义串口参数

若设备使用非默认参数（如波特率 115200），需通过 `-s` 选项指定：
```bash
# 波特率 115200，设备 /dev/ttyS0（传统串口）
microcom -s 115200 /dev/ttyS0
```

其他常用选项：
- `-p < parity >`：设置校验位（`n` 无校验，`o` 奇校验，`e` 偶校验），默认 `n`；
- `-8`：强制使用8位数据位（默认）；
- `-t`：设置超时时间（单位：毫秒），如 `-t 1000` 表示超时1秒。

示例（完整配置）：
```bash
# 115200波特率，偶校验，设备 /dev/ttyUSB1
microcom -s 115200 -p e /dev/ttyUSB1
```

**三、使用技巧**

1. **退出 microcom**  
   按 `Ctrl+q` 组合键退出当前连接（注意：不是 `Ctrl+c`，后者会被转发到串口设备）。

2. **数据收发**  
   进入 `microcom` 交互界面后，输入的内容会直接发送到串口设备，设备返回的数据会实时显示在终端上，适合调试传感器、单片机等设备的串口输出。

3. **查看可用串口**  
   若不确定设备路径，可通过以下命令列出系统中的串口设备：
   ```bash
   ls /dev/ttyS*   # 传统串口（如 ttyS0、ttyS1）
   ls /dev/ttyUSB* # USB转串口设备（如 ttyUSB0）
   ls /dev/ttyACM* # 某些USB设备（如Arduino）可能识别为此类
   ```

**四、常见问题**

1. **权限不足**  
   提示 `Permission denied` 时，需确保当前用户有权限访问串口设备。可将用户加入 `dialout` 组（Ubuntu/Debian）：
   ```bash
   sudo usermod -aG dialout $USER
   # 需重新登录生效
   ```

2. **设备被占用**  
   提示 `Device or resource busy` 时，说明串口已被其他程序占用（如 `minicom`、`screen` 等），需先关闭占用程序。

3. **乱码问题**  
   通常是波特率、校验位等参数与设备不匹配导致，需确认设备的串口参数并重新配置。

`microcom` 以简洁著称，适合快速调试；若需更复杂的功能（如日志记录、宏定义），可考虑 `minicom` 或 `screen` 工具。通过 `man microcom` 可查看完整选项说明。



### minicom

`minicom` 是一款功能强大的串口通信工具，支持更丰富的配置和操作（如保存配置、文件传输等），比 `microcom` 更适合复杂的串口调试场景。以下是其核心用法：

**源码**

```
https://salsa.debian.org/minicom-team/minicom
```

**一、安装 minicom**

在 Ubuntu/Debian 系统中安装：
```bash
sudo apt update
sudo apt install minicom
```

**二、基本配置（首次使用必做）**

1. 启动配置界面：
   ```bash
   sudo minicom -s  # -s 表示进入设置模式
   ```

2. 核心配置项（通过方向键选择，回车进入）：
   - **Serial port setup**（串口设置）：  
     按提示修改以下参数（按对应字母进入编辑）：  
     - `A`：串口设备路径（如 `/dev/ttyUSB0` 或 `/dev/ttyS0`）  
     - `E`：波特率/数据位/停止位（如 `115200 8N1`，即波特率115200、8数据位、无校验、1停止位）  
     - `F`：硬件流控（默认 `No`，即关闭）  
     完成后按 `Enter` 返回，再按 `Esc` 退出当前菜单。

   - **Save setup as dfl**（保存为默认配置）：  
     后续直接输入 `minicom` 即可使用此配置。

   - **Exit**（退出配置界面，进入串口交互模式）

**三、基本操作**

1. 启动 minicom（使用默认配置）：
   ```bash
   minicom
   ```
   若需加载其他配置文件（如 `myconfig`）：
   ```bash
   minicom myconfig
   ```

2. 退出 minicom：  
   按 `Ctrl+A` 进入命令模式，再按 `Q` 并确认（`Yes`）。

3. 常用快捷键（先按 `Ctrl+A`，再按以下键）：  
   - `Z`：显示所有快捷键帮助  
   - `O`：临时打开配置界面  
   - `W`：自动换行（解决长文本换行问题）  
   - `L`：日志记录（保存串口输出到文件）  
   - `S`：发送文件（支持 Xmodem、Ymodem 等协议）  
   - `R`：接收文件  

**四、高级用法**

1. **文件传输**：  
   - 发送方：`Ctrl+A` → `S` → 选择传输协议（如 `xmodem`）→ 选择本地文件。  
   - 接收方：确保目标设备已进入接收模式，在 minicom 中 `Ctrl+A` → `R` → 选择相同协议。

2. **多配置管理**：  
   - 保存新配置：`sudo minicom -s` → 配置完成后选择 `Save setup as <name>`（如 `uart1`）。  
   - 加载配置：`minicom uart1`。

3. **权限问题解决**：  
   若提示无权限访问串口，将用户加入 `dialout` 组（无需每次用 `sudo`）：
   ```bash
   sudo usermod -aG dialout $USER
   # 重新登录生效
   ```

**五、常见问题**

- **乱码**：检查波特率、数据位等参数是否与设备匹配（`Ctrl+A` → `O` 可临时修改）。  
- **无法输入**：确认硬件流控是否关闭（配置中 `Hardware Flow Control` 设为 `No`）。  
- **设备被占用**：关闭其他占用串口的程序（如 `screen`、`microcom`），或重启设备。


通过 `man minicom` 可查看完整手册。`minicom` 适合需要频繁调试不同串口设备或进行文件传输的场景，配置一次后可重复使用，效率较高。



### stty

`stty` 是 Linux 系统中用于配置和查询终端设备（如终端、串口等）参数的命令行工具，全称为 "set tty"。它可以调整终端的输入输出模式、控制字符、波特率等设置，常用于终端行为定制和串口调试。

源码：

```
https://github.com/coreutils/coreutils/blob/master/src/stty.c
```

**一、基本用法**

1. 查看终端参数

```bash
# 显示当前终端的基本配置
stty

# 显示所有参数（详细模式，包括控制字符）
stty -a

# 查看指定设备的参数（如串口 /dev/ttyS0）
stty -F /dev/ttyS0 -a
```

2. 修改终端参数

（1）控制输入回显

```bash
# 关闭输入回显（输入内容不显示在终端，常用于密码输入场景）
stty -echo

# 恢复输入回显
stty echo
```

（2）配置串口参数（波特率、数据位等）

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

（3）修改控制字符（如中断、退出键）

```bash
# 查看当前控制字符（如 Ctrl+C 对应 intr）
stty -a | grep intr

# 临时修改中断字符为 Ctrl+T（原 Ctrl+C 失效）
stty intr '^T'

# 恢复默认中断字符（Ctrl+C）
stty intr '^C'
```

（4）设置终端尺寸

```bash
# 设置终端为 24 行，80 列
stty rows 24 cols 80
```

3. 重置终端配置

当终端因异常设置（如乱码、无法输入）导致无法正常使用时，可重置为默认状态：
```bash
stty sane
```

**二、常用参数说明**

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

**三、实际应用场景**

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

### **不同电平有什么区别，为什么不能通讯？**<a id="uart_ttl"></a>

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