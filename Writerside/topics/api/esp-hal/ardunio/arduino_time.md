# arduino 时间

## 时间
主要是兼容arduino api，其使用esp_timer_get_time()
* 微秒数
  unsigned long micros();
* 毫秒数
  unsigned long millis();
## arduino 延时
* void delay(uint32_t);
  使用vTaskDelay进行延时，毫秒延时

* void delayMicroseconds(uint32_t us);
  使用NOP()延时(也就是执行NOP指令)