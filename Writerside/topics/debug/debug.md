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
    delay(3000); // jtag 可能沒有立即连上日志不宜打印太早
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

## 使用arduino ide调试esp32c3和esp32s3

* 合宙esp32c3

由于在 [arduino-esp32 boards.txt](https://github.com/espressif/arduino-esp32/blob/master/boards.txt)中未配置
合宙C3的Jtag选项。 导致无法指定调试使用的JTAG,为了debug不要选择`AirM2M_CORE_ESP32C3`而使用通用的`ESP32C3 Dev Module`。

合宙C3采用的是DIO方式外接flash，这里需要将`Flash Mode` 设为`DIO`。

然后选择JTAG Adaptor为`Integrated USB JTAG`表示使用C3内嵌的JTAG进行debug。

点击对钩进行`验证`(也就是编译)

点击debug

![ard_debug.png](ard_debug.png)

即可调试，注意看调试控制台的打印。

查看和读取寄存器，可以在左下角找到`CORTEX PERIPHERAL`

![ard_debug_c3_reg.png](ard_debug_c3_reg.png)

可以复制各种外设寄存器的值，也可以使用编辑按钮设置。



