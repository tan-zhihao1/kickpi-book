# ADB_Tutorial

ADB (Android Debug Bridge) is a debugging bridge tool from Google's Android SDK Platform-Tools for Android development. It supports connecting to devices via USB or network, enabling device management and debugging operations such as application installation, file transfer, and command execution.

## Quick Start with ADB

### Debugging Cable and Interface

- Debugging Cable: Use a USB Type-C data cable.
- Debugging Interface: The USB Type-C interface on the motherboard.

### **Obtaining and Installing ADB Tools**

**Obtaining the Tools**

The ADB software tools can be obtained from the KICKPI cloud disk for use on the Windows platform. They are located in the following directory on the cloud disk:

```
5-DevelopmentTool/win_x64_ADB_Tool/adb-tools.rar
```

>Google's latest Android SDK Platform-Tools can be downloaded from https://developer.android.com/tools/releases/platform-tools .

**Installation Steps**

1. Extract the `adb-tools.rar` archive, for example, to the path `D:\ADB Tool\adb-tools`.
2. Open a command-line window and switch to the extracted directory:

```
ADB$ cd D:\ADB Tool\adb-tools
```

3. Run the following command. A successful run indicates successful installation:

```
ADB$ adb --version
```

Example:

```
PS D:\ADB Tool\adb-tools> .\adb.exe --version
Android Debug Bridge version 1.0.40
Version 4797878
Installed as D:\ADB Tool\adb-tools\adb.exe
```

### **Detecting Devices with ADB**

1. Connect the computer to the motherboard using a TYPE-C data cable.
2. Open a command-line window, switch to the directory where the ADB tools are extracted, and run the following command. If the connection is successful, the corresponding device can be detected:

```
ADB$ adb devices
```

Example:

```
PS D:\ADB Tool\adb-tools> .\adb.exe devices
List of devices attached
5d5543a9bcc93eec        device
```

## Using ADB Features

ADB, full name Android Debug Bridge, is a debugging bridge tool for Android development. It supports connecting to devices via USB or network, enabling device management and debugging operations such as application installation, file transfer, and command execution.

The following is an introduction to some commonly used ADB commands.

### View help

```bash
adb help
```

### Device Connection and Management

**Viewing Connected Devices**

```bash
adb devices
```

This command is used to view all devices currently connected to the computer via ADB. If a device is correctly connected and authorized, its serial number and status (e.g., "device") will be displayed.

**Restarting the Device**

```bash
adb reboot
```

This command restarts the connected device.

### File Operations

**Uploading Files to the Device**

```bash
adb push <local file path> <device target path>
```

For example, to upload the local `test.txt` file to the `/sdcard/` directory on the device, use the following command:

```bash
adb push test.txt /sdcard/
```

**Downloading Files from the Device**

```bash
adb pull <device file path> <local target path>
```

To download the `/sdcard/log.txt` file from the device to the local current directory, use:

```bash
adb pull /sdcard/log.txt .
```

**Performing File System Operations on the Device**

```bash
adb shell ls /sdcard
```

This command executes the `ls` command in the device's shell environment to view the list of files and folders in the `/sdcard` directory.

### Application Management

> Only for Android platforms

**Installing an Application**

```bash
adb install <APK file path>
```

To install the local `app.apk` file, use:

```bash
adb install app.apk
```

**Uninstalling an Application**

```bash
adb uninstall <application package name>
```

For example, to uninstall the application with the package name `com.example.app`, use:

```bash
adb uninstall com.example.app
```

**Clearing Application Data**

```bash
adb shell pm clear <application package name>
```

This command clears all data of the specified application. To clear the data of `com.example.app`, use:

```bash
adb shell pm clear com.example.app
```

### System Debugging

**Entering the Device's Shell Environment**

```bash
adb shell
```

After executing this command, you will enter the device's command-line interface, where you can execute various system commands.

**Viewing System Logs**

> Only for Android platforms

```bash
adb logcat
```

This command is used to view the device's system logs, which can help developers locate application or system issues. Press `Ctrl + C` to stop the log output.

**Taking a Screenshot**

> Only for Android platforms

```bash
adb shell screencap -p /sdcard/screenshot.png
```

This command generates a screenshot file named `screenshot.png` in the `/sdcard` directory on the device. You can then use the `adb pull` command to download it to the local machine.


## Common Issues

### ADB Device Not Recognized

When performing ADB debugging, check if the system Device Manager detects a device similar to the following example **while the board's blue light is blinking**:  

![image-20250509185304921](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509185304921.png)  

If an **Unknown Device** is detected (as shown below), manually install the driver:  

![image-20250226152046272](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152046272.png)  

**Manually Install the Driver**  

Follow these steps:  

1. ![370849870429aae4906079eb686ee326](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/370849870429aae4906079eb686ee326.jpg)  
2. ![image-20250226152541205](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152541205.png)  
3. ![image-20250226152555933](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152555933.png)  
4. ![image-20250226152618854](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152618854.png)  
5. ![image-20250226152635425](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152635425.png)  
6. ![image-20250226152643858](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152643858.png)  
7. ![image-20250226152654260](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152654260.png)  
```  





