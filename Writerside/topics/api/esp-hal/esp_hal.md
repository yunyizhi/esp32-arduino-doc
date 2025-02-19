# ESP HAL

>该文件提供函数，本人另做分类，有些会合并到其他页面，不在该页面体现。

## FreeRTOS
在esp-hal.h中提供rtos api的只是一部分，也只是封装了idf api，也直接可以使用idf中的rtos api。
* void vPortYield(void);
当前线程会让出当前 CPU 的使用权，让优先级更高的任务得到执行。
* void yield(void); 同上

* optimistic_yield(u) 

* 启用0核看门狗
    void enableCore0WDT();
* 禁用0核看门狗
  void disableCore0WDT();
* 启用1核看门狗
  void enableCore1WDT();
* 禁用1核看门狗
  void disableCore1WDT();

* xTaskCreateUniversal
```C++
 BaseType_t xTaskCreateUniversal( TaskFunction_t pxTaskCode,
const char * const pcName,
const uint32_t usStackDepth,
void * const pvParameters,
UBaseType_t uxPriority,
TaskHandle_t * const pxCreatedTask,
const BaseType_t xCoreID );
```
创建一个任务，可以适应arduino下单核或者多核，在`CONFIG_FREERTOS_UNICORE`为1时，
会忽略`xCoreID`，使用`xTaskCreate`否则使用`xTaskCreatePinnedToCore`。

## 寄存器

* ESP_REG
可以读写寄存器。
GPIO状态使用示例:
`ESP_REG(GPIO_IN_REG)`
当然在soc.h也提供类似功能。以下是一个使用寄存器点合宙c3两个板载LED灯的示例:
```C++
#include <Arduino.h>

// 将12 13号引脚放到到同一个MASK里面
#define LEDS (BIT(12) | BIT(13))

void setup() {
    REG_WRITE(GPIO_ENABLE_W1TS_REG, LEDS);
}

void loop() {
    REG_WRITE(GPIO_OUT_W1TC_REG, LEDS);
    log_i("GPIO_IN_REG %s", String(ESP_REG(GPIO_IN_REG), 2).c_str());
    delay(1000);
    ESP_REG(GPIO_OUT_W1TS_REG) |= LEDS;
    log_i("GPIO_IN_REG %s", String(ESP_REG(GPIO_IN_REG), 2).c_str());
    delay(1000);
}
 
```
这里同时提供`ESP_REG`和`REG_WRITE`操作引脚的示例。

由于存在无USB转串口芯片版本，这里打开日志，则不需要关注串口是否是虚拟。
配置如下:
```Ini
[env:esp32c3]
platform = espressif32
board = airm2m_core_esp32c3
framework = arduino
upload_speed = 921600
monitor_filters =
    direct
build_flags =
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_INFO
    -D CONFIG_ARDUHAL_LOG_COLORS=1
```



## arduino loop看门狗
* 启用loop看门狗
void enableLoopWDT();
* 禁用loop看门狗
void disableLoopWDT();
* loop任务喂狗
void feedLoopWDT();