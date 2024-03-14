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
