# 架构概览

FFMedia 采用 Producer/Consumer 模型，所有单元都抽象为 `ModuleMedia` 类。一个生产者可以连接多个消费者，一个消费者也可以连接多个生产者。输入源模块没有上游生产者，处理和输出模块通过统一接口接入管线。

![FFMedia 软件框架](../../../img/FFMedia/p1.png)
