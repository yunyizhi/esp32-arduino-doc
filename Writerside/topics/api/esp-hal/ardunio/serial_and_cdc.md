# Serial 和Cdc

Serial 并不是arduino-esp32中的类名，大部分情况下它的身份是esp32 的HardwareSerial 对应0号串口的实例。

但它在一定情况下会变成内置Jtag或者OTG cdc串口。

`HardwareSerial`则是硬件串口。 每个芯片自己的`SOC_UART_NUM`，除了esp32P4,
其他esp32最多有3个串口，在未启用cdc on boot 情况下，分别对应`Serial` `Serial1` `Serial2`。

HardwareSerial 继承了 [Stream](stream_print.md) 相关输入输出的封装不再累述。

### 串口0
默认情况下 Serial是串口0，串口0在下载模式会被使用，在rom打印时也会使用串口0，
而我们的app在启动后也可以重新设置串口0，但无法影响下载模式。

当启用`ARDUINO_USB_CDC_ON_BOOT`选项之后， Serial身份会变成 `HWCDC` 或者 `USBCDC`。
我们仍可以使用其打印日志。这个时候可以把串口0释放出来与其他设备通讯，这里我们可以使用`Serial0`这个全局变量。


### HWCDC
esp32c3 esp32c6 esp32s3 等以及一些新出的esp32型号 含内置jtag并连接到USB，除调试外还可以作为CDC-ACM，

在arduino中 ARDUINO_USB_MODE 选项为 1时 esp32的usb口USB表现为 Hardware CDC and JTag。

此时启用 `ARDUINO_USB_CDC_ON_BOOT`选项之后  Serial身份会变成 `HWCDC`。

### USBCDC
esp32s2 esp32s3 等型号支持OTG外设，CONFIG_TINYUSB_CDC_ENABLED 会开启。

在arduino中 ARDUINO_USB_MODE 选项为 0时 esp32的usb口0则是USB-OTG。

此时启用 `ARDUINO_USB_CDC_ON_BOOT`选项之后  Serial身份会变成 `USBCDC`。

### 指定硬件串口引脚

硬件串口在未配置相关宏定义的时候在arduino下有了默认引脚，有时候希望修改引脚，可以采用以下方法:

#### 使用宏定义
 通过platformio的build_flags 指定这些宏的值:SOC_RX0 SOC_TX0 RX1 TX1 RX2 TX2
[参考串口配置](serial.md)

#### 使用begin参数
比如esp32s2 mini 的串口0没有引出，可以在启动后指定 串口引脚可以按以下方式。
` Serial.begin(115200,SERIAL_8N1, RX, TX);`

####  使用setPins

`bool setPins(int8_t rxPin, int8_t txPin, int8_t ctsPin = -1, int8_t rtsPin = -1);`
可以在begin前后调用

### 串口事件
注册事件之后，会创建task接受串口相关队列的消息，如果是串口数据相关事件，会通过该线程回调
注册的事件。

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

>`HardwareSerial::onReceiveError` 同理。

相关设置见[串口配置](serial.md)