# flash配置

## board_build.f_flash
需要覆盖开发板对应**boards**下json文件中flash频率时，设置该项。<br/>
具体需要参考模组或者合封芯片的数据手册。
如:40000000L

## board_build.flash_mode
需要覆盖开发板对应**boards**下json文件中flash模式时，设置该项。<br/>
具体需要参考模组或者合封芯片的数据手册。

flash模式则是flash线数，如:`dio` `qio` `opi`。<br/>
在设置psram时，需要指定<a href="psram.md#board-build-arduino-memory-type">memory-type</a>以便于加载对应文件夹的sdkconfig。
那项配置的值也由flash模式参与决定。

## board_upload.flash_size
需要覆盖开发板对应**boards**下json文件中flash大小时，设置该项。<br/>
具体需要参考模组或者合封芯片的数据手册。
如:8MB。
'4MB', '8MB', '16MB', '32MB', '64MB', '128MB'
## board_upload.maximum_size
需要开发板对应**boards**下json文件中最大程序大小，设置该项。<br/>
如flash为4M时该值，可以设置`4194304`。