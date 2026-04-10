# Real-Time-Automotive-Instrument-Cluster

Modern automotive instrument clusters are required to simultaneously sample, process, and display multiple vehicle
parameters in real time — a task that demands deterministic scheduling, concurrent execution, and thread-safe data
management. Traditional bare-metal polling architectures fail to satisfy these requirements when the number of
concurrent tasks grows: a blocking ADC read or I2C transaction stalls the entire system, preventing timely updates
to other parameters.
AReal-Time Operating System (RTOS) addresses these challenges through preemptive task scheduling, well-defined
inter-task communication primitives, and guaranteed worst-case response times. This project implements a four
parameter automotive instrument cluster on the ST B-L475E-IOT01A Discovery Kit (STM32L475VGT6, 80 MHz
Cortex-M4) running FreeRTOS with the CMSIS-RTOS v2 API. The four parameters monitored are:
• Engine Temperature — measured by the onboard HTS221 humidity and temperature sensor over I2C2.
• Fuel Level — measured from an external potentiometer connected to ADC channel 1 (12-bit, 0–3.3 V).
• Vehicle Speed — simulated in software using a sinusoidal function (0–120 km/h).
• Engine RPM —derived proportionally from simulated speed, with added harmonic variation (800–6000
RPM).
All values are transmitted over USART1 at 115200 baud and displayed in PuTTY on a host PC. LED indicators on
PA5 (temperature) and PB14 (fuel) give immediate visual status feedback.
