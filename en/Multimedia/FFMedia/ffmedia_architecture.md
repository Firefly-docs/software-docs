# Architecture Overview

FFMedia uses a Producer/Consumer model, and all units are abstracted as the `ModuleMedia` class. One producer can connect to multiple consumers, and one consumer can also connect to multiple producers. Input source modules have no upstream producers, while processing and output modules are connected to the pipeline through unified interfaces.

![FFMedia software framework](../../../img/FFMedia/p1.png)
