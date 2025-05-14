# 01 - Log Collection

SDK - Refers to the source code path.

Console - Refers to the debugging console.

ADB - Android Debug Bridge command-line tool. Hereinafter, it refers to an environment where ADB can be run.

## Kernel Log Collection Methods

### General Method

Command line - Debug serial port

1. Refer to [12 - Hardware Function Testing.md](12 - Hardware Function Testing.md#UART) to connect the debug serial port.
2. Kernel log printing command:
   ```shell
   (console) $ dmesg
```
3. Log saving:
   Various serial port software on Windows has its own saving methods. The following only introduces the saving method of the MobaXterm_Personal_20.3 tool provided on the network disk.
   Right-click on the corresponding window and select "Save current window output".
   ![image-20250328102712644](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250328102712644.png)

### Linux

Command line - Virtual terminal

1. Connect the motherboard to a monitor and a keyboard.
2. Enter the command to print the kernel log:
   ```shell
   (console) $ dmesg
```
3. Save the log as a file:
   ```shell
   (console) $ dmesg > dmesg.txt
   ```
4. Copy the file out via a USB flash drive.

### Android

Command line - ADB

1. Power on the board and connect the computer to the board's USB - Type C port.
2. Open the Windows command prompt (cmd) and enter the following command to ensure that the device is detected:
   ```
   (ADB) # adb devices
   ```
3. Enter the command to save the kernel log to the dmesg.txt file on the D drive:
   ```
   (ADB) # adb shell dmesg > D:/dmesg.txt
   ```

Android system logs

1. Power on the device and start the collection with the following command:
```
(ADB) # adb logcat > D:/logcat.txt
```
2. Reproduce the problem.
3. Press Ctrl + C to stop.
4. Provide the logcat.txt file.

## Common Problem Troubleshooting

Please ensure that the firmware being flashed is the one provided on the network disk so that technical support can assist you in troubleshooting.

### Boot Stuck at the Logo Screen

1. Check if the firmware is the one provided. Only when using the firmware from the network disk can technical support help troubleshoot the problem.
2. Restart the device multiple times to see if the problem persists.
3. If you are using the Ubuntu system, the boot process may be slow. Wait patiently to see if it eventually enters the graphical interface.
4. After the above checks, if the device is still stuck at the logo screen, perform the following checks:
5. Confirm if the blue system running indicator is blinking.
6. Connect the debug serial port and keep all the boot logs for technical support to assist in troubleshooting.

### Serial Port Printing Issues

If there is no printing or the printing is garbled:

1. Check the wiring. Refer to [12 - Hardware Function Testing.md](12 - Hardware Function Testing.md#UART) to connect the debug serial port.
2. Ensure that the serial port tool you are using is the one provided by our company. If not, make sure it supports a baud rate of 1500000.

### System Keeps Restarting

1. Remove all peripherals to eliminate peripheral interference.
2. Connect the serial port to view the logs for troubleshooting.
3. Try replacing the power supply. Poor power supply can cause some functions of the board to malfunction and lead to restarts.
