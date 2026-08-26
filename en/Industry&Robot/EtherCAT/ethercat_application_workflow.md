# EtherCAT Application Workflow

## 6. Developing IgH Applications

### 6.1 Development Workflow

An IgH application is generally initialized in the following order:

1. Obtain the master with `ecrt_request_master()`.
2. Create a process data domain with `ecrt_master_create_domain()`.
3. Configure slaves by alias, position, Vendor ID, and Product Code with `ecrt_master_slave_config()`.
4. Configure Sync Managers and PDOs with `ecrt_slave_config_pdos()`.
5. Register the PDO entries accessed by the application with `ecrt_domain_reg_pdo_entry_list()`.
6. Configure DC, SDOs, watchdogs, and slave-specific parameters as needed.
7. Activate the master with `ecrt_master_activate()`.
8. Obtain the process data memory with `ecrt_domain_data()`.
9. Enter the fixed-period real-time loop.
10. Before exiting, stop outputs, release the master, and restore a safe state.

```text
Request master → Create domain → Configure slaves and PDOs → Activate master
                                                   │
                                                   ▼
Receive → Process domain → Read inputs → Write outputs → Queue → Send
   ▲                                                               │
   └──────────────────── Fixed-period loop ─────────────────────────┘
```

### 6.2 Generating a PDO Configuration Reference

Connect and power on the slaves, then run:

```bash
ethercat slaves
ethercat pdos -p 0
ethercat cstruct -p 0
```

Use the `cstruct` output as a starting point for the application configuration. Remove unnecessary entries or adjust the PDO mapping according to the slave manual. Confirm the Vendor ID and Product Code with:

```bash
ethercat slaves -v
ethercat xml -p 0
```

Do not match slaves only by device name. The application should validate the Vendor ID and Product Code and, when necessary, the Revision Number.
