# DSP testbench template #

Simple but puwerful Python-Verilog combination for DSP module verification.

Brief spec:
  * Designed for time-invariant systems
  * Real-valued signals as input and output (I will make complex-valued
    extention in a branch later)
  * No parallel input (single sample at once)
  * Python program as the top level call
  * Console mode oriented
  * Could be looped automatically through different set of parameters
  * Standard RTL top-level module interface:
        - clock
        - synchronous reset
        - input data  (integer or fixed point)
        - output data (integer or fixed point)
        - input sample valid (one strope per one valid input sample)
        - output sample valid (one strope per one valid output sample)
  * Build-in checks:
        - x-states on output data bus while output sample valid signal is active
  * Collected metrics:
        - NMSE
        - Peak error (% of the dynamic range)

Anyway, it is not supposed to be used as is, you need to fit it to your project
anyway. And I do not suppose it is going to be used as a submodule. Rather CTRL-C CTRL-V
way. There is not so much to copy.

Usage model:

```
     | manual parameters set                                                               ^ log
     |                                                                                     |
  +--|----------------------------------------------------------------------------------------+
  |  v              +-------------+                                                        |  |
  |                 |gen test data|--------+------------+                                  |  |
  |     test.py     +-------------+        v            |                                  |  |
  |                                 +------------+      |                                  |  |
  |                   +------------>|gen ref data|----- | ------------------+              |  |
  |                   |             +------------+      |                   |              |  |
  +------+---------------------------------+---------------------+----------------------------+
         |            ^                    |            |        |vsim -do  |              |
         |            |                    |            |        |make.tcl  |              |
         |call        |some stuff          v            |        |          |              |
         |            |             +----------+        |   +----------+    |              |
         |            |             |files list|------- | ->| make.tcl |    |              |
         |            |             +----------+        |   +----------+    |              |
         |            |                                 v        |sim       v              |
         |            |                            +---------+   |     +---------+         |
         |            |                            |input.txt|   |     | ref.txt |         |
         |            |                            +---------+   |     +---------+         |
         v            |                                 |        v          |              |
      +------------------+                      +-------------------------------------+    |
      | Possible external|    +-----------+     |       |                   v         |    |
      | program that     |--->|some stuff |---->|--+    |  tb.sv         compare -----|----+
      | gen some stuff   |    +-----------+     |  |    |                   ^         |  score
      +------------------+                      +-------------------------------------+
                                                   |    |                   |
                                                   v    v data_i            | data_o
                                                +-------------------------------------+
                                                |                                     |
                                                |         RTL CODE TOP LEVEL          |
                                                |                                     |
                                                +-------------------------------------+
```

### Authors ###

 -- Dmitry Nekrasov <bluebag@yandex.ru>  Sat, 13 Apr 2024 09:36:58 +0300


### License ###

MIT
