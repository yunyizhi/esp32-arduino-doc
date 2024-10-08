# 移植idf项目 

arduino-esp32 本身可以算一个idf项目， 依然从app_main启动。

其中包含了ESP-IDF 项目components下的部分源码已经编译得到的静态库。

>由于部分内容已经编译完成，一些sdkconfig在这次编译时已经生效，对应.a文件已经生成。在编译arduino项目编译过程，不含idf源码编译。
> 所以在编译arduino项目修改针对已编译内容的sdkconfig项是没有作用的，
> 如果希望同时修改编译IDF项目的配置也想使用arduino的库，那也可以把arduino作为idf项目组件。

## 开发框架的差异性

正常情况下，在arduino-esp32项目中调用idf的api没有问题。简单用法可以先尝试片段拷贝。

整个项目移植 可能面临 
* cpp与c调用问题
* idf组件尤其是其cmake配置问题
* idf日志(基本上兼容，只需要简单配置)
* Menuconfig 配置移植问题

>由于arduino ide限制，修改一些编译参数可能需要到安装arduino-esp32目录或者arduino本身目录进行全局操作。
这里使用platformio下arduino框架来移植esp-idf项目.

>另外arduino是基于esp-idf，所以会落后于esp-idf发布，一些最新的特性可能不能移植，比如对新的芯片的支持。

## 移植方法

### 安装idf

这里可以不用idf开发建议还是下载一个idf环境，可以不配置编辑器 ide等 但idf命令行工具最好下载一个，用于处理组件的拷贝和Menuconfig问题。

在windows下，最简单的安装idf方式就是下载一个离线版idf安装包[Offline Installer](https://dl.espressif.com/dl/esp-idf/?idf=4.4)

![idf_offline.png](idf_offline.png)

>当然有时候需要考虑版本对应关系，可以看arduino-esp32的[release说明](https://github.com/espressif/arduino-esp32/releases)，安装合适的版本。

这里是国内下载站，正常网络即可下载，随后一键安装到无空格路径的下。

安装时默认已经勾选了创建桌面的命令行工具的快捷方式

![idf_poweshell.png](idf_poweshell.png)

我们可以双击打开对应的快捷方式，会自动配置idf环境，即可通过命令行编译烧录等一系列操作。

日志样例如下:

```text
Windows PowerShell
版权所有 (C) Microsoft Corporation。保留所有权利。

尝试新的跨平台 PowerShell https://aka.ms/pscore6

Setting PYTHONNOUSERSITE, was not set
Using Python in D:\Espressif\python_env\idf5.3_py3.11_env\Scripts
Python 3.11.2
Using Git in D:\Espressif\tools\idf-git\2.44.0\cmd
git version 2.44.0.windows.1
Setting IDF_PATH: D:\Espressif\frameworks\esp-idf-v5.3
Checking Python compatibility
Adding ESP-IDF tools to PATH...
WARNING: Error while accessing the ESP-IDF version file in the Python environment: [Errno 2] No such file or directory: 'D:\\Espressif\\python_env\\idf5.3_py3.11_env\\idf_version.txt'

Name                           Value
----                           -----
OPENOCD_SCRIPTS                D:\Espressif\tools\openocd-esp32\v0.12.0-esp32-20240318\openocd-esp32\share\openocd\s...
IDF_CCACHE_ENABLE              1
ESP_ROM_ELF_DIR                D:\Espressif\tools\esp-rom-elfs\20240305\
IDF_PYTHON_ENV_PATH            D:\Espressif\python_env\idf5.3_py3.11_env
ESP_IDF_VERSION                5.3
IDF_DEACTIVATE_FILE_PATH       C:\Users\admin\AppData\Local\Temp\tmpke1b1oa9idf_1616

Added to PATH
-------------
D:\Espressif\tools\xtensa-esp-elf-gdb\14.2_20240403\xtensa-esp-elf-gdb\bin
D:\Espressif\tools\riscv32-esp-elf-gdb\14.2_20240403\riscv32-esp-elf-gdb\bin
D:\Espressif\tools\xtensa-esp-elf\esp-13.2.0_20240530\xtensa-esp-elf\bin
D:\Espressif\tools\riscv32-esp-elf\esp-13.2.0_20240530\riscv32-esp-elf\bin
D:\Espressif\tools\esp32ulp-elf\2.38_20240113\esp32ulp-elf\bin
D:\Espressif\tools\cmake\3.24.0\bin
D:\Espressif\tools\openocd-esp32\v0.12.0-esp32-20240318\openocd-esp32\bin
D:\Espressif\tools\ninja\1.11.1\
D:\Espressif\tools\idf-exe\1.0.3\
D:\Espressif\tools\ccache\4.8\ccache-4.8-windows-x86_64
D:\Espressif\tools\dfu-util\0.11\dfu-util-0.11-win64
D:\Espressif\frameworks\esp-idf-v5.3\tools
Checking if Python packages are up to date...
Constraint file: D:\Espressif\espidf.constraints.v5.3.txt
Requirement files:
 - D:\Espressif\frameworks\esp-idf-v5.3\tools\requirements\requirements.core.txt
Python being checked: D:\Espressif\python_env\idf5.3_py3.11_env\Scripts\python.exe
Python requirements are satisfied.

Detected installed tools that are not currently used by active ESP-IDF version.
For removing old versions of idf-driver, idf-python-wheels use command 'python.exe D:\Espressif\frameworks\esp-idf-v5.3\tools\idf_tools.py uninstall'
For free up even more space, remove installation packages of those tools. Use option 'python.exe D:\Espressif\frameworks\esp-idf-v5.3\tools\idf_tools.py uninstall --remove-archives'.


Done! You can now compile ESP-IDF projects.
Go to the project directory and run:
    idf.py build
```



### 配置idf项目 {id="copy_idf_src"}

由于idf项目可能含有组件，同时可能存在特定的Kconfig。 执行一次idf.py set-target会触发下载组件以及将Kconfig项加到当前项目的sdkconfig中去。

我们可以先编译idf项目的源码，让其自动处理这些后，再进行移植。

这里以usb host msc例子为例进行移植，就是使用esp32s2 esp32s3等芯片的OTG外设驱动msc设备（比如U盘）

当前例程在esp-idf安装目录下的examples/peripherals/usb/host/msc 目录，

本地可以进入安装IDF工具目录的`frameworks\esp-idf-v5.X` 下面可以找到examples文件夹.。
如果是源码安装的，则是对应esp-idf源码根目录下可以找到examples

#### 1.使用上一步的命令行工具使用cd命令进入当前目录
```Bash
cd examples/peripherals/usb/host/msc
```

#### 2.设置芯片类型

这里需要设置芯片类型为esp32s2 esp32s3或者esp32p4才能使用当前例程

以esp32s3为例

在当前命令行执行 `idf.py set-target esp32s3`

其实这一步会触发cmake加载，执行成功后，此时组件也下载完成了。

如果想要验证是否正常，可以使用`idf.py flash` 或者  `idf.py flash -p 端口号`直接烧录到 esp32

使用`idf.py monitor`监控 串口打印。也可合并为一句 `idf.py flash monitor`

### 复制源码拷贝组件

#### 1.拷贝源码
新建一个esp32s3的platformio arduino项目，将msc项目的main目录下的源文件msc_example_main.c拷贝到
platformio项目的src下。

#### 2.拷贝组件 {id="copy_comp"}

生成的managed_components下有组件，将组件文件夹`espressif__usb_host_msc`拷贝到项目下的platformio的lib目录下。

该组件正常的源码和include路径基本符合platformio要求的lib结构，但不能直接使用。

#### 3.源码适配修改 {id="src_compat"}

在main.cpp中将idf例程拉起来，由于arduino的app_main这个入口已被占用，需要给原来的idf例程的app_main改名。

比如改成 `void start()` 。

当前需要通过cpp调用c,这里将void start()也提到头文件中，cpp调用c需要使用 extern "C" 包起相关C的声明。

可以头文件中看到一些通常的做法是
```C++
#ifdef __cplusplus
extern "C" {
#endif
 // 此处是c 的声明
#ifdef __cplusplus
}
#endif
```
用于兼容c和cpp调用。

仿造这种写法总体格式如下:

```C++

#ifndef USB_HOST_MSC_MSC_EXAMPLE_IDF_H
#define USB_HOST_MSC_MSC_EXAMPLE_IDF_H

#include <Arduino.h>

#ifdef __cplusplus
extern "C" {
#endif
void start();

#ifdef __cplusplus
}
#endif

#endif // USB_HOST_MSC_MSC_EXAMPLE_IDF_H
```
>关于加入`#include <Arduino.h>` 这一个后续会解释

以当前内容新建一个`msc_example_main.h`然后在main.cpp中调用
```C++
#include <Arduino.h>
#include "msc_example_idf.h"

void setup() {
    start();
}

void loop() {
    delay(10000);
}
```

由于idf项目大多会自己开线程，这里我们可以让loop函数处于delay让出cpu。

#### 4.lib编译参数修改

我们将组件拷到platformio的lib目录下 platformio会将include文件夹自动作为include path，将其他源文件作为源码，

经过测试会忽略名为test的文件夹，但部分idf组件 会以test_app放入一些非组件本身的源码，也会被platformio当成源文件路径。

这里只能手动重新命名或者去掉。

然后需要查看库的CMakeLists.txt文件。回到当前msc 例程。

使用的库
```CMake
set(sources src/msc_scsi_bot.c
            src/diskio_usb.c
            src/msc_host.c
            src/msc_host_vfs.c)

idf_component_register( SRCS ${sources}
                        INCLUDE_DIRS include include/usb # 'include/usb' is here for backwards compatibility
                        PRIV_INCLUDE_DIRS private_include include/esp_private
                        REQUIRES usb fatfs
                        PRIV_REQUIRES heap )
```

发现src下是源文件 include和 include/usb 文件夹是 include 目录 此外还有 private_include include/esp_private

两个私有的include路径。为了不对该组件源码大改，我们在platformio.ini中追加这两个private_include的路径让编译器也可以从其中找头文件。

在platformio.ini中具体env下的build_flags中可以加gcc参数。
```ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
build_flags =
    -I lib/espressif__usb_host_msc/include/esp_private
    -I lib/espressif__usb_host_msc/private_include
```

### IDF日志配置 {id="idf_log"}
IDF使用的日志宏在arduino中可以正常编译，但如果不配置日志可能不会打印和相应在IDF中的日志颜色。

>另外在目前platformio基于arduino-esp32 2.0.1x版本中 使用IDF日志API虽然导入了esp log相关头文件也不能输出日志，
需要加`#include <Arduino.h>`，这也是在上述[第三步](#src_compat)中加入这句的原因。

配置arduino-esp32的日志适用于原有的IDF日志可参考[日志配置](log_config.md)

这里我将日志设置为info级别并打开颜色码(日志颜色码不希望被platformio处理，搭配monitor_filters中的direct便可显色)。
```ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_filters =
    direct
build_flags =
    -I lib/espressif__usb_host_msc/include/esp_private
    -I lib/espressif__usb_host_msc/private_include
    -D CORE_DEBUG_LEVEL=ARDUHAL_LOG_LEVEL_INFO
    -D CONFIG_ARDUHAL_LOG_COLORS=1
```

### MenuConfig 移植
这里引入的组件或者例程有Kconfig情况下，sdkconfig会出现一些新的内容。

比如CONFIG_EXAMPLE_XXXX 等，这些我们一律使用build_flags处理。

将源码中CONFIG_EXAMPLE_XXXX的宏加到build_flags中如
`-D CONFIG_EXAMPLE_XXXX=1`

如果是特殊项目组件里面有大量配置，我们直接去sdkconfig中复制出来并加 -D。

### idf_component_register 的 REQUIRES处理
组件的cmake中idf_component_register 可能含`REQUIRES` 项，我们需要把idf源码中 未打包打包的到arduino的组件拷到lib中，同样对其进行配置。

可能依然存在其他问题。实在无法解决情况下可以选择使用idf下将arduino作为组件引入。
