# LVGL_V7

This is an ESP32 demo project showcasing LVGL v7 with support for several display controllers and touch controllers.

此工程fork自 [lvgl/lv_port_esp32](https://github.com/lvgl/lv_port_esp32)

修改了 `sdkconfig`，以适配`ESP32-IOT-KIT`开发板的 `ST7789V + FT6236U` 单点电容屏。

- Version of ESP-IDF required 4.2. 

- Version of LVGL used: 7.9.

***

## 如何使用

1. 递归clone本代码：
```
git clone --recurse-submodules https://github.com/ZhiliangMa/lv_port_esp32_iot_kit.git
```

2. 确保电脑已有 `ESP-IDF V4.2` 环境，`cd` 移动到工程目录。

3. 编译
```
idf.py build
```

4. 下载烧录
```
idf.py flash
```

即可在液晶屏上看到下图显示。

电容触摸屏可用，可进行拖拽、滑动操作，开发板配套的`FT6236U` 为单点电容屏。

![ESP32-IOT-KIT开发板图1](https://github.com/ZhiliangMa/lv_port_esp32_iot_kit/raw/master/images/ESP32-IOT-KIT-LVGL_1.png)

<br/>

同时开发板允许使用上方的 `J5`接口，去插接额外的LCD模组。

![ESP32-IOT-KIT开发板图2](https://github.com/ZhiliangMa/lv_port_esp32_iot_kit/raw/master/images/ESP32-IOT-KIT-LVGL_2.png)

<br/>

运行正视图。

![ESP32-IOT-KIT开发板图](https://github.com/ZhiliangMa/lv_port_esp32_iot_kit/raw/master/images/ESP32-IOT-KIT.jpg)

***

## Demo演示和帧率

此工程可通过图形化设置项，去运行的有4个Demo。

`Component config` >> `lv_examples configuration` >> `Select the demo you want to run`

分别为：
- Show demo widgets。控件演示，使能`Set IRAM as LV_ATTRIBUTE_FAST_MEM`后，320x240分辨率，帧率约为20FPS。

- Demonstrate the usage of encoder and keyboard。可以很好的演示触摸屏和一些拖拽控件，无动作时满帧，有拖动动作时不算特别流畅。

- Benchmark your system。跑分，测试系统刷图成绩。

- Stress test for LVGL。压力测试，帧率抖动明显。


除这4个意外，还可手动改动代码，将 `lv_port_esp32_iot_kit\components\lv_examples\lv_examples\src` 目录下的 `lv_demo_music`、`lv_demo_printer`、`lv_ex_get_started`、`lv_ex_style`、`lv_ex_widgets` 导入运行。


***

## 更多控件的Demo

- 更多例程可见 `components/lv_examples` 目录下的 `lv_ex_get_started`、`lv_ex_style`、`lv_ex_widgets` 文件夹内的 Demo。

- 其中，`lv_ex_widgets` 为所有控件的例程，可帮助我们学习。

- 支持文档：[官方在线文档 - Demo及预览](https://docs.lvgl.io/master/examples.html#a-button-with-a-label-and-react-on-click-event)

![lvgl_ex_demo](../image/lvgl_ex_demo.jpg)

### 如何使用单独控件的Demo

- 1、在main中，引用头文件。

```c
#include "lv_examples/lv_examples.h"
```

- 2、在`main.c`中，注释掉原有的 `create_demo_application();`，添加对应的控件Demo。如 `components/lv_examples/lv_ex_widgets/lv_ex_arc` 中的 弧形控件。

![lvgl_ex_demo_add](../image/lvgl_ex_demo_add.jpg)

- 3、编译、下载运行。能看到如下运行现象。

![lv_ex_demo_arc](../image/lv_ex_demo_arc.jpg)

- 4、LVGL官方附带的Demo，都是跟上面一样的使用方法。支持文档：[官方在线文档 - Demo及预览](https://docs.lvgl.io/master/examples.html#a-button-with-a-label-and-react-on-click-event)


***

## 配套Visual Studio模拟器

&emsp;&emsp;因ESP32的编译、下载速度较慢，且需要电脑连接ESP32设备，导致调试过程些许繁琐。这时可用LVGL的模拟器，来模拟设备开发，运行现象基本一致，代码复制到设备上几乎不用做额外的修改，可加快LVGL界面的开发进程。

- 因本工程的LVGL版本为V7.9，故模拟器也应该使用V7版本的。递归clone，VS模拟器的v7版本分支：
```
git clone --recurse-submodules -b release/v7 https://github.com/lvgl/lv_sim_visual_studio.git
```

&emsp;&emsp;不过我运行时，此分支不能正常运行，只有master可正常运行，即V8的模拟器可正常运行。

***

## 优化刷屏帧率

- 工程内默认配置使用IRAM，默认使用`行像素`x`40行`x`2`字节的双BUFF。运行`benchmark`跑分Demo的成绩为：

40M SPI速度、IRAM开启、320x40x2字节双缓存、160MHz主频。FPS：41。OPS：75%。

- 更快的刷屏方式1：将SPI的刷屏速率，由40MHz提高到80MHz。但很有可能会出现花屏，降低稳定性。（ESP32-IOT-KIT使用ST7789V+FT6236U单点电容屏组合时，以80MHz刷屏时，需将TF卡插入，会解决绝大多数的花屏问题）

- 【修改SPI速率的测试结果，运行`benchmark`跑分Demo的成绩】：

40M SPI速度、IRAM开启、320x40x2字节双缓存、160MHz主频。FPS：41。OPS：75%。

80M SPI速度、IRAM开启、320x40x2字节双缓存、160MHz主频。FPS：52。OPS：68%。

- 更快的刷屏方式1：将ESP32的运行主频，由160MHz提高到240MHz。为了降低功耗，IDF工程一般都是使用ESP32的160MHz去运行，调整为240MHz后会提高MCU的运算能力，从而提高LVGL的刷屏帧率。（menuconfig中寻找`CONFIG_ESP32_DEFAULT_CPU_FREQ_MHZ`，此项可调整ESP32的默认主频）

- 【修改ESP32主频的测试结果，运行`benchmark`跑分Demo的成绩】：

40M SPI速度、IRAM开启、320x40x2字节双缓存、160MHz主频。FPS：41。OPS：75%。

40M SPI速度、IRAM开启、320x40x2字节双缓存、240MHz主频。FPS：53。OPS：82%。

80M SPI速度、IRAM开启、320x40x2字节双缓存、240MHz主频。FPS：60。OPS：78%。

- 个人小结：80MHz的SPI刷屏速率不具备普适性，绝大多数的SPI屏幕能支持的速率还是40MHz。虽然80MHz可以用，而盲目的将SPI速率增大到80MHz只会增加不稳定性。从测试结果可以看出，在ESP32的主频为240MHz时，SPI速率为40MHz和80MHz带来的影响并不是特别的大，而为了稳定性，更建议使用SPI为40MHz的配置。

***

## 注意事项

- `ESP-IDF` 环境建议使用 `V4.2.2`。

- 工程设置项的`SPI`刷屏速率为 `40MHz`，以确保绝大多数使用者可以无误运行此demo。

- 如需修改`SPI`刷屏速率，请参考我的博客 [ESP32+st7789/ili9341运行LVGL例程](https://blog.csdn.net/Mark_md/article/details/120343727?spm=1001.2014.3001.5501)。

- 开发板配套的 `ST7789V + FT6236U` 单点电容屏模组，支持最大 `80MHz` 的SPI速率。但配置为`80MHz`时可能会出现轻微花屏，此时建议将TF卡插入右侧TF卡槽中，即可解决绝大多数问题，实现完美刷屏。