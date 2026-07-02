这是重新输出的完整可直接上传的 `nexus-4D.md` 终版。针对图片加载失败的问题，我将路线图改为了**原生ASCII文本框图**，无需依赖任何外部图片文件，所有GitHub、Markdown编辑器均可直接正常渲染，彻底避免加载异常；同时保留了图片引用的注释行，你后续放入正式图片文件后取消注释即可。

所有技术细节、阶段验收指标、系统底座与工具署名均完整保留，文档风格与原项目英文规范完全统一。

```markdown
# Nexus-4D
**ESP32-S3 based 4-Dimensional Distributed Heterogeneous Compute Cluster**  
Vector-Dictionary Transmission · Soft-GPU Pipeline · Self-Evolving OTA · Offline Voice AI Node

Four-Layer Edge Architecture: Cloud (x86 Strategy) → Command (Wi-Fi Scheduler) → Hub (DMA Bypass) → Execution (Rendering / Audio / Xiaozhi AI)

**Core Status:** ✅ Dual Execution Nodes Validated | Tri-Node Interconnection: 🔧 In Progress | Xiaozhi AI Standalone: ✅ Verified

## About This Project
This project proposes a distributed heterogeneous edge computing architecture built on the ESP32-S3 platform. It splits four core roles — computing, scheduling, rendering, and peripheral management — into independent chips interconnected via a high-speed SPI bus, completely eliminating the resource contention bottleneck of single-chip solutions.

The v2.0 release introduces a dedicated rendering function board with a general-purpose software-defined GPU (Soft GPU) pipeline. Implemented with triangle primitives and MVP matrix transformation, this pipeline replaces traditional MCU 2D graphics libraries and provides a unified computing foundation for 2D UI, multimedia playback, and future 3D rendering expansion. The entire stack is engineered on top of the mature Retro-Go driver framework for maximum reliability and minimum adaptation cost.

In the v2.1 iteration, an offline Xiaozhi AI voice interaction node is added as the third execution-layer function board, realizing physical decoupling of AI interaction, graphics rendering and audio processing. The cluster evolves from a pure multimedia heterogeneous system to an intelligent edge computing cluster with native voice interaction capability, while fully retaining the original four-layer architecture, protocol stack and OTA system without destructive modifications.

## Core Advantages
### Complete Resource Decoupling
Four physically isolated layers run independently without competing for CPU or memory. Single-module failures do not affect the whole system, delivering higher stability and linearly scalable performance. The newly added AI node exclusively occupies one chip, and does not occupy the bus or computing resources of rendering and audio nodes.

### General-Purpose Soft GPU Pipeline
A single rendering engine covers 2D UI, image transformation, audio spectrum visualization, and video frame output. Built around a fixed-function pipeline paradigm, it natively supports smooth expansion to 3D rendering. All graphical output from AI interaction scenarios can also be uniformly adapted to this pipeline.

### Modular Heterogeneous Expansion
All function boards use a standardized identity descriptor protocol for automatic hot-plug discovery and binding. Rendering, audio, offline voice AI, and sensor aggregation nodes can be mounted on demand with active-standby failover. The Xiaozhi AI node can be connected with zero modification to the existing base system.

### High-Reliability OTA System
Local SD card firmware repository combined with A/B partition atomic rollback supports both online upgrades and offline reflashing. Failed upgrades automatically revert to a safe version without downtime. All newly added nodes are natively compatible with this set of upgrade and rollback system.

### Retro-Go Ecosystem Reuse
Built on proven Retro-Go subsystems including display drivers, I2S audio, dual-core scheduling, and PSRAM memory management, significantly reducing development and hardware adaptation risk.

## Architecture Overview
The system follows a strictly decoupled four-layer design, forming a complete decision → scheduling → execution chain from top to bottom. No cross-layer direct communication is allowed.

```text
                          ┌─────────────────────────┐
                          │   Cloud Brain Layer     │
                          │   Local x64 Server      │
                          └───────────┬─────────────┘
                                      │ MCP over Wi-Fi
                                      ▼
                          ┌─────────────────────────┐
                          │    Command Layer        │
                          │ Top-Level Board ESP32-S3│
                          └───────────┬─────────────┘
                                      │ SPI HD / Commands + OTA + Media
                                      ▼
                          ┌─────────────────────────┐
                          │      Hub Layer          │
                          │  Logic Board ESP32-S3   │
                          └─────────┬─┬─┬─┬─────────┘
                                    │ │ │ │
                        ┌───────────┘ │ │ └───────────┐
                        ▼             ▼ ▼             ▼
                ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────────┐
                │  Rendering │ │   Audio    │ │ Xiaozhi AI │ │ Reserved Slot  │
                │ Soft GPU + │ │ DSP Process│ │ Offline VO │ │   Expansion    │
                │ Multimedia │ │            │ │            │ │                │
                └────────────┘ └────────────┘ └────────────┘ └────────────────┘

                Structured Logs / Status  →  Aggregated Status Logs  →  Full Log Upload
```

| Layer | Board | Core Responsibilities | Permission Level |
|-------|-------|-----------------------|------------------|
| Cloud Brain Layer | Local x64 desktop server | Deep learning training, policy generation, firmware & media resource compilation, AI model optimization | Highest; only node permitted to modify top-level board policies |
| Command Layer | Top-Level Board (ESP32-S3 + Wi-Fi) | Policy AI execution, log aggregation, upgrade scheduling, resource distribution, AI task scheduling | Cluster decision hub |
| Hub Layer | Logic Board (ESP32-S3 + SD Card) | Bus enumeration, device routing, peripheral management, firmware repository, offline reflashing | Sole bus master; routes traffic without parsing business logic |
| Execution Layer | Multiple Function Boards (ESP32-S3) | Atomic task execution: graphics rendering, audio decoding, offline voice wake-up & interaction, neural inference | Responds only to bus commands; no direct inter-board communication |

For full interaction topology, protocol frame format, and detailed sequence diagrams, see `ARCHITECTURE.md`.

## Core Interaction Workflows
### 1. Power-On Auto Discovery
On boot, the Logic Board broadcasts discovery queries through each CS pin. Each function board replies with a 64-byte standardized identity descriptor. The Logic Board then registers the device into its routing table, loads the matching driver, and reports the device list upstream — all fully automatic without manual configuration. Supports hot-plug identification of the Xiaozhi AI node.

### 2. End-to-End Multimedia Rendering
The Top-Level Board issues playback commands. The Logic Board streams fragmented media data from the SD card to the rendering and audio function boards via SPI. The Soft GPU pipeline handles decoding, matrix transformation, rasterization, and DMA frame output in parallel, with structured status logs returned periodically.

### 3. End-to-End Offline AI Interaction
The Top-Level Board issues AI interaction instructions. The Logic Board forwards the instructions to the Xiaozhi AI function board through the SPI bus. The AI node completes voice wake-up, instruction recognition and response generation locally, and returns structured results and status back through virtual read transactions. The whole process does not depend on the cloud, and does not occupy the computing resources of rendering and audio nodes.

### 4. OTA Update with Atomic Rollback
Firmware images are distributed to the local SD card repository. During low-load periods, the Logic Board forwards firmware fragments to the target board, which writes to its backup OTA partition. If heartbeat communication fails within 10 seconds after reboot, a hardware watchdog triggers an automatic rollback to the stable main partition. This mechanism is fully applicable to all execution nodes including the Xiaozhi AI board.

## Current Validation Status
| Status | Modules |
|--------|---------|
| ✅ Validated | Dual execution nodes full-link communication (Logic Board + Rendering Board + Audio Board) |
| ✅ Validated | Basic device discovery, routing table maintenance and instruction distribution |
| ✅ Validated | SD card FATFS file system driver & local firmware repository |
| ✅ Validated | Soft GPU pipeline & full multimedia service stack (JPEG / MP3 / MJPEG) |
| ✅ Validated | OTA atomic rollback, hot-plug and exception degradation mechanism |
| ✅ Validated | Xiaozhi AI board standalone deployment & offline voice interaction kernel stability |
| 🔧 In Development | Three-node SPI bus interconnection & physical signal verification |
| 🔧 In Development | Xiaozhi AI board SPI slave protocol adaptation |
| 🔧 In Development | Logic Board AI node routing extension & driver forwarding |
| 🔧 In Development | Three-node parallel operation stability & bus conflict verification |
| 🔧 In Development | Xiaozhi AI node access to OTA & offline reflashing system |
| 📋 Designed, Pending Implementation | Dynamic scheduling priority adjustment on Logic Board |
| 📋 Designed, Pending Implementation | Top-Level Board Wi-Fi MCP communication protocol |
| 📋 Designed, Pending Implementation | Distributed AI logging closed loop & strategy self-evolution |
| 📋 Designed, Pending Implementation | Business linkage between AI node and rendering/audio subsystems |
| 📋 Designed, Pending Implementation | Multi-cluster cascade & more function node expansion |

## v2.1 Iteration Roadmap
The tri-node integration follows a strict gate-based verification methodology: hardware stability first, simulation before physical deployment, and single-node validation before full cluster bring-up. Each phase has quantitative acceptance criteria and can only proceed after the previous milestone is fully verified.

<!-- Uncomment below if you have the roadmap image file in ./docs/assets/ -->
<!-- ![v2.1 Tri-Node Bring-up Roadmap](./docs/assets/nexus4d-trinode-roadmap.png) -->

```text
                  ┌──────────────────────┐   ┌──────────────────────┐
                  │ Brownout Fix & Power │   │ 24h Stability Test   │
                  └───────────┬──────────┘   └──────────┬───────────┘
                              └──────────────┬───────────┘
                                             ▼
                          ┌───────────────────────────────┐
                          │       M0: Base Stability     │
                          └───────────────┬───────────────┘
                                          │
          ┌───────────────────────────────┼───────────────────────────────┬──────────────────────┐
          │ ESP-IDF Native API Porting    │ GDMA Channel Allocation      │ Firmware Adaptation  │
          └───────────────────────────────┼───────────────────────────────┴──────────────────────┘
                                          ▼
                          ┌───────────────────────────────┐
                          │ M1: SPI Slave Standalone Sim │
                          └───────────────┬───────────────┘
                                          │
                          ┌───────────────┴───────────────┐
                          │ 1-20MHz Frequency Scan        │ I2S+SPI Concurrent Capture
                          └───────────────┬───────────────┘
                                          ▼
                          ┌───────────────────────────────┐
                          │ M2: Dual-Board Physical Link │
                          └───────────────┬───────────────┘
                                          │
          ┌───────────────────────────────┼───────────────────────────────┬──────────────────────┐
          │ Dynamic Water-Level Scheduler │ TLV Variable-Length Status    │ SPI-Based OTA        │
          └───────────────────────────────┼───────────────────────────────┴──────────────────────┘
                                          ▼
                          ┌───────────────────────────────┐
                          │ M3: Three-Node Full Cluster   │
                          └───────────────┬───────────────┘
                                          ▼
                          ┌───────────────────────────────┐
                          │   M4: AI Business Integration │
                          └───────────────────────────────┘
```

| Milestone | Status | Core Objective | Key Technical Tasks | Quantitative Acceptance Criteria |
|-----------|--------|----------------|---------------------|----------------------------------|
| M0: Base Stability | 🔧 In Progress | Eliminate hardware reliability risks and establish a stable baseline for all subsequent software integration | 1. Power supply topology optimization & bulk capacitor configuration<br>2. Brownout reset root cause tracing<br>3. Full-load long-term stability test | 1. Zero resets under 24-hour continuous full load (max volume + WiFi active + continuous inference)<br>2. 3.3V rail transient drop ≤ 0.2V, ripple ≤ 50mV<br>3. No brownout events recorded in RTC reset registers |
| M1: SPI Slave Standalone Simulation | 📋 Planned | Validate SPI Slave driver and firmware adaptation in an isolated environment, avoiding on-cluster debugging overhead | 1. ESP-IDF v5.x native SPI Slave API porting<br>2. Static GDMA channel allocation & isolation verification<br>3. Minimal-intrusion firmware modification<br>4. Full instruction set simulation with a second ESP32-S3 | 1. 100% accuracy of 64-byte identity descriptor reading<br>2. Zero audio popping and PDM sample loss under 8h continuous SPI+I2S concurrency<br>3. Synchronous command response latency ≤ 50ms |
| M2: Dual-Board Physical Interconnect | 📋 Planned | Verify physical signal integrity and basic command passthrough between real Hub and AI boards | 1. 1–20MHz SPI frequency scanning & stable operating point calibration<br>2. I2S + SPI concurrent signal capture with logic analyzer<br>3. Basic routing table registration & command forwarding | 1. Stable communication at ≥ 10MHz with zero bit error rate<br>2. Zero SPI timeout / retransmission in 4-hour continuous operation<br>3. Compliant with SPI Mode 0 timing specification |
| M3: Three-Node Full Cluster | 📋 Planned | Achieve stable parallel operation of rendering, audio and AI nodes, with full system capability integration | 1. Dynamic water-level bus scheduler implementation<br>2. TLV variable-length status protocol adaptation<br>3. AI node OTA access via SPI bus | 1. Rendering frame rate drop ≤ 15% under full-load tri-node concurrency<br>2. Zero audio glitches during AI wakeup and inference<br>3. Atomic rollback works correctly for AI node OTA |
| M4: AI Business Integration | 📋 Designed | Realize cross-node functional linkage, evolving from technical verification to complete product experience | 1. Voice wakeup → UI rendering → audio response full-link linkage<br>2. Voice-controlled multimedia playback instruction passthrough<br>3. Real-time inference status visualization | 1. End-to-end voice interaction response within 500ms<br>2. Zero functional coupling between subsystems<br>3. Full backward compatibility with original architecture |

## Documentation
- 📄 **Full Technical Paper v2.1** — Complete architecture design, protocol stack, Soft GPU implementation details, storage system, OTA mechanism and AI node specification
- 🏗️ **Architecture Deep Dive** — Full interaction topology, sequence diagrams, and communication protocol specifications
- 🛠️ **Getting Started Guide** — Hardware setup, compilation, and flashing instructions (coming soon)
- 📝 **v2.1 Iteration Changelog** — Tri-node expansion details and progress tracking

## Built with & Acknowledgments
### Embedded System Foundations
- **Retro-Go**: Provides mature display driver, I2S audio, dual-core scheduling and PSRAM memory management subsystems, serving as the underlying software base for the cluster.
- **Xiaozhi AI (小智AI)**: Offline voice interaction kernel for the dedicated AI node, delivering local wake-up, speech recognition and response generation capabilities.

### AI & Development Assistants
- **Doubao (豆包)**: Architecture analysis, engineering scheme design, code implementation guidance and document organization.
- **DeepSeek**: Technical scheme review, risk identification and implementation path optimization.
- **AilyBlockly**: Embedded development toolchain support and visual logic debugging assistance.

## License
This project is released under the MIT License. You are free to use, modify, and distribute the design with proper attribution.

## About
ESP32-S3 based 4-Dimensional Distributed Heterogeneous Compute Cluster with Vector-Dictionary Transmission, Soft-GPU pipeline and offline voice AI node.

**Topics:** ota, distributed-computing, spi-dma, edge-computing, heterogeneous-computing, retro-gaming, esp32-s3, soft-gpu, embedded-cluster, multimedia-acceleration, offline-ai, voice-assistant
```
