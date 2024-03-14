# 串口
给串口注册接收事件，会创task用于处理事件回调
## ARDUINO_SERIAL_EVENT_TASK_STACK_SIZE
串口事件线程栈大小，默认值2048

## ARDUINO_SERIAL_EVENT_TASK_PRIORITY
串口事件线程优先级, 默认configMAX_PRIORITIES-1

## ARDUINO_SERIAL_EVENT_TASK_RUNNING_CORE
串口事件线程在哪个核

## 串口数量
SOC_UART_NUM 表示串口数量, Serial1 Serial2只有达到对应串口数量才可使用

## SOC_RX0 
 RX0 引脚 ，默认值随芯片变化,被Serial0使用
## SOC_TX0
TX0 引脚 ，默认值随芯片变化,被Serial0使用

## RX1
RX1 引脚 ，默认值随芯片变化,被Serial1使用。
## TX1
TX1 引脚 ，默认值随芯片变化,被Serial1使用

## RX2
RX2 引脚 ，默认值随芯片变化,被Serial2使用
## TX2
TX2 引脚 ，默认值随芯片变化,被Serial2使用
