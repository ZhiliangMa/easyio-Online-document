# Easyio 开源驱动库

***

> &emsp;&emsp;`Easyio` 是一款适配于`ESP-IDF`框架的`开源驱动库`，以支持`ESP32`的简便开发。其目的是在保持官方SDK灵活性的同时，大幅度简化乐鑫`ESP-IDF`开发框架的使用难度。（方便的话，有开源的Arduino和Platform可以用，但在工作上有时会硬性要求使用`ESP-IDF`，毕竟要对接FAE，于是就萌生了搞个 `Easyio` 的想法）

&emsp;&emsp;功能上，`Easyio` 已初具雏形，目前涵盖如下的驱动：

* LED、GPIO（+中断）

* 按键（队列方式，数目几无上限）、触摸按键
* ADC（8通道）、DAC（2通道）
* LEDc、PWM（+输入捕获）、PCNT（编码器计数）
* RMT红外、RMT-WS2812B-RGB灯带
* UART、RS485
* I2C_TOOLS、I2C_MPU6050、I2C_AHT20
* SPI液晶屏（支持`ST7735`、`ST7735S`、`ST7789V`、`ILI9341`、`ILI9488`、`ILI9481`、`ST7796S`、`HX8357C`8种IC，涵盖3.5寸以下的绝大多数液晶模组）
* SPI_AS5047P/TLE5012B 磁编码器。
* FT5/6xxx电容触摸屏。
* FATFS、NVS、VFS、SD_CARD（SPI模式，1/4线SDIO模式）。
* jpg解码（TJpgDec）
* LCD 可以以 `SPI-DMA 双缓冲环形队列`的方式刷屏。（320x240分辨率，RGB565，SPI以`80MHz`速率通信，最大刷屏帧率`53FPS`；40MHz也能有`30.2FPS`。目前DMA加速仅完美支持`ILI9341`、`ST7789V`两种驱动IC型号的屏幕）
* LCD显示波动动效。
* mbedtls 加密算法库。
* WIFI的 TCP、UDP、HTTP、MQTT、SNTP、SCAN 的Demo。
* cJson合成、解析。
* MQTT阿里云物联网设备连接认证。
* `WIFI配网`：SmartConfig、EspTouch、AirKiss、Blufi 齐了，以后抽空将WEB配网补上。
* LAN8720有线以太网。同时支持`IP101`、`DP83848`、`RTL8201`和`DM9051`。
* LVGL暂时只Fork了官方的Demo，修改了sdkconfig，使其适配开发板的 `ST7789V` + `FT6236U`单点电容屏。后续会完善大量Demo来演示其控件和功能使用。
* 蓝牙、GUI待续......
* OTA可能要鸽了，能跑会用，但要整合到库中很费事。