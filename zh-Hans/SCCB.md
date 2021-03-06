# 串行摄像机控制总线 (SCCB)

## 概述

SCCB 是一种串行摄像机控制总线。

## 功能描述

SCCB 模块具有以下功能：

- 独立的 SCCB 设备封装外设相关参数
- 自动处理多设备总线争用

## API 参考

对应的头文件 `devices.h`

为用户提供以下接口：

- [sccb\_get\_device](#sccbgetdevice)
- [sccb\_dev\_read\_byte](#sccbdevreadbyte)
- [sccb\_dev\_write\_byte](#sccbdevwritebyte)

### sccb\_get\_device

#### 描述

注册并打开一个 SCCB 设备。

#### 函数原型

```c
handle_t sccb_get_device(handle_t file, const char *name, size_t slave_address, size_t reg_address_width);
```

#### 参数

| 参数名称            |   描述             |  输入输出  |
| ------------------ | ------------------ | --------- |
| file               | SCCB 控制器句柄      | 输入      |
| name               | 指定访问该设备的路径 | 输入      |
| slave\_address     | 从设备地址          | 输入      |
| reg_address\_width | 寄存器地址宽度      | 输入      |

#### 返回值

SCCB 设备句柄。

### sccb\_dev\_read\_byte

#### 描述

从 SCCB 设备读取一个字节。

#### 函数原型

```c
uint8_t sccb_dev_read_byte(handle_t file, uint16_t reg_address);
```

#### 参数

| 参数名称       |   描述         |  输入输出  |
| ------------- | -------------- | --------- |
| file          | SCCB 设备句柄   | 输入      |
| reg\_address  | 寄存器地址      | 输入      |

#### 返回值

读取的字节。

### sccb\_dev\_write\_byte

#### 描述

向 SCCB 设备写入一个字节。

#### 函数原型

```c
void sccb_dev_write_byte(handle_t file, uint16_t reg_address, uint8_t value);
```

#### 参数

| 参数名称          |   描述         |  输入输出  |
| ---------------- | -------------- | --------- |
| file             | SCCB 设备句柄   | 输入       |
| reg\_address     | 寄存器地址      | 输入       |
| value            | 要写入的字节    | 输入       |

#### 返回值

无。

### 举例

```c
handle_t sccb = io_open("/dev/sccb0");
handle_t dev0 = sccb_get_device(sccb, "/dev/sccb0/dev0", 0x60, 8);

sccb_dev_write_byte(dev0, 0xFF, 0);
uint8_t value = sccb_dev_read_byte(dev0, 0xFF);
```