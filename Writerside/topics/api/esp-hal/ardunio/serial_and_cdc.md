# Serial 和Cdc

在arduino下通常有个全局变量 `Serial`。
在arduino-esp32中该变量大多情况下是是硬件串口0的实例。
但在开启CDC情况下，可能会是Jtag CDC或者OTG CDC虚拟串口的实例。
CDC并非真实串口，可用于与PC或其他支持USB功能的上位机通讯模拟串口功能，可用于烧录固件。

HardwareSerial 继承了 [Stream](stream_print.md) 相关输入输出的封装不再累述。大部分内容与arduino通用的api相同。

### 串口数量

`SOC_UART_NUM`这个宏表示当前芯片的硬件串口数量。

除P4外，其他esp32系列的芯片，最多只有3个硬件串口。在未启用`ARDUINO_USB_CDC_ON_BOOT` 情况下，分别对应`Serial` `Serial1`
`Serial2`,
在启用`ARDUINO_USB_CDC_ON_BOOT`之后对应`Serial0` `Serial1` `Serial2`。

使用方法
```c++

Serial1.begin(115200);
Serial1.begin(115200,SERIAL_8N1, RX_PIN, TX_PIN); // 并指定引脚

Serial2.begin(115200);
Serial2.begin(115200,SERIAL_8N1, RX_PIN, TX_PIN);  // 并指定引脚
```

| 芯片       | 硬件串口数量 |
|----------|--------|
| esp32    | 3      |
| esp32s2  | 2      |
| esp32s3  | 3      |
| esp32c2  | 2      |
| esp32c3  | 2      |
| esp32c6  | 3      |
| esp32c61 | 3      |
| esp32h2  | 2      |
| esp32p4  | 6      |

### 串口0

默认情况下 Serial是串口0，串口0在下载模式会被使用，在rom打印时也会使用串口0，
而我们的app在启动后也可以重新设置串口0的引脚，但不会影响app启动之前的设置（也就是说串口0在下载模式和打印启动日志时相当于有固定引脚）。

当启用`ARDUINO_USB_CDC_ON_BOOT`选项之后， Serial身份会变成 `HWCDC` 或者 `USBCDC`。
我们仍可以使用`Serial`打印日志,这个时候需要使用esp芯片的usb连接电脑而不是使用串口芯片引出的usb连接电脑。
同时这个时候可以把串口0释放出来与其他设备通讯，这里我们可以使用`Serial0`这个全局变量。

### HWCDC

esp32c3 esp32c6 esp32s3 等以及一些新出的esp32型号 含内置jtag并连接到USB，除调试外还可以作为CDC-ACM，

在arduino中 `ARDUINO_USB_MODE` 选项为 1时 esp32的usb口USB表现为 Hardware CDC and JTag。

此时启用 `ARDUINO_USB_CDC_ON_BOOT`选项之后 Serial身份会变成 `HWCDC`。

### USBCDC

esp32s2 esp32s3 等型号支持OTG外设，CONFIG_TINYUSB_CDC_ENABLED 会开启。

在arduino中 ARDUINO_USB_MODE 选项为 0时 esp32的usb口0则是USB-OTG。

此时启用 `ARDUINO_USB_CDC_ON_BOOT`选项之后 Serial身份会变成 `USBCDC`。

### 指定硬件串口引脚

硬件串口在未配置相关宏定义的时候在arduino下有了默认引脚，有时候希望修改引脚，可以采用以下方法:

#### 使用宏定义

通过platformio的build_flags 指定这些宏的值:SOC_RX0 SOC_TX0 RX1 TX1 RX2 TX2
* SOC_RX0
RX0 引脚 ，默认值随芯片变化,被Serial0使用
* SOC_TX0
TX0 引脚 ，默认值随芯片变化,被Serial0使用

* RX1
RX1 引脚 ，默认值随芯片变化,被Serial1使用。
* TX1
TX1 引脚 ，默认值随芯片变化,被Serial1使用

* RX2
RX2 引脚 ，默认值随芯片变化,被Serial2使用
* TX2
TX2 引脚 ，默认值随芯片变化,被Serial2使用

#### 使用begin参数

比如esp32s2 mini 的串口0没有引出，可以在启动后指定 串口引脚可以按以下方式。
` Serial.begin(115200,SERIAL_8N1, RX, TX);`

#### 使用setPins

`bool setPins(int8_t rxPin, int8_t txPin, int8_t ctsPin = -1, int8_t rtsPin = -1);`
可以在begin前后调用

### 串口事件

注册事件之后，会创建task接受串口相关队列的消息，如果是串口数据相关事件，会通过该线程回调
注册的事件。

`HardwareSerial::onReceive`

```C++
void serialReceiveTask() {

    int i = Serial.read();
    if (i == -1) {
        return;
    }
    // XXXXX
}

void setup() {
 Serial.onReceive(serialReceiveTask);
}

```
注册串口错误事件
`HardwareSerial::onReceiveError`
用法同上

#### 串口事件配置

##### ARDUINO_SERIAL_EVENT_TASK_STACK_SIZE
串口事件线程栈大小，默认值2048

##### ARDUINO_SERIAL_EVENT_TASK_PRIORITY
串口事件线程优先级, 默认configMAX_PRIORITIES-1

##### ARDUINO_SERIAL_EVENT_TASK_RUNNING_CORE
串口事件线程在哪个核