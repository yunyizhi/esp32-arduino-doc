# jtag调试

这里介绍基于arduino 环境的debug，本质也是使用OpenOCD调试环境。

参考[IDF JTAG 调试](https://docs.espressif.com/projects/esp-idf/zh_CN/v5.2.2/esp32s3/api-guides/jtag-debugging/index.html)

目前这里只提供esp32c3 esp32s3等内置jtag调试配置方法。(
由于目前外接调试器暂未使用官方调试器尝试，使用第三方的，暂未在arduino下测试成功。)

## 代码

代码如下

```C++
#include <Arduino.h>

void setup() {
    uint8_t mac_base[6] = {0};
    if (esp_efuse_mac_get_default(mac_base) == ESP_OK) {
        log_i("efuse mac :%02X:%02X:%02X:%02X:%02X:%02X", mac_base[0], mac_base[1], mac_base[2], mac_base[3],
              mac_base[4], mac_base[5]);
    } else {
        log_e("get efuse mac failed");
    }
}

void loop() {
    delay(1000);
}
```

## 使用platformio调试

#### esp32s3 platformio配置

选择`esp32-s3-devkitc-1`并不需要额外为debug配置其他参数，因为默认debug_tool就是使用内置jtag已经在boards下的json文件中配置好了。

```Ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
```

#### 合宙esp32c3 platformio配置

由于boards下的json文件未指定内置jtag调试，这里配置debug_tool选项为 `esp-builtin`

```Ini
[env:airm2m_core_esp32c3]
platform = espressif32
board = airm2m_core_esp32c3
framework = arduino
debug_tool = esp-builtin
monitor_speed = 115200
upload_speed = 921600
```

#### 使用clion点击debug一键调试

s3需要将usb jtag口连接至pc,而合宙esp32c3只有一个usb口便是jtag。

![clion_debug.png](clion_debug.png)

#### clion操作外设寄存器

##### 加载svd文件

![clion_peripheral.png](clion_peripheral.png)

选择以下目录中的svd文件
<tabs>
    <tab title="Windows">
        <code-block lang="plain text">C:\Users\用户名\.platformio\packages\framework-arduinoespressif32\tools\ide-debug\svd</code-block>
    </tab>
    <tab title="Linux">
        <code-block lang="plain text">/home/用户名/.platformio/packages/framework-arduinoespressif32/tools/ide-debug/svd
          </code-block>
    </tab>
</tabs>
如果已加载的svd不符合芯片类型,可以右键hide

#### 修改寄存器

外设这里只有读取和复制值等功能

可以复制寄存器地址到gdb控制台进行操作

![clion_reg_copy.png](clion_reg_copy.png)


##### gpio操作例子

具体外设可以参考idf
指南中关联的[硬件参考](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32s3/hw-reference/index.html)里面的 
**技术参考手册 (PDF)**

**这里用到三个寄存器**

| 名称            | 地址         | 说明     |
|---------------|------------|--------|
| `ENABLE_W1TS` | 0x60004024 | 设置输出模式 |
| `OUT_W1TS`    | 0x60004008 | 设置为高   |
| `OUT_W1TC`    | 0x6000400c | 设置为低   |

>s3引脚不止32个，多出的引脚在ENABLE1_XXX OUT1_XXX相关寄存器。

比如在gdb 控制台操作2号引脚可以依次输入以下命令可以操作io2输出高低电平
```Shell
set {uint32_t } 0x60004024 |= (1 << 2)
set {uint32_t } 0x60004008 |= (1 << 2)
set {uint32_t } 0x6000400c |= (1 << 2)
```


## 使用arduino ide调试esp32c3和esp32s3

#### 合宙esp32c3 arduino ide 菜单配置

由于在 [arduino-esp32 boards.txt](https://github.com/espressif/arduino-esp32/blob/master/boards.txt)中未配置
合宙C3的Jtag选项。 导致无法指定调试使用的JTAG,为了debug不要选择`AirM2M_CORE_ESP32C3`而使用通用的`ESP32C3 Dev Module`。

合宙C3采用的是DIO方式外接flash，这里需要将`Flash Mode` 设为`DIO`。

然后选择JTAG Adaptor为`Integrated USB JTAG`表示使用C3内嵌的JTAG进行debug。

#### esp32s3 arduino ide 菜单配置

设置USB模式为JTAG, JTAG Adaptor选择`Integrated USB JTAG` 便可用内置jtag 调试

![s3_debug_config.png](s3_debug_config.png)

#### 开始调试

s3需要将usb jtag口连接至pc,而合宙esp32c3只有一个usb口便是jtag。

点击对钩进行`验证`(也就是编译，直接debug不会触发编译而是报文件找不到。)

点击debug

![ard_debug.png](ard_debug.png)

即可调试，注意看调试控制台的打印。

#### 外设寄存器

查看和读取寄存器，可以在左下角找到`CORTEX PERIPHERAL`

![debug_reg.png](debug_reg.png)

可以复制各种外设寄存器的值，也可以使用编辑按钮设置。

具体外设可以参考idf
指南中关联的[硬件参考](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32s3/hw-reference/index.html)里面的 
**技术参考手册 (PDF)**

