# arduino杂项

## ARDUINO_LOOP_STACK_SIZE

loop 函数的栈大小,默认 `8192`。<br/>
下方例子将其设置为10000。
```Ini
[env:esp32-s3-devkitc-loop]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
monitor_filters = direct
build_flags =
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_INFO
    -D CONFIG_ARDUHAL_LOG_COLORS=1
    -D ARDUINO_LOOP_STACK_SIZE=10000
```

## ARDUINO_RUNNING_CORE
arduino 的setup和loop使用哪个核。只有在多核的esp32上有效。<br/>
可选值0,1。

## ARDUINO_EVENT_RUNNING_CORE
arduino 事件使用哪个核，只有在多核的esp32上有效。<br/>
可选值0,1。
