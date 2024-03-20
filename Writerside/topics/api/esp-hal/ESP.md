# ESP31B-specific APIs
 已存在一个声明为 ESPClass ESP;

按如下方式调用 如:`ESP.restart()`
## 重启
`void restart()`

## 内存(INTERNAL RAM)
* 堆大小总量
`uint32_t getHeapSize()`
* 空闲内存
`uint32_t getFreeHeap()`
* 本次启动空闲内存最低时的值
`uint32_t getMinFreeHeap()`
* 一次性可以申请的最大堆块
`uint32_t getMaxAllocHeap()`

## psram (SPI RAM)

* psram大小总量
  `uint32_t getPsramSize()`
* 空闲内存
  `uint32_t getFreePsram()`
* 本次启动空闲内存最低时的值
  `uint32_t getMinFreePsram()`
* 一次性可以申请的最大块
  `uint32_t getMaxAllocPsram()`
## 芯片
* 芯片修订版本号
  `uint8_t getChipRevision()`
* 芯片型号
  `const char * getChipModel()`
* 芯片核数
  `uint8_t getChipCores()`
* CPU频率(MHz)
  `uint32_t getCpuFreqMHz()`
* CPU指令周期数
  `uint32_t getCycleCount()`
* Sdk版本号
  `const char * getSdkVersion()`
## 深度睡眠
* 深度睡眠     
`void deepSleep(uint32_t time_us)`
 等同于调用 `void esp_deep_sleep(uint64_t time_in_us)`
>调用后设备将在设定的深度休眠时间后自动唤醒，不会优雅地关闭 WiFi、BT 和更高级别的协议连接。

## Flash
* 获取flash芯片大小
    `uint32_t getFlashChipSize();`
* 获取flash芯片速度
    `uint32_t getFlashChipSpeed();`
* 获取flash芯片模式
    FlashMode_t getFlashChipMode();
```C++
typedef enum {
    FM_QIO = 0x00,
    FM_QOUT = 0x01,
    FM_DIO = 0x02,
    FM_DOUT = 0x03,
    FM_FAST_READ = 0x04,
    FM_SLOW_READ = 0x05,
    FM_UNKNOWN = 0xff
} FlashMode_t;
```

## Sketch（当前程序）
* 获取当前Sketch大小
    `uint32_t getSketchSize()`
* 获取Sketch的MD5值
    `String getSketchMD5()`
* 用于获取当前 Sketch 可用的剩余 Flash 存储空间大小
    `uint32_t getFreeSketchSpace()`
