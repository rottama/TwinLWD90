# Twin LWD 90
Binary fixes for Alpha Innotec Luxtronic heat pump controller firmware

This repository documents the patches to V4.81.2 of the Luxtronic Firmware that runs on installations using two LWD90 compressors and the hydraulic module HMD2.

Problem 1: HC Add-time limit too short (de: HRM-Zeit)

The timer 'HC Add-time' increases for as long as the return temperature is below the lower hysteresis point of its targeted temperature (de: Rücklauf-Soll). If this timer exceeds 25 minutes of heating, the second compressor turns on. However, twin compressors would require a flow rate of 4'000 litres per hour. Since no domestical hydraulic installation can be expected to support this insane amount of flow, the flow by-passes via the parallel buffer tank. This raises the return temperature above hysteresis prematurely, leading to an early shutdown of the system. Because the floor heating itself did not heat up equally (i.e. it's still cold), the return temperature quickly falls below hysteresis, triggering a new heating cycle after a brief pause.

The catch is that before the heating turns on, the system waits for the duration of 1 off-time switch cycle (Schaltspielsperre SSP) of 20 minutes, likely due to a bug. Unfortunately, the timer 'HC Add-time' is counting during this time as well, clocking in at 20 minutes already when the heating actually turns on, leaving only 5 minutes to bring the floor heating up to temperature before turning on the second compressor.

As a result, the system is chopping on and off, which puts undue thermal stress on the components (multiple compressor starts instead of one).

Instead, the controller should provide more time for 1 compressor to warm the heating circuit. The 25 minute limit must increase.

