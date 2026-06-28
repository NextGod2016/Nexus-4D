# ESP32-S3 Distributed Heterogeneous Embedded Computing Cluster
> An Original 4-Tier Self-Evolving Edge Architecture with Vector Quantization Communication & Local OTA Infrastructure

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Document Version](https://img.shields.io/badge/Version-v1.1-blue.svg)](https://github.com/NextGod2016/esp32-cluster-architecture)
[![Status](https://img.shields.io/badge/Status-Design%20Finalized-orange.svg)](https://github.com/NextGod2016/esp32-cluster-architecture#current-verification-status)

## 🚀 Project Overview
This project implements a **distributed heterogeneous embedded computing cluster** based on the ESP32-S3 platform, designed to eliminate the critical performance bottlenecks of single-chip systems: CPU, memory, and bus resource contention between computing, scheduling, rendering, and peripheral management.

Unlike traditional multi-board assemblies, this is a **cloud-edge collaborative, self-evolving edge node platform** with a modular hardware design, asynchronous SPI bus communication, local OTA upgrade system, and next-generation **Vector Quantization (VQ)** data exchange model.

### Core Purpose
- Break the physical limits of single ESP32-S3 chips
- Achieve hardware-level decoupling of computing tasks
- Build an industrial-grade, scalable, and maintenance-free edge system
- Enable AI-driven self-evolution with cloud-server collaboration

## 🎯 Performance Positioning & Design Vision
### Core Design Value
This architecture solves the fatal flaws of single-board embedded systems:
- Mutual blocking between high-load tasks (graphics, AI, storage, communication)
- Fixed performance ceiling and poor scalability
- High maintenance difficulty and system instability
- Lack of reliable OTA and fault rollback mechanisms

### Performance Objectives
1. **Computing Decoupling**: Dedicated logic board for scheduling + independent function boards for hardware acceleration (1+1 > 2 performance gain)
2. **High-Efficiency Bus**: 20MHz SPI half-duplex asynchronous communication (1.2~1.8MB/s effective bandwidth)
3. **Ultra-Low Bandwidth Usage**: Vector Quantization (VQ) reduces payload by **97%+**, breaking SPI physical limits
4. **Industrial Reliability**: Local firmware repository, dual-file OTA, offline reflash, and A/B rollback (never-brick system)
5. **Unlimited Expansion**: Hot-swappable function boards with auto-discovery and plug-and-play

### Final Orientation
Build a **lightweight, modular, self-evolving edge computing node** for:
High-frame-rate graphics rendering, AI acceleration, multimedia processing, and industrial IoT applications.

## 🏗️ 4-Tier System Architecture
The system adopts a hierarchical decoupled design (cloud + edge cluster):

| Tier | Node | Role | Analogy |
|------|------|------|---------|
| Cloud Brain | Local x64 Server | MCP Host, AI training, strategy generation, dual-file compilation | Prefrontal Cortex |
| Instruction Tier | Top Board (ESP32-S3 + Wi-Fi) | Policy AI execution, log aggregation, cloud communication | Cerebral Cortex |
| Central Tier | Logic Board (ESP32-S3 + SD Card) | Bus scheduling, device discovery, OTA repository management | Chipset / System Bus |
| Execution Tier | Function Boards (ESP32-S3 Slave) | Specialized tasks (rendering, audio, AI acceleration) | Hardware Accelerators |

## 🔑 Core Features
### 1. Vector Quantization (VQ) Communication (Next-Gen)
- Local codebook dictionary on function boards
- Transmit **indexes instead of raw data**
- 240x240 frame payload: 8KB → **225 Bytes**
- Ultra-low computing decoding (table lookup only)
- Co-optimized with lightweight AI inference

### 2. Asynchronous SPI Bus Protocol
- SPI Half-Duplex (HD) @ 20MHz
- 1-master, multi-slave architecture with independent CS pins
- Non-blocking communication + virtual read transactions for log backhaul
- Robust frame format with CRC16, sequence check, and QoS control

### 3. Self-Evolving OTA System
- Local SD card firmware repository (mass storage)
- Dual-file evolution system (policy file + firmware image)
- Offline auto-reflash for replacement function boards
- A/B partition rollback (100% anti-brick)

### 4. Modular & Hot-Swappable
- Auto device discovery & driver binding
- Support: Rendering, Audio, AI Accelerator, Sensor boards
- Isolated hardware design (no mutual interference)
- Unlimited extensibility

### 5. Hierarchical Permission Isolation
Permission Chain: `Server > Top Board > Logic Board > Function Boards`
- Physical isolation of unauthorized write access
- Industrial-grade security and stability

## 🧩 Hardware Layered Design
### Central Tier (Logic Board)
- SPI Host, SD Card OTA Repository
- Device routing, task scheduling, peripheral management

### Execution Tier (Function Boards)
- **Rendering Board**: 240x240 high-frame-rate display
- **Audio Board**: DSP audio processing
- **AI Accelerator Board**: NPU lightweight inference
- Custom sensor & expansion boards

## 📡 Communication Protocol
### Physical Layer
- Interface: SPI Slave HD (Half-Duplex)
- Clock: 20MHz
- Effective Bandwidth: 1.2 ~ 1.8MB/s
- Max DMA Transfer: 4092 Bytes

### Enhanced Frame Format
`[HEADER 2B] [TARGET_ID 1B] [CMD 1B] [FLAGS 1B] [SEQ 1B] [LEN 2B] [PAYLOAD N] [CRC16 2B]`
- QoS: Reliable control frames + Best-effort data frames
- Sequence check for anti-loss & disorder
- CRC16 for anti-interference

## 📊 Current Verification Status
- ✅ Verified: SPI HD dual-board communication
- 🚧 In Progress: Logic board device discovery, routing table, SD card repository
- 📅 Planned: Full OTA chain, VQ communication, top-board cloud integration

## 🎯 Future Roadmap
1. Full Vector Quantization (VQ) communication deployment
2. Distributed AI log closed-loop system
3. MCP cloud-edge collaborative scheduling
4. Multi-node cluster expansion
5. Mass production modularization

## 📦 License
This project is licensed under the **MIT License** - free to use, modify, and distribute.

---
**Document Version**: v1.1 | **Last Updated**: 2026
**GitHub Repository**: https://github.com/NextGod2016/esp32-cluster-architecture
