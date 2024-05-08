# DSP testbench template #

Simple but puwerful Python-Verilog combination for DSP module verification.

Brief spec:
  * Designed for time-invariant systems
  * Real-valued signals as input and output (check out "complex" branch for
      complex-valued i/o template extention
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

### General approach ###

I leave this information here because I'd like to refer to it in different places
where I use this template. So the general idea is to separate architectural
aspects and RTL-coding aspects as much as possible. For this purpose it is
desireable to create Python model that emulates limited precision module in
Verilog, again, as close as possible. All roundings, data range boundaries, etc.
This model is compared against reference model which used floating point precise
functions (or Python libraries) to calculate reference data.

After running limited precision Python model against reference on sufficient
amount of data, ensuring that the performance meets requiremets, we can switch
to Verilog simulation, making this limited precision model the reference.

```

1. Align limited precision Python model with some general solution

   +-----------+      test data      +-------------+
   |           |          |          |   Python    |
   |  Python   |<---------+--------->|  RTL module |
   |  general  |                     |  emulating  | <==>  Align with the Verilog code, roundings,
   | solution  |------+       +------|   limited   |       floors, corner cases, etc. Make changes
   | (precise) |      |       |      |  precision  |       in the RTL code based on things found out
   |           |      v       v      |    model    |       during this stage.
   +-----------+   compare results   +-------------+

2. Align Verilog module with limited precision Python model

                                     +-------------+      test data      +-----------+
                                     |   Python    |          |          |           |
                                     |  RTL module |<---------+--------->|   RTL     |
                                     |  emulating  |                     |  module   |
                                     |   limited   |------+       +------| simulation|
                                     |  precision  |      |       |      |           |
                                     |    model    |      v       v      |           |
                                     +-------------+   compare results   +-----------+
```

This approach can save a lot of time by making clear identification of the
source of a problem. Of course there could be some quality vs time tradeoffs.
But the more problems we find during the first stage the more time we save,
because it is much easier to debug in Python.

### Authors ###

 -- Dmitry Nekrasov <bluebag@yandex.ru>  Sat, 13 Apr 2024 09:36:58 +0300

### License ###

MIT
