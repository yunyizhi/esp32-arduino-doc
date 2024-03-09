# SDK配置
在platformio.ini中可以在
<a href="https://docs.platformio.org/en/latest/projectconf/sections/env/options/build/build_flags.html">build_flags</a>
中添加宏，从而修改一些配置。<br/>
当我们选择了具体开发板时，在`\platforms\espressif32\boards`文件夹下的json中`build`的下一级`extra_flags`会含有预设的宏定义。
如果需要覆盖这个参数则在platformio.ini中对应的选项中通过`board_build.extra_flags`字段修改，建议将原有定义复制出来之后修改值。<br/>
正常情况下建议使用`build_flags`

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