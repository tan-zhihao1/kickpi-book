# USB_Cable_Flashing

---

### Install USB Driver  

**Extract PhoenixSuit.zip** 
The USB driver directory:  

``` 
PhoenixSuit/Drivers/AW_Driver/ 
```

> The driver cannot be installed directly. It requires recognizing an "Unknown Device" in Device Manager first.  

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
[A driver can't load on this device - Microsoft Support](https://support.microsoft.com/en-us/windows/a-driver-can-t-load-on-this-device-8eea34e5-ff4b-16ec-870d-61a4a43b3dd5)

  

---

### Install Image Burning Tool  

**Extract PhoenixSuit.zip**  

**Open PhoenixSuit.exe**  
![image-20250513163349688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250513163349688.png)  

**Burning Tool Interface**  
![image-20250219160538270](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160538270.png)  

---

### Burn the Image  

**Select Image**  

1. Enter the "Firmware" interface.

2. Click on "Image" to select the image.

   ![image-20250219160640908](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160640908.png)  



  

**Start Burning**  

Reconnect the motherboard in burning mode, and the tool will prompt to start burning.

1. Click **Yes** to begin.  
2. Wait for the progress bar to complete.  
3. The board will reboot automatically after burning.  
   ![image-20250219160856586](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160856586.png)  

The board will automatically reboot after successful burning.

