# 03-Functional Testing

## LED

The development board is equipped with two LEDs:  
- **Green LED**: Power indicator (stays on when powered).  
- **Blue LED**: Heartbeat indicator (blinks continuously after the kernel boots).  

**User-Level LED Control**  

By default, the LED trigger mode is set to `heartbeat`, which prevents manual control. To enable command-based control, use the following commands:  

```shell
$ echo none > /sys/class/leds/sysled/trigger
$ echo 1 > /sys/class/leds/sysled/brightness  # Turn on
$ echo 0 > /sys/class/leds/sysled/brightness  # Turn off
```

---

## WiFi / Bluetooth

The K5C board integrates the AW859A high-performance WiFi module.  

**WiFi Operations**  
```shell
# List network devices
sudo nmcli dev

# Enable WiFi
sudo nmcli r wifi on

# Disable WiFi
sudo nmcli r wifi off

# Scan for nearby hotspots
sudo nmcli dev wifi

# Connect to a hotspot (replace SSID and PASSWORD)
sudo nmcli dev wifi connect "SSID" password "PASSWORD" ifname wlan0
```

**Bluetooth Operations**  
Install dependencies:  
```shell
apt-get install pulseaudio-module-bluetooth
reboot
```

Control commands:  
```shell
bluetoothctl           # Enter Bluetooth console
scan on                # Start scanning
pair 28:52:E0:19:17:69 # Pair with a device
connect 28:52:E0:19:17:69 # Connect to a device
```

**Bluetooth Audio Playback**  
*Note: Only works on desktop terminals, not serial terminals.*  
```shell
# Set default audio sink
pacmd set-default-sink bluez_sink.28_52_E0_19_17_69
# Play audio
aplay sample-15s.wav
```

---

## Key Functions

The K5C board has three buttons:  
- **POWER**: Controls screen on/off (Android) or opens logout screen (Ubuntu).  
- **RESET**: System reset button.  
- **RECOVERY**: Enters firmware flashing mode.  

---

## RTC Testing

**Power-Off Time Retention Test**  
Steps:  
1. Connect the board to the network for automatic time synchronization.  
2. Disconnect network and power.  
3. Wait for a period, then reconnect power.  
4. If the system time matches the current time, RTC functions properly.  

*Note: Ensure a CR2032 battery is installed and charged.*  

---

## IR (Infrared) Testing

Check input devices:  
```shell
cat /proc/bus/input/devices
```

Test IR receiver:  
```shell
hexdump /dev/input/event2  # Press remote buttons to see data output
```

---

## LCD Testing

Supports **Single LVDS**, **Dual LVDS**, and **MIPI** displays. For details, see [LCD Functional Debugging](../05-Peripheral-Drivers/LCD功能调试.md).  

**Backlight Control**  
```shell
echo lcd0 > /sys/kernel/debug/dispdbg/name
echo setbl > /sys/kernel/debug/dispdbg/command
echo 128 > /sys/kernel/debug/dispdbg/param  # Set brightness (0-255)
echo 1 > /sys/kernel/debug/dispdbg/start
```

---

## Fan Control

The board includes a GPIO-controlled fan (enabled by default).  

```shell
# Check fan status
cat /sys/class/leds/fan_en/brightness

# Turn on
echo 255 > /sys/class/leds/fan_en/brightness

# Turn off
echo 0 > /sys/class/leds/fan_en/brightness
```

---

## Speaker/Headphone Testing

**Speaker Control**  
```shell
# Enable speaker
echo 255 > /sys/class/leds/spk_en/brightness

# Disable speaker
echo 0 > /sys/class/leds/spk_en/brightness
```

**Audio Playback**  
Ubuntu:  
```shell
aplay sample-15s.wav
```

Android:  
```shell
tinyplay sample-15s.wav
```

---

## Microphone Testing

**Ubuntu**  
```shell
# Record audio
arecord -Dhw:sun50iw10codec -f S24_LE -r 16000 -vvv test.wav
# Playback
aplay test.wav
```

**Android**  
```shell
tinymix "ADCL Input MIC1 Boost Switch" 1
tinycap file.wav -D 0 -d 0 -c 2 -r 16000 -b 16 -T 10
tinyplay file.wav
```

---

## Ethernet Testing

Supports Gigabit Ethernet. Speed test results:  
- **Upload**: 783 Mbits/sec  
- **Download**: 898 Mbits/sec  

---

## 4G Module Testing

**Prerequisites**  
- Verify USB module detection:  
  ```shell
  lsusb
  ls /dev/ttyUSB*
  ```
- Insert SIM card and ensure antennas are connected.  

**Dial-Up Test**  
```shell
pppd call quectel-ppp &  # Start PPP connection
ping www.baidu.com       # Test connectivity

# Debugging commands
jobs       # View background processes
fg %1      # Bring process to foreground
kill -9 $(ps -ef | grep pppd | awk '{print $2}')  # Terminate process
```

---

## SD Card Testing

```shell
# List partitions
fdisk -l

# Mount SD card
mount /dev/mmcblk1p1 /mnt/
```

---

## Touchscreen Testing

```shell
# List input devices
cat /proc/bus/input/devices

# Install evtest
apt install evtest
evtest  # Interactive touch test
```

---

## USB/SD Card Detection

```shell
fdisk -l  # List storage devices
```