# 配置

这里使用platformio.ini进行配置，参考
<a href="https://docs.platformio.org/en/latest/projectconf/sections/env/index.html">[env:xx]选项配置</a>

以下是一个esp32s3的配置示例。
```Ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
monitor_filters =
    direct
build_flags =
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_INFO
    -D CONFIG_ARDUHAL_LOG_COLORS=1
```
