# Arduino ADC


## analogRead()
此函数用于获取给定引脚/ADC通道的ADC原始值。
```C++
uint16_t analogRead(uint8_t pin);
```
此函数将返回模拟原始值（未校准）。
* `pin` GPIO模拟引脚

##  analogReadResolution()
此函数用于设置返回值的分辨率。默认值为 12 位（范围为 0 到 4095） 对于除 ESP32S3 之外的所有芯片，默认为 13 位（范围从 0 到 8191）。 
当设置不同的分辨率时，读取的值将被移动以匹配给定的分辨率。

## analogReadMillivolts() (非arduino框架通用)

此函数用于获取给定引脚/ADC通道的ADC原始值，并将其转换为以毫伏为单位的校准结果。
```C++
uint32_t analogReadMilliVolts(uint8_t pin);
```
* `pin` GPIO模拟引脚
## analogSetAttenuation
此功能用于设置所有通道的衰减。

输入电压在输入到ADC之前可以衰减。 有 4 种可用的衰减选项，衰减越高，可测量的输入电压就越高。
每个芯片的可测量输入电压都不同，有关详细信息，请参见下表。
```C++
void analogSetAttenuation(adc_attenuation_t attenuation);
```
* attenuation 设置衰减。