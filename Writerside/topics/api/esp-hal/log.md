# 日志
为esp32编写代码时，使用日志接口，可以帮助把文件的文件名,行号出来。且对使用usb监控的场景和串口场景可以通用，
方便通过编译控制日志级别，在调试和运行时按需控制日志量。

## hal-log
在arduino-esp32库中使用的是`esp32-hal-log.h`中提供的日志宏函数。

`log_v` `log_d` `log_i` `log_w` `log_e` `log_n`
>参数为(format, ...)
使用示例: `log_i("flash size:%u",ESP.getFlashChipSize());`

需要参考后文[日志配置](log_config.md)打开相关配置。

```C++
#include <Arduino.h>

void setup() {
}

void loop() {
    log_v("log_v");
    log_d("log_d");
    log_i("log_i");
    log_w("log_w");
    log_e("log_e");
    log_n("log_n");
    delay(3 * 1000);
}
```

这里把日志级别开到最高,配置如下:
```Ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
monitor_filters =
    direct
    esp32_exception_decoder
build_flags =
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_VERBOSE
    -D CONFIG_ARDUHAL_LOG_COLORS=1
```

效果如下:
![log_x](log_x.png)

可以看到log_n打印的颜色和级别是log error.但log_n在未配置日志级别时，依然会打印。

## ESP LOG
idf中的日志接口，在`esp_log.h`中提供.一般来说，arduino-esp32库不会使用该接口，但依然可以在arduino-esp32调用，如果需要移植idf例程，或者使用idf组件库，
同样可以参考后文[日志配置](log_config.md)，对ESP LOG的日志级别进行控制。

>使用方式类似 hal-log,需要传入一个TAG<br>
>`ESP_LOGI(TAG, "flash size:%u", ESP.getFlashChipSize());`

```C++
#include <Arduino.h>

static const char *TAG = "Test SDK Config";

void setup() {
}

void loop() {
    ESP_LOGV(TAG, "ESP_LOGV");
    ESP_LOGD(TAG, "ESP_LOGV");
    ESP_LOGI(TAG, "ESP_LOGI");
    ESP_LOGW(TAG, "ESP_LOGW");
    ESP_LOGE(TAG, "ESP_LOGE");
    delay(3 * 1000);
}
```

配置如下:

```Ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
monitor_filters =
    direct
    esp32_exception_decoder
build_flags =
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_VERBOSE
    -D CONFIG_ARDUHAL_LOG_COLORS=1
```
打印效果类似，多了新增的Target字段

![ESP_LOGX](esp_log_x.png)
