# EtherCAT Servo State and Monitoring

## 6. Developing IgH Applications

### 6.6 CiA 402 Servo State Machine

Many EtherCAT servo drives use the CoE CiA 402 state machine. A common control sequence is:

```text
Fault Reset
    ↓
Shutdown (0x0006)
    ↓
Switch On (0x0007)
    ↓
Enable Operation (0x000F)
    ↓
Write target position / velocity / torque
```

Actual control-word decisions must use the status-word mask and the drive manual. Requirements for PDOs, units, enable sequences, and stopping behavior may differ between operating modes.

### 6.7 State Monitoring

The application should check the following states periodically or when events occur:

- `ec_master_state_t`: Master link, number of slaves, and AL state.
- `ec_domain_state_t`: Domain working counter and working counter state.
- `ec_slave_config_state_t`: Whether the slave is online and operational.
- Slave status word, error code, and vendor-specific diagnostic objects.

An incomplete working counter generally indicates that a slave is offline, the PDO configurations do not match, or some datagrams did not receive responses. Do not continue outputting hazardous control values while the working counter is abnormal.
