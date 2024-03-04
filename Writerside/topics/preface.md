# 前言

<!--Writerside adds this topic when you create a new documentation project.
You can use it as a sandbox to play with Writerside features, and remove it from the TOC when you don't need it anymore.
If you want to re-add it for your experiments, click + to create a new topic, choose Topic from Template, and select the 
"Starter" template.-->
>声明:
**这不是一份乐鑫官方文档，其中内容不代表官方立场，错误部分和主观部分需要甄别。**

开始于ESP32 Arduino 2.0.14，可能存在已过时内容。
## API分类

<a href="https://github.com/espressif/arduino-esp32">ESP32-Arduino</a>是基于
<a href="https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/get-started/">ESP-IDF</a>
对arduino适配而来。
一份ESP32-Arduino 环境下，含有以下api:
* esp-hal: 在源码cores/esp32目录下。包含兼容arduino通用的api，以及对arduino框架通用api的拓展的部分。
* Libraries: 在源码libraries目录下,ESP32在Arduino框架下特有的库。这一部分是类似于其他arduino库，以cpp编写，并附了例程。
* esp-idf api: 在ESP32-Arduino中的idf已经编译完成，提供了`.a`文件和头文件。当需要使用idf相关api，如需要多核执行任务这个时候可以调用idf相关api。

> 在ESP32-Arduino中的idf已编译，虽然大大缩短arduino下编译时间，但有时需要查看源码，可以去<a href="https://github.com/espressif/esp-idf">ESP-IDF</a>
> 项目下查看源码。
>
>需要注意<a href="https://github.com/espressif/arduino-esp32/releases">ESP32-Arduino发型版本</a>
> 号和idf版本号的关系。

## SDK配置

关于SDK配置，本文主要以*platformio*环境下展开。

在esp-idf项目中通常以`menuconfig`功能修改配置项，单项目会有 `sdkconfig`。在Arduino IDE中，单项目的配置在菜单中，不会随源码提交。
而且配置项有限，有时需要去安装ESP32-Arduino 环境文件夹中修改`sdkconfig`相关文件。

使用platformio集成ESP32-Arduino项目，可对配置进行单项目的管理，包括库和SDK配置以及其他编译预设。同时可以根据自己喜好选择ide:
> vscode clion codeblocks eclipse emacs netbeans qtcreator sublimetext vim visualstudio

<seealso>
    <category ref="eaa">
        <a href="https://docs.espressif.com/projects/arduino-esp32/en/latest/">ESP32 Arduino Core’s documentation</a>
        <a href="https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/get-started/">ESP-IDF编程指南</a>
    </category>
</seealso>
