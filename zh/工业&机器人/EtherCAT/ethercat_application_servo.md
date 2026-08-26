# EtherCAT 伺服状态和监控

## 6. IgH 应用开发

### 6.6 CiA 402 伺服状态机

许多 EtherCAT 伺服驱动器使用 CoE CiA 402 状态机。常见控制流程为：

```text
Fault Reset
    ↓
Shutdown (0x0006)
    ↓
Switch On (0x0007)
    ↓
Enable Operation (0x000F)
    ↓
写入目标位置 / 速度 / 扭矩
```

实际控制字判断必须结合状态字掩码和驱动器手册。不同工作模式对 PDO、单位、使能顺序和停止行为的要求可能不同。

### 6.7 状态监控

应用应周期或按事件检查以下状态：

- `ec_master_state_t`：主站链路、从站数量和 AL 状态。
- `ec_domain_state_t`：Domain 工作计数和工作计数状态。
- `ec_slave_config_state_t`：从站是否在线、是否可操作。
- 从站状态字、错误码和厂商诊断对象。

工作计数不完整通常表示从站掉线、PDO 配置不一致或部分数据报未得到响应。不要在工作计数异常时继续输出危险控制量。
