# 通用配置
由platformio-esp32支持的配置。

## 覆盖开发板配置
部分配置以`board_`前缀开始,用于覆盖单独开发板的配置。


参考具体开发板配置:
<tabs>
    <tab title="Windows">
        <code-block lang="plain text">C:\Users\用户名\.platformio\espressif32\boards</code-block>
    </tab>
    <tab title="Linux">
        <code-block lang="plain text">/home/用户名/.platformio/packages/espressif32/boards
          </code-block>
    </tab>
</tabs>

下方是 `esp32dev` (Espressif ESP32 Dev Module)配置json文件。
如果需要在platformio.ini中覆盖`build`下方的内容，如需要修改flash模式至qio。
在对应[env:xxx]选项中配置该项:`board_build.flash_mode = qio`
同理`upload`节点下方同理加上board_前缀:`board_upload.flash_size = 8MB`

```JSON
{
  "build": {
    "arduino":{
      "ldscript": "esp32_out.ld"
    },
    "core": "esp32",
    "extra_flags": "-DARDUINO_ESP32_DEV",
    "f_cpu": "240000000L",
    "f_flash": "40000000L",
    "flash_mode": "dio",
    "mcu": "esp32",
    "variant": "esp32"
  },
  "connectivity": [
    "wifi",
    "bluetooth",
    "ethernet",
    "can"
  ],
  "debug": {
    "openocd_board": "esp-wroom-32.cfg"
  },
  "frameworks": [
    "arduino",
    "espidf"
  ],
  "name": "Espressif ESP32 Dev Module",
  "upload": {
    "flash_size": "4MB",
    "maximum_ram_size": 327680,
    "maximum_size": 4194304,
    "require_upload_port": true,
    "speed": 460800
  },
  "url": "https://en.wikipedia.org/wiki/ESP32",
  "vendor": "Espressif"
}

```