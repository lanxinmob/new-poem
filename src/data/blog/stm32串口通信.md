---
author: 'Lanxinmob'
title: 'stm32串口通信'
postSlug: 'stm32串口通信'
featured: false
draft: false
tags:
  - '笔记'
  - 'stm32'
  - '硬件'
ogImage: ''
description: ''
pubDatetime: 2025-12-27T01:32:00Z
toc: true
---

## CubeMX 配置与基础知识

*   **GPIO 复用与下载调试接口**: CubeMX 开启 GPIO 复用相关代码后，会根据设置配置下载调试接口。需要手动设置下载调试器接口以确保正常下载和调试。
*   **波特率**:
    *   定义：每秒传送的码元数量，即每秒产生多少次高低电平信号。
    *   默认情况下，传送一字节信息需要 10 bit (1 位起始位 + 8 位数据位 + 1 位终止位)。
    *   计算示例：波特率为 115200 bit/s 时，一秒内可传送 115200 / 10 = 11520 字节的数据。
    *   常用波特率：9600, 19200, 38400, 115200 等。
    *   **重要前提**：通信的两个设备必须保持相同的波特率。
*   **硬件连接**:
    *   使用 TTL 串口转 USB 模块。
    *   接线规则：**交叉连接**，即模块的 RX 接设备的 TX，模块的 TX 接设备的 RX。
    *   必须**共地 (GND 连 GND)**。
*   **工具**:
    *   串口调试助手 (用于在 PC 端发送和接收数据)。
    *   安装对应的串口驱动 。

## 串口通信模式

### 1. 轮询模式 (Polling Mode)

*   必须阻塞程序执行，直到发送或接收完成，或者等待超时。
*   接收时通常需要指定接收固定长度的数据。

### 2. 中断模式 (Interrupt Mode)

*   **触发机制**: 每接收或发送一字节数据时触发一次中断。
*   **回调函数**: 每次接收完成后会执行 `HAL_UART_RxCpltCallback` 回调函数。
*   ![image-20260308134401916](C:\Users\N\AppData\Roaming\Typora\typora-user-images\image-20260308134401916.png)
    *   开启了 `USART2 global interrupt` (USART2 全局中断)。
    *   在 `main` 函数前调用 `HAL_UART_Receive_IT(&huart2, receiveData, 2);` 启动中断接收（接收长度为2）。
    *   在 `HAL_UART_RxCpltCallback` 中，接收到数据后，通过判断 `receiveData[0]` 和 `receiveData[1]` 的值来控制 LED 灯的亮灭。
    *   **关键点**: 在回调函数末尾，再次调用 `HAL_UART_Receive_IT(&huart2, receiveData, 2);`，以便开启下一次中断接收。

### 3. DMA 模式 (Direct Memory Access Mode)

*   **原理**: 直接内存访问，由 DMA 控制器硬件帮助搬运数据，不需要 CPU 干预，极大减轻 CPU 负担。
*   **配置**: 需要在 CubeMX 中设置串口的 DMA 通道。
*   **函数后缀**: 将发送和接收函数的后缀改为 `_DMA` (例如 `HAL_UART_Transmit_DMA`)。
*   **触发机制**: 在接收或发送**指定长度**的数据完成时产生中断。

### 4. 串口空闲中断 + DMA 接收不定长数据

*   **原理**: 当串口接收完一帧数据包后，总线出现空闲状态时触发中断。适合接收长度未知的数据包。
*   **回调函数**: 改为调用 `HAL_UARTEx_RxEventCallback` 回调函数。
*   ![image-20260308134524006](C:\Users\N\AppData\Roaming\Typora\typora-user-images\image-20260308134524006.png)
    *   使用了 `HAL_UARTEx_ReceiveToIdle_DMA(&huart2, receiveData, sizeof(receiveData));` 开启空闲中断接收。
    *   在 `HAL_UARTEx_RxEventCallback` 中，`Size` 参数表示实际接收到的数据长度。然后调用 `HAL_UART_Transmit_DMA(&huart2, receiveData, Size);` 将接收到的数据发送回去。
    *   同样，在回调末尾需要重新开启接收 `HAL_UARTEx_ReceiveToIdle_DMA(...)`。
*   **⚠️ 重要注意事项**:
    *   在设置完 DMA 模式接收后，**必须关闭 DMA 传输过半中断**。`__HAL_DMA_DISABLE_IT(&hdma_usart2_rx,DMA_IT_HT);`
    *   如果不关闭，当接收数据量达到设定缓冲区大小的一半时，也会触发 DMA 中断，进而导致错误地调用 `HAL_UARTEx_RxEventCallback` 函数，造成数据处理提前或逻辑混乱。