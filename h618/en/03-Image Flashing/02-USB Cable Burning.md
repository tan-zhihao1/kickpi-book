# 02-USB Cable Burning

## 1. Android USB Cable Image Burning

The image can be downloaded from the network drive at:  
``` 
h618_data\2-Image\K2*\Android12.0_* 
```

The development board comes pre-installed with the default image. Skip this step if no system replacement is needed.  

---

### Preparation Tools  

**Download Burning Tool**  
``` 
h618_data\5-DevelopmentTools\USB_ImageBurnTool\PhoenixSuit.zip 
```

**Hardware Preparation**  
Prepare a USB A-to-C cable for connecting the host PC to the development board.  

---

### Install USB Driver  

**Extract PhoenixSuit.zip**  
The USB driver directory:  
``` 
PhoenixSuit/Drivers/AW_Driver/ 
```

> The driver cannot be installed directly. It requires recognizing an "Unknown Device" in Device Manager first.  

**Enter Burning Mode on the Board**  
1. Press and hold the **maskrom** button on the back of the board.  
2. Power on the board. If already powered, restart it (press the reset button).  
3. Release the **maskrom** button after ~3 seconds.  

Detailed steps:  
![image-20250509191757297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191757297.png)  

---

**Open Device Manager on PC**  
If the board enters burning mode successfully and the PC lacks the USB driver, an "Unknown Device" will appear. Follow the steps below to install the driver:  

![image-20250219155858913](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155858913.png)  
![image-20250219155942874](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155942874.png) 
 ![image-20250219160431151](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160431151.png)

![image-20250219160504707](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160504707.png)

---

### Common Issues  

1. **Compatibility Error**   

Refer to Microsoft’s official solution:  
https://support.microsoft.com/zh-cn/windows/%E6%97%A0%E6%B3%95%E5%9C%A8%E6%AD%A4%E8%AE%BE%E5%A4%87%E4%B8%8A%E5%8A%A0%E8%BD%BD%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F-8eea34e5-ff4b-16ec-870d-61a4a43b3dd5  

  

---

### Install Image Burning Tool  

**Extract PhoenixSuit.zip**  

**Open PhoenixSuit.exe**  
![image-20240306170759670](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170759670.png)  

**Burning Tool Interface**  
![image-20240306170835586](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170835586.png)  

---

### Burn the Image  

**Select Image**  
1. Go to the **One-Click Flash** interface.  
2. Click **Browse** to select the image.  
![image-20240306171236404](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171236404.png)  

**Enter Burning Mode on the Board**  
Steps:  
1. Press and hold the **maskrom** button.  
2. Power on/restart the board.  
3. Release the button after ~3 seconds.  
![image-20250509191757297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191757297.png)  

**Start Burning**  
1. Click **Yes** to begin.  
2. Wait for the progress bar to complete.  
3. The board will reboot automatically after burning.  
![image-20240306171455427](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171455427.png)  

---

### Android Desktop Interface  

Connect the board to an HDMI display. After successful booting, the interface will appear as follows:  
![image-20240306171855522](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171855522.png)