# 日志配置

## CORE_DEBUG_LEVEL
esp32-hal-log中 日志级别，使用`log_i`,`log_w`,`log_e`等宏输出日志时，
控制对应级别日志是否显示。

| 可选值                       | 值 | 默认值 |
|---------------------------|---|-----|
| ARDUHAL_LOG_LEVEL_NONE    | 0 | 是   |
| ARDUHAL_LOG_LEVEL_ERROR   | 1 |     |
| ARDUHAL_LOG_LEVEL_WARN    | 2 |     |
| ARDUHAL_LOG_LEVEL_INFO    | 3 |     |
| ARDUHAL_LOG_LEVEL_DEBUG   | 4 |     |
| ARDUHAL_LOG_LEVEL_VERBOSE | 5 |     |

默认没有日志，这样在自身代码不使用串口输出的时候不会输出内容，但是也会导致一些问题无法定位。
如:wifi连接失败的原因。
建议使用ARDUHAL_LOG_LEVEL_INFO ,在可以帮助常见问题定位情况下，日志量合适。
`-D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_INFO`或者`-D CORE_DEBUG_LEVEL=3`

## CONFIG_ARDUHAL_LOG_COLORS
日志颜色，使用`log_i`,`log_w`,`log_e`等宏输出日志时，日志的颜色。需要终端支持颜色。
默认未开启颜色，需要开启使用`-D CONFIG_ARDUHAL_LOG_COLORS=1`

效果如下:

<img src="log_color.png" alt=""/>

同时开启platformio的monitor_filters的direct防止颜色字符码被处理
```Ini
monitor_filters =
    direct
```
例如总体配置如下:
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
>在clion中官方插件使用的终端未支持颜色，使用platformio plus插件的串口监视功能，可正常显示颜色。
