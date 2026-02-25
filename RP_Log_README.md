# RP_Log 日志系统

致力于实现低侵入式、方便快捷、可靠的日志系统。

## 快速开始

1. 实现串口发送函数
```c
// 在 RP_Log.c 里实现，或者其他地方重写这个函数
int RP_Log_Transmit(const uint8_t *data, uint16_t length)
{
    if (HAL_UART_Transmit_DMA(&huart1, data, length) == HAL_OK) {
        return 0;
    }
    return -1;
}
```

2. 调用日志
```c
RP_LOG_INFO("System started");
RP_LOG_ERROR("Init failed: %d", err_code);
```

3. 另开一个日志线程并调用以下函数
```c
while (1) {
    g_rp_log.work(&g_rp_log);
    osDelay(1);
}
```

## 输出示例

```
[1234][INFO][main.c:45]: System started
[1235][ERROR][task.c:20]: Init failed: -1
```

## 配置项

在 `g_rp_log.config_param` 中修改：

| 配置项        | 默认值            | 说明           |
| ------------- | ----------------- | -------------- |
| output_range  | RP_LOG_OUTPUT_ALL | 输出等级       |
| use_timestamp | 1                 | 是否显示时间戳 |
| rtt_use_color | 1                 | RTT颜色        |

等级可选：`RP_LOG_OUTPUT_FATAL_ONLY` ~ `RP_LOG_OUTPUT_ALL`

## 开启RTT

在 RP_Log.c 中：
```c
#define RP_LOG_USE_RTT 1
```

需要集成 SEGGER_RTT 库。

## API

| 函数                 | 说明                 |
| -------------------- | -------------------- |
| g_rp_log.write()     | 写日志（宏调用）     |
| g_rp_log.work()      | 处理输出（循环调用） |
| g_rp_log.get_count() | 获取缓冲区内日志数   |
| g_rp_log.flush()     | 清空缓冲区           |

## 日志等级说明

| 等级      | 场景                                                 |
| --------- | ---------------------------------------------------- |
| **FATAL** | HardFault、触发看门狗、无法恢复的错误等              |
| **ERROR** | 电机掉线、传感器读取失败、关键功能初始化失败等       |
| **WARN**  | 电机过温、超热量、超射速、超功率、缓冲能量过低等     |
| **INFO**  | 收到指令、开超电、打弹、跳跃、装甲板伤害、状态切换等 |
| **DEBUG** | 记录变量值、中间计算结果、排查问题用                 |
| **TRACE** | 每行代码执行轨迹、原始数据                           |

## 日志宏

- RP_LOG_FATAL
- RP_LOG_ERROR
- RP_LOG_WARN
- RP_LOG_INFO
- RP_LOG_DEBUG
- RP_LOG_TRACE
