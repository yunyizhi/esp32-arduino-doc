# SDK配置
在platformio.ini中可以在
<a href="https://docs.platformio.org/en/latest/projectconf/sections/env/options/build/build_flags.html">build_flags</a>
中添加宏，从而修改一些配置。

`` -DA=B``即可定义宏

例如

```ini
[env:esp32cam]
platform = espressif32
framework = arduino
board = esp32cam
monitor_speed = 115200
upload_speed = 921600
build_flags =
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_INFO
    -D CONFIG_ARDUHAL_LOG_COLORS=1
```