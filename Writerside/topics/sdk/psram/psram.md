# psram

## BOARD_HAS_PSRAM

这里仅仅定义`-D BOARD_HAS_PSRAM`即可开启相关功能。但很多情况下仅仅开启psram不能正确初始化它。
我们需要正确指定psram模式属于哪个组合才能加载到对应文件夹下载的sdk配置文件。

## board_build.arduino.memory_type

在platformio中从下述目录找到每种芯片的不同psram和flash模式组合的sdk配置文件，用于正确初始化psram和flash。
<tabs>
    <tab title="Windows">
        <code-block lang="plain text">C:\Users\用户名\.platformio\packages\framework-arduinoespressif32\tools\sdk</code-block>
    </tab>
    <tab title="Linux">
        <code-block lang="plain text">/home/用户名/.platformio/packages/framework-arduinoespressif32/tools/sdk/
          </code-block>
    </tab>
</tabs>
在该目录可以找到具体芯片类型的，文件夹。选择一个芯片类型的文件夹如:esp32s3
则会包含如下的文件夹:
 <code-block lang="plain text">
lustre@VivoBook-linux:~/.platformio/packages/framework-arduinoespressif32/tools/sdk/esp32s3$ ls
bin      dio_qspi  ld   opi_opi   qio_opi   sdkconfig
dio_opi  include   lib  opi_qspi  qio_qspi
</code-block>

例如:`dio_qspi` `opi_opi` `qio_opi` `dio_opi` `opi_qspi` `qio_qspi`
都是`board_build.arduino.memory_type`的参数
如果不清楚芯片或模组的flash以及psram模式，参考乐鑫官方的数据手册。

例如:esp32-wrover-cam的flash 模式qio， prsam模式qspi

```Ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
upload_speed = 921600
board_build.arduino.memory_type = qio_qspi
build_flags =
    -D BOARD_HAS_PSRAM
```
> 需要完整编译并烧录和监控一次，才会触发下载相关开发板的各种依赖包。
>
> 可使用命令`pio run -t upload -t monitor`或者在任务树上点击Upload And Monitor