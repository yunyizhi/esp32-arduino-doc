# rgb灯
如esp32s3 devkit-c开发板会带一个rgb灯。

我们可以使用`esp32-hal-rgb-led.h`提供的`neopixelWrite`函数对该rgb灯引脚写入颜色。

该函数会编码一段波形配置，使用esp32的rmt外设发送对应波形。

比如 若rgb灯对应48号引脚，第一个参数为48 后三个参数是rgb值。分别对应红绿蓝，uint8 取0~255。
如红色`neopixelWrite(48, 255, 0, 0);`

```C++
#include <Arduino.h>

void setup()
{
}

void loop()
{
    neopixelWrite(48, 255, 0, 0);
    delay(500);
    neopixelWrite(48, 255, 165, 0);
    delay(500);
    neopixelWrite(48, 255, 255, 0);
    delay(500);
    neopixelWrite(48, 0, 255, 0);
    delay(500);
    neopixelWrite(48, 0, 127, 255);
    delay(500);
    neopixelWrite(48, 0, 0, 255);
    delay(500);
    neopixelWrite(48, 139, 0, 255);
    delay(500);
}

```

相关的宏，如果开发板类型选择正确。在对应开发板的 pins_arduino.h 中含有 `RGB_BUILTIN` 和`RGB_BRIGHTNESS`。

可以使用RGB_BUILTIN代替具体引脚号，RGB_BRIGHTNESS 用于亮度。
在RGB_BUILTIN 等于同时当使用`digitalWrite`写的引脚时， 将会使用RGB_BRIGHTNESS或者0作为rgb值调用`neopixelWrite`。

比如我选的开发板类型是`esp32-s3-devkitc-1`，同时我的esp32s3开发板rgb灯也是48，则同样可以使用以下代码点亮rgb以白光闪烁。
```C++
#include <Arduino.h>

void setup()
{
}

void loop()
{
    digitalWrite(RGB_BUILTIN, HIGH);
    delay(1000);
    digitalWrite(RGB_BUILTIN, LOW);
    delay(1000);
}

```
配置如下:
```Ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
```