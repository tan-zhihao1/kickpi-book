# 01-SD_Card_Flashing

## 1. Android SD Card Image Burning

### Preparation Tools

1. Prepare an SD card and card reader.
2. Download the burning tool **PhoenixCard**.
3. Download the Android image.

> Software tools can be downloaded from the network drive:  
> - **PhoenixCard**: `5-DevelopmentTools\SD_Boot_Card_Tool\PhoenixCard4.2.8.rar`  
> - **Android image**: `2-Image\K2\*\Android12.0_*`

---

### Install the Burning Tool

Extract `PhoenixCard.rar` and run the tool. The interface is shown below:  
![image-20250509185807163](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509185807163.png)

---

### Burn the Image

**Differences between “Product” and “Start up”**  

- **Product**: Automatically burns the system image from the SD card into eMMC. It cannot boot directly.  
- **Start up**: Boots the system directly from the SD card.  

#### Start up Card Burning Steps  
1. Insert the SD card into the computer.  
2. Follow the steps below in the burning tool:  
![image-20250509190012699](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509190012699.png)  

Insert the burned SD card into the development board and power on to boot automatically.  

#### ProductCard Burning Steps  
1. Select the image file.  
2. Choose **Product**.  
3. Start burning.  
![image-20250509190047639](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509190047639.png)  

Insert the SD Product card into the development board and power on. The system power LED will turn off after completion.  

---

## 2. Ubuntu Image SD Card Burning

### Preparation Tools

**Hardware**  
- A 16GB+ SD card (Class 10 or higher recommended).  

**Software**  
- Download **balenaEtcher** from:  
  `5-DevelopmentTools\SD_card_burning_tool\balenaEtcher-1.19.3.Setup.exe`  

**Image**  
- Ubuntu image path:  
  `h618_data\2-Image\K2*\Ubuntu2204_*`  

---

### Create a Bootable SD Card

Follow these steps using **balenaEtcher**:  
![image-20250509190242824](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509190242824.png)  

1. Select the Ubuntu image file.  
2. Choose the SD card drive.  
3. Click **Flash** to start burning.  

Wait for completion:  
![image-20250509191546115](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191546115.png)  

---

### Boot from SD Card

1. Insert the SD card into the development board.  
2. Power on. The first boot may take longer.  

---

### Copy from SD Card to eMMC

After booting, execute the following command in the terminal:  
```shell
sudo nand-sata-install 2 1
```
Press Enter, then enter the password: `kickpi`.  

Process screenshots:  
![image-20250217140706329](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250217140706329.png)  

Wait for completion:  
![image-20250217140819381](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250217140819381.png)  

After finishing:  
1. Power off the board.  
2. Remove the SD card.  
3. Power on again.  

Normal completion screens:  
![image-20250217141729269](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250217141729269.png)  
![image-20250217141809550](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250217141809550.png)