# Arduino variant

如有些开发板上引脚并未标GPIO号而是使用`D1` `D2`这些定义，同时代码如果使用这种方式定义。当我们选择其他开发板时，可能就报未定义。
部分开发板的`D1` `D2`这种常量，就是定义在Arduino variant中具体开发板目录下。其中包含了具体开发板的一些配置。

选择了正确的开发板，之后大部分情况下，我们可以使用 `RGB_BUILTIN`, `LED_BUILTIN`等常量，在一些未定义的场景，比如直接使用I2C,
I2C引脚也会有`SDA` `SCL`默认值，则是 Arduino variant的定义。

除了引脚外，Arduino variant也可能包含一些开发板特有的内容。

在platformio中 boards下具体开发板的json中，在`build`节点下有个 `variant`字段。
对应arduino 下的[variant](https://github.com/espressif/arduino-esp32/tree/master/variants)文件夹,下方是一些具体开发板对应的文件夹。
其中至少含`pins_arduino.h`等文件。


已esp32s3为例,其pins_arduino.h内容如下:

```C++
#ifndef Pins_Arduino_h
#define Pins_Arduino_h

#include <stdint.h>
#include "soc/soc_caps.h"

#define USB_VID 0x303a
#define USB_PID 0x1001

#define EXTERNAL_NUM_INTERRUPTS 46
#define NUM_DIGITAL_PINS        48
#define NUM_ANALOG_INPUTS       20

// Some boards have too low voltage on this pin (board design bug)
// Use different pin with 3V and connect with 48
// and change this setup for the chosen pin (for example 38)
static const uint8_t LED_BUILTIN = SOC_GPIO_PIN_COUNT+48;
#define BUILTIN_LED  LED_BUILTIN // backward compatibility
#define LED_BUILTIN LED_BUILTIN
#define RGB_BUILTIN LED_BUILTIN
#define RGB_BRIGHTNESS 64

#define analogInputToDigitalPin(p)  (((p)<20)?(analogChannelToDigitalPin(p)):-1)
#define digitalPinToInterrupt(p)    (((p)<49)?(p):-1)
#define digitalPinHasPWM(p)         (p < 46)

static const uint8_t TX = 43;
static const uint8_t RX = 44;

static const uint8_t SDA = 8;
static const uint8_t SCL = 9;

static const uint8_t SS    = 10;
static const uint8_t MOSI  = 11;
static const uint8_t MISO  = 13;
static const uint8_t SCK   = 12;

static const uint8_t A0 = 1;
static const uint8_t A1 = 2;
static const uint8_t A2 = 3;
static const uint8_t A3 = 4;
static const uint8_t A4 = 5;
static const uint8_t A5 = 6;
static const uint8_t A6 = 7;
static const uint8_t A7 = 8;
static const uint8_t A8 = 9;
static const uint8_t A9 = 10;
static const uint8_t A10 = 11;
static const uint8_t A11 = 12;
static const uint8_t A12 = 13;
static const uint8_t A13 = 14;
static const uint8_t A14 = 15;
static const uint8_t A15 = 16;
static const uint8_t A16 = 17;
static const uint8_t A17 = 18;
static const uint8_t A18 = 19;
static const uint8_t A19 = 20;

static const uint8_t T1 = 1;
static const uint8_t T2 = 2;
static const uint8_t T3 = 3;
static const uint8_t T4 = 4;
static const uint8_t T5 = 5;
static const uint8_t T6 = 6;
static const uint8_t T7 = 7;
static const uint8_t T8 = 8;
static const uint8_t T9 = 9;
static const uint8_t T10 = 10;
static const uint8_t T11 = 11;
static const uint8_t T12 = 12;
static const uint8_t T13 = 13;
static const uint8_t T14 = 14;

#endif /* Pins_Arduino_h */
```

实际使用过程中I2C和HSPI其实都可以重新映射，而且配置不一定与芯片数据手册初始值一致。