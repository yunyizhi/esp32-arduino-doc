# Arduino 数字IO

## digitalRead()

用于读取高低电平。

```C++
int digitalRead(uint8_t pin);
```

* pin: 要读取的引脚编号。

### 返回

HIGH或LOW。

#### 注意事项和警告

悬空引脚读取可能是高或者低。

## digitalWrite()

用于输出高低电平 ，例如点亮板载LED灯。
```C++
void digitalWrite(uint8_t pin, uint8_t val)
```

* pin: 要输出的的引脚编号,HIGH或LOW。
* val: HIGH或LOW。


## pinMode()

设置引脚模式

```
void pinMode(uint8_t pin, uint8_t mode);
```

* pin: 要读取的引脚编号。
* mode: 目标模式可选值如下:

| 定义                | 值    | 说明                                                                                                                                           |
|-------------------|------|----------------------------------------------------------------------------------------------------------------------------------------------|
| INPUT             | 0x01 |                                                                                                                                              |
| -                 | 0x02 | Changed OUTPUT from 0x02 to behave the same as Arduino pinMode(pin,OUTPUT) where you can read the state of pin even when it is set as OUTPUT |
| OUTPUT            | 0x03 |                                                                                                                                              |
| PULLUP            | 0x04 |                                                                                                                                              |
| INPUT_PULLUP      | 0x05 |                                                                                                                                              |
| PULLDOWN          | 0x08 |                                                                                                                                              |
| INPUT_PULLDOWN    | 0x09 |                                                                                                                                              |
| OPEN_DRAIN        | 0x10 |                                                                                                                                              |
| OUTPUT_OPEN_DRAIN | 0x13 |                                                                                                                                              |
| ANALOG            | 0xC0 |                                                                                                                                              |