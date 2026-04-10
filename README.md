# Real-Time-Automotive-Instrument-Cluster using STM32L475 + FreeRTOS
A multi-tasked real-time automotive instrument cluster running on the ST B-L475E-IOT01A Discovery Kit using FreeRTOS (via CMSIS-RTOS v2). The system concurrently monitors engine temperature, fuel level, vehicle speed, and engine RPM with deterministic scheduling, thread-safe inter-task communication, LED status indicators, and live UART output to PuTTY.

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Hardware Requirements](#hardware-requirements)
- [System Architecture](#system-architecture)
- [Task Design](#task-design)
- [Inter-Task Communication](#inter-task-communication)
- [Circuit Diagram](#circuit-diagram)
- [Results](#results)
- [Resource Utilisation](#resource-utilisation)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)

## Overview
Modern automotive instrument clusters must simultaneously sample, process, and display multiple vehicle parameters in real time. Traditional bare-metal polling architectures fail when task count grows — a blocking I2C or ADC read stalls the entire system.
This project solves that using FreeRTOS preemptive scheduling on an STM32L475VGT6 (ARM Cortex-M4, 80 MHz). Four parameters are monitored concurrently:

| Parameter | Source |
| -------- | -------- |
| Engine Temperature   | HTS221 sensor (I2C2)   |
| Fuel Level   | Potentiometer → 12-bit ADC   |
| Vehicle Speed  | Software sinusoidal simulation (0–120 km/h)   |
| Engine RPM   | Derived from speed with harmonic variation (800–6000 RPM)  |

All data is transmitted over USART1 at 115200 baud and visualised live in PuTTY and a custom serial dashboard.

## Features
- Six concurrent FreeRTOS tasks with deterministic update rates
- Thread-safe data sharing using CMSIS-RTOS v2 **message queues** and **mutexes**
- Real HTS221 temperature sensor over I2C2 with retry logic
- 12-bit ADC fuel level sensing with potentiometer
- Sinusoidal speed/RPM simulation (HIL-style)
- Dual LED status indicators with multi-state blink patterns
- Live UART output tagged per sensor (`[TEMP]`, `[FUEL]`, `[SPEED]`)
- Task timing verified within ±1 ms scheduling granularity

## Hardware Requirements
| Component | Details |
|-----------|---------|
| MCU Board | ST B-L475E-IOT01A Discovery Kit (STM32L475VGT6) |
| Temperature Sensor | Onboard HTS221 (I2C2 — SDA: PB11, SCL: PB10) |
| Fuel Sensor | External potentiometer → ADC1 CH1 (PC0 / ARD_A0) |
| Temperature LED (LD1) | GPIO PA5 |
| Fuel LED (LD2) | GPIO PB14 |
| UART | USART1 — TX: PA9, RX: PA10 (115200 baud, 8N1) |
| Host PC | PuTTY terminal via ST-Link virtual COM port |

## System Architecture
 
### Firmware Execution Flowchart

