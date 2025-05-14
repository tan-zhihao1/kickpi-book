# 02-Common_FAQ

## Power-on and Power Supply Issues

When the motherboard is connected to multiple peripherals, resulting in high power consumption, after the power is connected, the power-on green light may be dim or not stay lit constantly, and the system startup may also encounter abnormalities.

**Solution**: This is generally a power supply issue. Please check whether the power consumption of the corresponding power supply is sufficient to support the power supply of the connected peripheral modules.



## MIPI Screen Display Issues

If the MIPI screen fails to display, check whether the wiring is correct and whether the backlight is lit normally.

**Solution**:
- If the backlight is not lit, check the wiring.

- If the backlight is lit, check the software.

   (K7 V1.2 does not support automatic recognition of MIPI screens yet. Different software needs to be burned for corresponding screens.)



## Debug Serial Port Usage Issues

When using Mobaxterm software or other serial port tools to connect to the serial port, you may find issues such as serial port garbled characters or the ability to only display but not write.

**Solution**: Generally, the debug serial port tool does not support a baud rate of 1,500,000. It is recommended to use a debug serial port tool that supports a baud rate of 1,500,000.



## System Startup Stuck at the Logo Screen

1. Ensure that the firmware being used is the one provided. Only when using the firmware provided on the network disk can technical support assist in troubleshooting.
2. Restart the system multiple times and check if the issue persists every time.
3. If you are using the Ubuntu system, the startup may sometimes be slow. Please wait patiently and observe if the system subsequently enters the graphical interface normally.
4. After confirming the above, if the system is still stuck at the logo screen, conduct the following checks:
5. Confirm whether the blue system running light is blinking.
6. Connect to the debug serial port and keep all printed startup logs for technical support to assist in troubleshooting.



## System Continuously Restarting

1. Remove all peripherals to eliminate peripheral interference.
2. Connect to the serial port to view the logs for troubleshooting.
3. Try replacing the power supply. Poor power supply can cause some functions of the board to malfunction and lead to restarts.



## Username and Password Errors

When logging in via ssh, the password is incorrect.

**Solution**: Please confirm whether the relevant account exists and whether the password is correct. You can use the following command to change the user password:

```
console$ passwd $username
```

Example:

```
root@kickpi:/# passwd kickpi
New password:
Retype new password:
passwd: password updated successfully
```



## What is the Motherboard Command Line Console? <a id="console_readme"> </a>

The command line console, as referred to in the documentation, generally refers to an operating interface that allows users to interact with the computer system through text commands. Users execute operations (such as file management, program execution, and system configuration) by entering specific instructions, rather than relying on mouse clicks and visual operations in a graphical user interface (GUI).

Examples of command line consoles:
- For the Android system, you can use the serial port or adb shell to access the command line console.

```
console$
```
- For the Debian system, you can use the serial port, adb shell, ssh, etc., to access the command line console.

```
root@linaro$
```
- For the Ubuntu system, you can use the serial port, adb shell, ssh, etc., to access the command line console.

```
kickpi@kickpi$
```
