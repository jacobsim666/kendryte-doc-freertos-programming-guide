# 串行外设接口 (SPI)

## 概述

SPI 是一种高速的，全双工，同步的通信总线。

## 功能描述

SPI 模块具有以下功能：

- 独立的 SPI 设备封装外设相关参数
- 自动处理多设备总线争用
- 支持标准、双线、四线、八线模式
- 支持先写后读和全双工读写
- 支持发送一串相同的数据帧，常用于清屏、填充存储扇区等场景

## API 参考

对应的头文件 `devices.h`

为用户提供以下接口：

- [spi\_get\_device](#spigetdevice)
- [spi\_dev\_config\_non\_standard](#spidevconfignonstandard)
- [spi\_dev\_set\_clock\_rate](#spidevsetclockrate)
- [spi\_dev\_transfer\_full\_duplex](#spidevtransferfullduplex)
- [spi\_dev\_transfer\_sequential](#spidevtransfersequential)
- [spi\_dev\_fill](#spidevfill)

### spi\_get\_device

#### 描述

注册并打开一个 SPI 设备。

#### 函数原型

```c
handle_t spi_get_device(handle_t file, const char *name, spi_mode mode, spi_frame_format frame_format, uint32_t chip_select_mask, uint32_t data_bit_length);
```

#### 参数

| 参数名称            |   描述             |  输入输出  |
| ------------------ | ------------------ | --------- |
| file               | SPI 控制器句柄      | 输入       |
| name               | 指定访问该设备的路径 | 输入       |
| mode               | SPI 模式            | 输入       |
| frame\_format      | 帧格式              | 输入       |
| chip\_select\_mask | 片选掩码            | 输入       |
| data\_bit\_length  | 数据位长度          | 输入       |

#### 返回值

SPI 设备句柄。

### spi\_dev\_config\_non\_standard

#### 描述

配置 SPI 设备的非标准帧格式参数。

#### 函数原型

```c
void spi_dev_config_non_standard(handle_t file, uint32_t instruction_length, uint32_t address_length, uint32_t wait_cycles, spi_inst_addr_trans_mode_t trans_mode);
```

#### 参数

| 参数名称             |   描述             |  输入输出  |
| ------------------- | ------------------ | --------- |
| file                | SPI 设备句柄        | 输入      |
| instruction\_length | 指令长度            | 输入      |
| address\_length     | 地址长度            | 输入      |
| wait\_cycles        | 等待周期数          | 输入      |
| trans\_mode         | 指令和地址的传输模式 | 输入      |

#### 返回值

无。

### spi\_dev\_set\_clock\_rate

#### 描述

配置 SPI 设备的时钟速率。

#### 函数原型

```c
double spi_dev_set_clock_rate(handle_t file, double clock_rate);
```

#### 参数

| 参数名称          |   描述        |  输入输出  |
| ---------------- | ------------- | --------- |
| file             | SPI 设备句柄   | 输入       |
| clock\_rate      | 期望的时钟速率 | 输入       |

#### 返回值

设置后的实际速率。

### spi\_dev\_transfer\_full\_duplex

#### 描述

对 SPI 设备进行全双工传输。

**注：** 仅支持标准帧格式。

#### 函数原型

```c
int spi_dev_transfer_full_duplex(handle_t file, const uint8_t *write_buffer, size_t write_len, uint8_t *read_buffer, size_t read_len);
```

#### 参数

| 参数名称          |   描述         |  输入输出  |
| ---------------- | -------------- | --------- |
| file             | SPI 设备句柄    | 输入      |
| write\_buffer    | 源缓冲区        | 输入      |
| write\_len       | 要写入的字节数   | 输入      |
| read\_buffer     | 目标缓冲区       | 输出      |
| read\_len        | 最多读取的字节数 | 输入      |

#### 返回值

实际读取的字节数。

### spi\_dev\_transfer\_sequential

#### 描述

对 SPI 设备进行先写后读。

**注：** 仅支持标准帧格式。

#### 函数原型

```c
int spi_dev_transfer_sequential(handle_t file, const uint8_t *write_buffer, size_t write_len, uint8_t *read_buffer, size_t read_len);
```

#### 参数

| 参数名称          |   描述         |  输入输出  |
| ---------------- | -------------- | --------- |
| file             | SPI 设备句柄    | 输入      |
| write\_buffer    | 源缓冲区        | 输入      |
| write\_len       | 要写入的字节数   | 输入      |
| read\_buffer     | 目标缓冲区       | 输出      |
| read\_len        | 最多读取的字节数 | 输入      |

#### 返回值

实际读取的字节数。

### spi\_dev\_fill

#### 描述

对 SPI 设备填充一串相同的帧。

**注：** 仅支持标准帧格式。

#### 函数原型

```c
void spi_dev_fill(handle_t file, uint32_t instruction, uint32_t address, uint32_t value, size_t count);
```

#### 参数

| 参数名称          |   描述                 |  输入输出  |
| ---------------- | ---------------------- | --------- |
| file             | SPI 设备句柄            | 输入      |
| instruction      | 指令（标准帧格式下忽略） | 输入      |
| address          | 地址（标准帧格式下忽略） | 输入      |
| value            | 帧数据                 | 输出      |
| count            | 帧数                   | 输入      |

#### 返回值

无。

### 举例

```c
handle_t spi = io_open("/dev/spi0");
/* dev0 工作在 MODE0 模式 标准 SPI 模式 单次发送 8 位数据 使用片选 0 */
handle_t dev0 = spi_get_device(spi, "/dev/spi0/dev0", SPI_MODE_0, SPI_FF_STANDARD, 0b1, 8);
uint8_t data_buf[] = { 0x06, 0x01, 0x02, 0x04, 0, 1, 2, 3 };
/* 发送指令 0x06 向地址 0x010204 发送 0，1，2，3 四个字节数据 */
io_write(dev0, data_buf, sizeof(data_buf));
/* 发送指令 0x06 地址 0x010204 接收四个字节的数据 */
spi_dev_transfer_sequential(dev0, data_buf, 4, data_buf, 4);
```

## 数据类型

相关数据类型、数据结构定义如下：

- [spi\_mode\_t](#spimodet)：SPI 模式。
- [spi\_frame\_format\_t](#spiframeformatt)：SPI 帧格式。
- [spi\_inst\_addr\_trans\_mode\_t](#spiinstaddrtransmodet)：SPI 指令和地址的传输模式。

### spi\_mode\_t

#### 描述

SPI 模式。

#### 定义

```c
typedef enum _spi_mode
{
    SPI_MODE_0,
    SPI_MODE_1,
    SPI_MODE_2,
    SPI_MODE_3,
} spi_mode_t;
```

#### 成员

| 成员名称       | 描述        |
| ------------- | ----------- |
| SPI\_MODE\_0  | SPI 模式 0  |
| SPI\_MODE\_1  | SPI 模式 1  |
| SPI\_MODE\_2  | SPI 模式 2  |
| SPI\_MODE\_3  | SPI 模式 3  |

### spi\_frame\_format\_t

#### 描述

SPI 帧格式。

#### 定义

```c
typedef enum _spi_frame_format
{
    SPI_FF_STANDARD,
    SPI_FF_DUAL,
    SPI_FF_QUAD,
    SPI_FF_OCTAL
} spi_frame_format_t;
```

#### 成员

| 成员名称            | 描述                      |
| ------------------ | ------------------------- |
| SPI\_FF\_STANDARD  | 标准                      |
| SPI\_FF\_DUAL      | 双线                      |
| SPI\_FF\_QUAD      | 四线                      |
| SPI\_FF\_OCTAL     | 八线（`/dev/spi3` 不支持） |

### spi\_inst\_addr\_trans\_mode\_t

#### 描述

SPI 指令和地址的传输模式。

#### 定义

```c
typedef enum _spi_inst_addr_trans_mode
{
    SPI_AITM_STANDARD,
    SPI_AITM_ADDR_STANDARD,
    SPI_AITM_AS_FRAME_FORMAT
} spi_inst_addr_trans_mode_t;
```

#### 成员

| 成员名称                      | 描述               |
| ---------------------------- | ------------------ |
| SPI\_AITM\_STANDARD          | 均使用标准帧格式     |
| SPI\_AITM\_ADDR\_STANDARD    | 指令使用配置的值，地址使用标准帧格式 |
| SPI\_AITM\_AS\_FRAME\_FORMAT | 均使用配置的值     |