# SD_Card_Flashing

The path of the burning tool network disk:

``` 
5-DevelopmentTool\SD_Boot_Card_Tool\PhoenixCard4.2.8.rar
```



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

Insert the SD Product card into the development board and power on. 