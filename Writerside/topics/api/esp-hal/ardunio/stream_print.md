# Stream 和 Print

这两个类是很多字符字节输入输出操作的父类。
比如串口 I2C TCP UDP 文件的系统相关等。

### Print
Print 含大量不同要求输出的 函数
包括
* write()
* print()
* printf()
* println()
和在arduino 原有库上的拓展，print相关含有大量重载和类似名称的函数 ，满足各种打印场景，帮助将原本的内容转为字符串输出。
write 相关是字节写出。

除了常见基础类型和字符串以及封装的String类型，其他拓展类想要被直接作为参数打印可以继承 `Printable`类。

### Stream
Stream 继承了Print 主要增加了读出数据的和相关处理的函数，可以读出数据并转化为相应类型，进行查找等相关操作。

需要子类实现这些虚函数
* int available();
* int read()
* int peek()

提供了同一的读出和处理相关函数如：

* size_t readBytesUntil(char terminator, uint8_t *buffer, size_t length)
* String readString()
* String readStringUntil(char terminator)
* find

具体参考具体版本源码 大体上会满足 arduino通用的 [Stream](https://www.arduino.cc/reference/en/language/functions/communication/serial/)

在使用串口 TCP UDP通讯 文件对象时 均可使用Stream相关API进行字符或者字节操作。