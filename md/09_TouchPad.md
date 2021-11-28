# 09_TouchPad

## 例程简介

学习`ESP32`的`触摸传感器`。

使用 `FreeRTOS` 的 `Task`，创建两个任务：

1. **led_task** ，控制LED闪烁

2. **touch_pad_scan_task** ，检测触摸按键，并控制led任务运行状态

使用 `touch_pad_button.c.h` 驱动模块，来对ESP32的 `触摸传感器` 进行配置。

【注意】：开发板因触摸按键的IO与ETH共用，导致走线情况不理想，无法按照官方硬件手册的设计准则去设计。还因为受限于PCB尺寸，无法容纳更大面积的触摸板，最后只在开发板上提供了一10*10mm的触摸按键，
致使最终触摸效果不理想，触摸前后大约只有1/4~1/5初始值的变化，与官方Demo的1/3相差略远。如需修改按键触发阈值，请到 "touch_pad_button.c"文件中的`tp_example_set_thresholds`函数去修改。

## 硬件连接

|        | LED    | Touch_Pad_Button   |
| ------ | ------ | ------------------ |
| ESP32  | GPIO33 | GPIO27             |

Touch_Pad 与 Pin 对照表，可见 `touch_pad_button.c` 文件


## 运行现象

* LED闪烁。触摸按键放上手指后，LED闪烁停止。

* 手指拿开后，LED会继续闪烁。

* 串口输出 `触摸按键的初始计数值`、`当前计数值`、`中断触发状态`。


## 学习内容

1. ESP32的 `触摸传感器`原理，人体电容模型。

2. ESP32最多支持`10`路Touch_Pad的输入，不支持任意GPIO映射，Touch_Pad IO 与 GPIO 对照表在`touch_pad_button.c.h`中。


## 关键函数

```c
// 触摸板按键检测初始化.支持函数重载，支持输入不定数目的通道参数，总参数数目为 2~11。
void touch_pad_button_init(int tp_num, ...);

// 读取滤波后的OUT计数值
touch_pad_read_filtered(i, &value);

//touch pad 的初始感应值，用来做中断阈值的较准（注意，在初始化过程中，电极请勿触碰其他物体，以免干扰此值的正常获取）
extern bool s_pad_activated[TOUCH_PAD_MAX];
```


## 注意事项

- 开发板因触摸按键的IO与ETH共用，导致走线情况不理想，无法按照官方硬件手册的设计准则去设计。还因为受限于PCB尺寸，无法容纳更大面积的触摸板，最后只在开发板上提供了一10*10mm的触摸按键，
致使最终触摸效果不理想，触摸前后大约只有1/4~1/5初始值的变化，与官方Demo的1/3相差略远。如需修改按键触发阈值，请到 "touch_pad_button.c"文件中的`tp_example_set_thresholds`函数去修改。

- touch_pad_button_init支持重载，可输入多个参数，但第一项的参数个数也要跟随改变。例：<br/>
touch_pad_button_init(1, TOUCH_PAD_NUM0);<br/>
touch_pad_button_init(4, TOUCH_PAD_NUM0, TOUCH_PAD_NUM2, TOUCH_PAD_NUM3, TOUCH_PAD_NUM4);

- ESP32 的`T1`为`GPIO0`，一般有电阻下拉到GND做`BOOT`模式选择，导致触摸失效，所以尽量不要用`T1`做 touch pad 功能。

- ESP32的触摸判定原理为：通过读取 touch pad 的初始感应值，使用初始值的 2/3 作为阈值，小于初始值的 2/3 则判定为被人体触摸。如因硬件设计不当，导致人体触摸不灵敏，
可到 `touch_pad_button.c.` 的 `static void tp_example_set_thresholds(void)` 函数中更改阈值。

- 在初始化过程中，触摸板电极请勿触碰其他物体（不要上电过程中手碰触摸板）。以免干扰参考值的获取。

- 触摸板电极的Layout设计，要严格遵守手册的要求。注意ESD防护。