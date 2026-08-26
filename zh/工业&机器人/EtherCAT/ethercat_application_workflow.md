# EtherCAT 应用开发流程

## 6. IgH 应用开发

### 6.1 开发流程

IgH 应用通常按以下顺序初始化：

1. 使用 `ecrt_request_master()` 获取主站。
2. 使用 `ecrt_master_create_domain()` 创建过程数据 Domain。
3. 使用 `ecrt_master_slave_config()` 按别名、位置、Vendor ID 和 Product Code 配置从站。
4. 使用 `ecrt_slave_config_pdos()` 配置同步管理器和 PDO。
5. 使用 `ecrt_domain_reg_pdo_entry_list()` 注册应用需要访问的 PDO 条目。
6. 按需配置 DC、SDO、看门狗和从站专用参数。
7. 使用 `ecrt_master_activate()` 激活主站。
8. 使用 `ecrt_domain_data()` 获取过程数据内存。
9. 进入固定周期的实时循环。
10. 退出前停止输出、释放主站并恢复安全状态。

```text
获取主站 → 创建 Domain → 配置从站和 PDO → 激活主站
                                      │
                                      ▼
接收 → 处理 Domain → 读取输入 → 写入输出 → 排队 → 发送
  ▲                                                     │
  └────────────────── 固定周期循环 ──────────────────────┘
```

### 6.2 生成 PDO 配置参考

先连接并上电从站，然后执行：

```bash
ethercat slaves
ethercat pdos -p 0
ethercat cstruct -p 0
```

将 `cstruct` 输出作为应用配置的起点，并根据从站手册删除无关条目或调整 PDO 映射。Vendor ID 和 Product Code 可通过以下命令确认：

```bash
ethercat slaves -v
ethercat xml -p 0
```

不要只按设备名称匹配从站。应用应校验 Vendor ID、Product Code，并按需要校验 Revision Number。
