# ESP32-S3 Distributed Heterogeneous Embedded Computing Cluster v2.0
> Four-Layer Self-Evolving Edge Architecture · Software-defined GPU Rendering Subsystem · Localized OTA Infrastructure · Multimedia Heterogeneous Acceleration
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Document Version](https://img.shields.io/badge/Version-v2.0-blue.svg)](./README.md)
[![Status](https://img.shields.io/badge/Status-Core%20Validated%20%7C%20Render%20Subsystem%20in%20Dev-orange.svg)](./README.md#9-current-validation-status)

## Table of Contents
- [Project Overview](#project-overview)
  - [Abstract](#abstract)
  - [Core Values and Advantages](#core-values-and-advantages)
  - [Keywords](#keywords)
- [1. Introduction](#1-introduction)
  - [1.1 Problem Statement](#11-problem-statement)
  - [1.2 Design Objectives](#12-design-objectives)
  - [1.3 Architecture Overview](#13-architecture-overview)
- [2. System Architecture](#2-system-architecture)
  - [2.1 Cloud Brain Layer: Local Server](#21-cloud-brain-layer-local-server)
  - [2.2 Three-Layer Physical Isolation of Embedded Cluster](#22-three-layer-physical-isolation-of-embedded-cluster)
  - [2.3 Function Board Expansion Mechanism](#23-function-board-expansion-mechanism)
  - [2.4 Architecture Design Principles](#24-architecture-design-principles)
- [3. Inter-Chip Communication Protocol Stack](#3-inter-chip-communication-protocol-stack)
  - [3.1 Physical Layer Specifications](#31-physical-layer-specifications)
  - [3.2 Protocol Frame Format](#32-protocol-frame-format)
  - [3.3 Device Discovery and Identity Descriptor](#33-device-discovery-and-identity-descriptor)
  - [3.4 Asynchronous Communication Model](#34-asynchronous-communication-model)
  - [3.5 Rendering Function Board-Specific Instruction Extensions](#35-rendering-function-board-specific-instruction-extensions)
- [4. Logic Board System Design](#4-logic-board-system-design)
  - [4.1 Core Responsibilities](#41-core-responsibilities)
  - [4.2 Non-Responsibilities](#42-non-responsibilities)
  - [4.3 Device Routing Table Structure](#43-device-routing-table-structure)
- [5. Rendering Function Board: Soft GPU and Multimedia Subsystem](#5-rendering-function-board-soft-gpu-and-multimedia-subsystem)
  - [5.1 Core Positioning and Responsibility Boundaries](#51-core-positioning-and-responsibility-boundaries)
  - [5.2 GPU-Like Fixed-Function Rendering Pipeline (Triangle + Digital Matrix Core)](#52-gpu-like-fixed-function-rendering-pipeline-triangle--digital-matrix-core)
  - [5.3 Full-Featured Multimedia Service Stack](#53-full-featured-multimedia-service-stack)
  - [5.4 Engineering Transformation Scheme Based on RETROGO](#54-engineering-transformation-scheme-based-on-retrogo)
  - [5.5 Dual-Core Scheduling and Performance Optimization System](#55-dual-core-scheduling-and-performance-optimization-system)
- [6. Storage System Design](#6-storage-system-design)
  - [6.1 Local Firmware and Resource Repository (SD Card)](#61-local-firmware-and-resource-repository-sd-card)
  - [6.2 Version and Resource Index Table](#62-version-and-resource-index-table)
- [7. OTA Upgrade and Resource Distribution Mechanism](#7-ota-upgrade-and-resource-distribution-mechanism)
  - [7.1 Extension of Dual-File Evolution System](#71-extension-of-dual-file-evolution-system)
  - [7.2 Standardized Upgrade Process](#72-standardized-upgrade-process)
  - [7.3 Offline Flashing and Resource Preloading](#73-offline-flashing-and-resource-preloading)
  - [7.4 Atomic Rollback Mechanism](#74-atomic-rollback-mechanism)
- [8. Permission Isolation System](#8-permission-isolation-system)
- [9. Current Validation Status](#9-current-validation-status)
- [10. Key Technical Constraints and Countermeasures](#10-key-technical-constraints-and-countermeasures)
- [11. Reserved Architecture Expansion Space](#11-reserved-architecture-expansion-space)
- [License](#license)

---

## Project Overview
### Abstract
This paper proposes a **Distributed Heterogeneous Embedded Computing System Architecture v2.0** built on the ESP32-S3 platform. Building on the four-layer decoupled architecture of v1.0, it adds a rendering function board with general-purpose Software-defined GPU (Soft GPU) capabilities, fully implementing an NVIDIA-like fixed-function rendering pipeline for "triangle primitives + digital matrix transformation", while integrating multimedia capabilities such as MP3 audio, MP4 video, and electronic photo albums.

The architecture consists of four layers: Cloud Brain Layer (local server), Command Layer (Top-Level Board), Hub Layer (Logic Board), and Execution Layer (Function Board), interconnected via high-speed SPI bus. It completely resolves the performance bottleneck of resource contention (CPU, memory, bus) among computing, scheduling, rendering, and peripheral management in single-chip solutions. The system embeds a localized firmware repository and dual-file OTA evolution mechanism, supporting automatic discovery of hot-swappable devices, offline reflashing, A/B partition atomic rollback, and achieving a highly reliable and scalable edge computing cluster with a hierarchical permission chain.

The core breakthrough of v2.0 is: completely decoupling rendering capabilities from general-purpose chips to build an independent embedded universal graphics acceleration node. All graphic output uniformly goes through the GPU-style rendering pipeline, replacing traditional MCU 2D graphics libraries, and providing a universal computing base for subsequent 3D rendering, UI acceleration, and AI visualization.

### Core Values and Advantages
1. **Complete Resource Decoupling**
   Split the four core roles (computing, scheduling, rendering, peripheral management) into independent chips. Each node runs independently without competing for CPU and memory resources; single-module failures do not affect the overall system, significantly improving system stability and performance upper limits.

2. **General-Purpose Soft GPU Capabilities**
   Replicate the GPU fixed-function pipeline with "triangle + digital matrix" as the core, covering all scenarios (2D UI, image transformation, spectrum visualization, video frame output) with a single rendering engine. Break free from the functional limitations of traditional MCU graphics libraries and enable smooth expansion to 3D rendering.

3. **Modular Heterogeneous Expansion**
   Function boards adopt standardized identity descriptors, supporting automatic hot-swappable discovery and binding. They can be mounted with various nodes (rendering, audio, AI acceleration, sensor aggregation, etc.) on demand, and support active-standby hot switching for multiple boards of the same type.

4. **Highly Reliable OTA System**
   Local SD card firmware repository + dual-file evolution + A/B partition rollback support online upgrade and offline reflashing. Automatic rollback to a safe version in case of upgrade failure, balancing iteration efficiency and embedded system reliability.

5. **Layered AI and Self-Evolution Base**
   A layered AI architecture (cloud training, edge execution) combined with full-link structured logging enables continuous iterative evolution of policies and firmware, providing a complete data closed loop for edge adaptive scheduling.

6. **Mature Ecosystem Reuse**
   Based on the mature ESP32 driver framework of RETROGO (with engineering modifications), reuse underlying capabilities such as display, audio, dual-core scheduling, and memory management, significantly reducing development costs and hardware adaptation risks.

### Keywords
ESP32-S3, Distributed System, Heterogeneous Computing, Embedded Soft GPU, Graphics Rendering Pipeline, OTA, Edge Computing, Firmware Repository, Layered AI, RETROGO, Embedded Multimedia

---

## 1. Introduction
### 1.1 Problem Statement
In embedded graphics processing and edge computing scenarios, a single chip typically undertakes four roles simultaneously: computing, scheduling, rendering, and peripheral management. These four roles compete for CPU time, memory bandwidth, and bus resources, leading to limited overall system performance. Modifications to any module may affect global stability.

Especially in multimedia and graphics scenarios, traditional MCU solutions generally have three major pain points:
- Graphics rendering relies on dedicated 2D graphics libraries, only capable of basic geometric drawing, lacking general transformation and acceleration capabilities; the cost of extending functions such as 3D and special effects is extremely high;
- Audio decoding, video decoding, graphics rendering, and business scheduling are coupled in the same chip, resulting in mutual compromise of frame rate, sound quality, and response speed;
- High coupling between firmware and resource upgrades, lack of reliable hierarchical upgrade and rollback mechanisms, leading to high device operation and maintenance costs.

### 1.2 Design Objectives
This architecture separates the four core roles into independent chips and builds a distributed embedded computing cluster through high-speed serial bus interconnection. The v2.0 version further focuses on upgrading graphics rendering capabilities, with core objectives including:
1.  Architecture level: Maintain the four-layer decoupled design with clear responsibility boundaries for each node, and provide modular replaceability and on-demand expansion capabilities;
2.  Rendering level: Implement a GPU-like general-purpose rendering pipeline with triangles as basic primitives and matrix transformation as the core, replacing traditional 2D drawing APIs to build an embedded universal graphics acceleration node;
3.  Multimedia level: Integrate full MP3/MP4/electronic photo album functions, with all graphic output uniformly going through the rendering pipeline to verify the universality and performance of the GPU pipeline;
4.  Operation and maintenance level: Improve the localized firmware and resource repository, retain high-reliability features such as offline reflashing and atomic rollback, and adapt to independent operation in cloud-free scenarios;
5.  Expansion level: Reserve AI logging and scheduling interfaces for the full link, retaining architectural space for subsequent distributed AI closed loops, 3D rendering, and multi-board cascading.

### 1.3 Architecture Overview
The system adopts a four-layer architecture design with strictly isolated responsibilities for each layer, forming a complete decision-scheduling-execution link from top to bottom:

| Layer         | Board/Node                                  | Core Role                                                                 | Functional Analogy                 |
|---------------|---------------------------------------------|---------------------------------------------------------------------------|------------------------------------|
| Cloud Brain   | Local Server (x64 desktop, 24/7 online)     | MCP Host: Deep learning training, policy generation, dual-evolution file compilation, multimedia resource production | Cloud Intelligence / Prefrontal Cortex |
| Command Layer | Top-Level Board (ESP32-S3 + Wi-Fi)          | Run policy AI, aggregate work logs, receive and verify evolution files, decide upgrade timing | Decision Hub / Cerebral Cortex     |
| Hub Layer     | Logic Board (ESP32-S3 SPI Master + SD Card) | Bus enumeration, device discovery, task routing, unified peripheral management, local firmware and resource repository | System Bus / Chipset + Autonomic Nervous System |
| Execution Layer | Function Board (ESP32-S3 SPI Slave)       | Independently complete specialized tasks (rendering, audio, AI acceleration, etc.), generate structured work logs | Dedicated Accelerator / Peripheral Controller |

---

## 2. System Architecture
### 2.1 Cloud Brain Layer: Local Server
The Cloud Brain Layer is an x64 desktop running on the local network, online 24/7. As the Model Context Protocol (MCP) host, it undertakes heavy computing responsibilities and is completely decoupled from the embedded cluster:
- Aggregate structured work logs from all function boards through the Top-Level Board, and perform deep learning training and pattern recognition;
- Generate optimal scheduling policies and operating parameters, compile dual-evolution files: File A (Top-Level Board policy) and File B (Function Board firmware/weights);
- Produce and transcode multimedia resources (resolution-adapted images, audio, MJPEG videos) for unified distribution to the embedded cluster;
- It is the only node in the system with permission to modify the Top-Level Board policy logic, completely decoupling heavy computing from real-time embedded operations.

### 2.2 Three-Layer Physical Isolation of Embedded Cluster
The embedded cluster consists of three layers of physical nodes: Top-Level Board, Logic Board, and Function Board, interconnected hierarchically via SPI bus. The architecture topology is as follows:
```text
┌─────────────────────────────────────────────────────────────────────────────────┐
│                          Local Server (x64 desktop, 24/7 online)                 │
│                    MCP Host: Deep Learning Training + Policy Generation + Resource Compilation                   │
│                              + Dual-Evolution File Compilation                                   │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │ Wi-Fi (MCP Protocol + Resource Distribution)
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│  Top-Level Board (Command Layer)        ESP32-S3 + Wi-Fi                                       │
│  Responsibilities:                                                                        │
│  · Run Policy AI (execute "File A: Top-Level Policy" issued by MCP)                                  │
│  · Aggregate work logs from all Function Boards → forward to MCP Server                                      │
│  · Receive dual-evolution files and media resources from MCP → verify → distribute to Logic Board for SD card writing                 │
│  · Decide "when to upgrade which board, load which resources" (policy decision-making right)                          │
└─────────────────────────────────────────────────────────────────────────────────┘
                                    │ SPI HD (Instruction + OTA Data + Resource Stream Transmission)
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│  Logic Board (Hub Layer)        ESP32-S3 (SPI Master) + High-Speed SD Card (4GB+, as "Local Repository")     │
│  Responsibilities:                                                                        │
│  · Bus Enumeration and Device Discovery (automatic identification and binding of multiple boards)                                      │
│  · Driver Loading and Task Routing (maintain device routing table)                                          │
│  · Scheduling AI (monitor communication bandwidth of each channel, dynamically adjust priority)                                   │
│  · Unified Peripheral Management (SD Card/Buttons/Audio/Power)                                          │
│  · Version Repository Administrator: maintain firmware library and media resource library in SD card                                 │
│  · Offline Reflashing: when replacing a Function Board, directly retrieve the corresponding firmware from SD card for reflashing                            │
│  · Self-Upgrade: read its own firmware from SD card → write to OTA partition → take effect after restart                         │
│  · [FORBIDDEN] Cannot modify Top-Level Board firmware (permission isolation)                                        │
└─────────────────────────────────────────────────────────────────────────────────┘
          │ SPI Bus (CS1)        │ SPI Bus (CS2)        │ SPI Bus (CS3)        │ SPI Bus (CS4)
          ▼                      ▼                      ▼                      ▼
┌──────────────┐        ┌──────────────┐        ┌──────────────┐        ┌──────────────┐
│  Rendering   │        │  Audio        │        │  AI Acceleration │        │  Reserved    │
│  Function    │        │  Function     │        │  Function Board  │        │  Slot        │
│  Board       │        │  Board        │        │  (NPU)           │        │              │
│  (Soft GPU)  │        │  (DSP)        │        │                  │        │              │
│              │        │              │        │                  │        │              │
│ ·General Rendering Pipeline │        │ ·Audio Codec   │        │ ·Neural Network Inference │        │              │
│ ·Multimedia Decoding   │        │ ·DSP Sound Processing  │        │ ·Feature Extraction     │        │              │
│ ·Generate Rendering Logs │        │ ·Generate Audio Logs │        │ ·Generate Computing Logs │        │              │
└──────────────┘        └──────────────┘        └──────────────┘        └──────────────┘
```

### 2.3 Function Board Expansion Mechanism
The Execution Layer is an open ecosystem of function boards. Execution boards with different functions can be mounted according to product requirements. The system supports multiple function boards of the same type to stand by simultaneously during operation, with unified scheduling and active-standby switching by the Logic Board:
- **Rendering Board**: General-purpose Soft GPU graphics acceleration, multimedia decoding, display output, undertaking all graphics rendering tasks;
- **Audio Board**: Audio codec, DSP sound processing, multi-channel output, independently undertaking audio computing;
- **AI Acceleration Board**: Neural network inference acceleration, sensor data preprocessing, feature extraction;
- **Sensor Aggregation Board**: Multi-channel sensor data collection, filtering, preprocessing, and reporting;
- **Custom Function Board**: Any custom board complying with the standard identity descriptor protocol.

### 2.4 Architecture Design Principles
1.  The Top-Level Board is unaware of the existence of Function Boards, only communicates with the Logic Board, and does not directly issue any Execution Layer instructions;
2.  Function Boards are unaware of each other's existence, only respond to bus instructions, and have no direct inter-board communication;
3.  The Logic Board acts as the only hub node, does not parse business content, and only performs routing, scheduling, and general peripheral management;
4.  Any Function Board can be automatically discovered, driver-bound, and resource-matched by providing a standard identity descriptor;
5.  The Local Server runs independently of the embedded cluster, does not directly control any board, and only communicates with the Top-Level Board;
6.  All Execution Layer boards only undertake atomic execution responsibilities, do not maintain business state machines, and state and scheduling are uniformly managed by upper layers.

---

## 3. Inter-Chip Communication Protocol Stack
### 3.1 Physical Layer Specifications
| Item         | Specification Parameters                                   |
|--------------|------------------------------------------------------------|
| Physical Interface | SPI Slave HD (Half-Duplex), 1-bit data line                |
| Clock Frequency | 20 MHz (dupont wire scenario), 40MHz verifiable for PCB version   |
| Effective Bandwidth | ~2.4 MB/s @20MHz                                         |
| Topology     | One Master (Logic Board) + Multiple Slaves (Function Boards), addressed via independent CS pins |
| Maximum Single DMA Transfer | 4092 bytes                                       |
| DMA Memory Limit | Only writable to internal SRAM, not directly to PSRAM      |

### 3.2 Protocol Frame Format
A unified frame format is adopted to cover all scenarios (instruction, OTA, storage, query), with the frame structure defined as follows:
```
[HEADER 2B] [TARGET_ID 1B] [FRAME_TYPE 1B] [CMD 1B] [LEN 2B] [PAYLOAD N] [CHECKSUM 1B]
```

| Field       | Description                                          |
|-------------|------------------------------------------------------|
| HEADER      | Frame start flag, fixed value `0xAA55`               |
| TARGET_ID   | Target board ID: `0x00`=Broadcast, `0x01~0xFE`=Specific board |
| FRAME_TYPE  | Frame Type: `0x01`=Real-Time Instruction / `0x02`=OTA Data Block / `0x03`=SD Card Storage / `0x04`=Version Query / `0x05`=Media Data Stream |
| CMD         | Instruction code, valid for real-time instruction and media stream scenarios |
| LEN         | PAYLOAD data length, stored in big-endian order      |
| PAYLOAD     | Data payload, length specified by the LEN field       |
| CHECKSUM    | XOR checksum of the entire frame                     |

### 3.3 Device Discovery and Identity Descriptor
1.  After power-on, the Logic Board sends broadcast query instructions through each CS pin in sequence;
2.  Slave Function Boards reply with a 64-byte fixed-format identity descriptor, including basic information and capability descriptions;
3.  The Logic Board parses the descriptor, registers the device in the routing table, and loads the corresponding driver and matching resources.

**v2.0 Extension: Rendering Board Capability Fields**
GPU and multimedia capability bitmaps are added to the descriptor, including supported primitive types, maximum vertex count, texture resolution upper limit, decoding capabilities, maximum frame rate, and other parameters. The Logic Board can schedule rendering tasks and match resources based on these parameters.

### 3.4 Asynchronous Communication Model
SPI is a synchronous protocol, and slaves need to return data via MISO driven by the clock. The system implements application-layer asynchronous semantics through the following strategies:
1.  After sending an instruction frame, the Logic Board does not block to wait for a reply and continues to execute subsequent scheduling tasks;
2.  The Logic Board periodically inserts "virtual read transactions" (sending empty instruction frames) to bring back pending upload logs and status data from Function Boards "along the way";
3.  Pending upload data of Function Boards is pre-stored in the MISO transmit register, waiting for the next read transaction to trigger return;
4.  High-priority status (e.g., exceptions, upgrade completion) can be marked as emergency events and reported with priority in the next virtual read transaction.

### 3.5 Rendering Function Board-Specific Instruction Extensions
Under `FRAME_TYPE=0x01 (Real-Time Instruction)` and `FRAME_TYPE=0x05 (Media Data Stream)`, a set of rendering board-specific instructions are added:

| FRAME_TYPE | CMD Value | Instruction Name         | PAYLOAD Content Description                                 |
|------------|-----------|--------------------------|------------------------------------------------------------|
| 0x01       | 0x10      | Submit Drawing Command   | Transformation matrix parameters + vertex list + texture configuration |
| 0x01       | 0x11      | Frame Buffer Refresh     | No payload, trigger LCD DMA to output the current frame     |
| 0x01       | 0x12      | Rendering Parameter Configuration | Clear color, blending mode, clipping area, and other global parameters |
| 0x01       | 0x20      | Start Media Playback     | Media type + resource ID + playback parameters              |
| 0x01       | 0x22      | Playback Control Command | Control words for play/pause/next track/fast forward/volume adjustment |
| 0x05       | 0x21      | Media Data Stream Packet | Fragmented audio/video stream data, supporting edge transmission, edge decoding, and edge rendering |

---

## 4. Logic Board System Design
### 4.1 Core Responsibilities
- **Bus Enumeration and Device Discovery**: Automatically scan all CS slots after power-on, identify Function Board types and capabilities, and complete device registration;
- **Device Routing Table Maintenance**: Dynamically maintain the status, capabilities, driver entries of all online devices, and support hot-swapping event processing;
- **Instruction Routing and Distribution**: Receive instructions from the Top-Level Board, distribute to corresponding Function Boards according to target ID and device type, and support broadcasting;
- **Unified Peripheral Management**: Unified management and scheduling of global peripherals such as SD card file system, key scanning, and power monitoring;
- **Firmware and Resource Repository Management**: Maintain the firmware version library and media resource library in the SD card, supporting version query and verification;
- **Exception Handling and Degradation**: Device timeout retry, fault offline, active-standby board automatic switching to ensure system availability;
- **Offline Reflashing Execution**: When a new device is inserted, automatically match the latest stable firmware of the corresponding type in the SD card and complete the flashing process without upper-layer intervention.

### 4.2 Non-Responsibilities
- Do not execute business logic calculation or policy reasoning (responsibility of the Top-Level Board);
- Do not process graphics rendering, audio decoding, or neural network inference (responsibility of Function Boards);
- Do not block to wait for any external confirmation; all operations adopt an asynchronous non-blocking model;
- Do not write any data to the Top-Level Board, strictly complying with permission isolation rules.

### 4.3 Device Routing Table Structure
The Logic Board maintains up to 4 device slots, with complete device information recorded for each slot:

| Field           | Description                  |
|-----------------|------------------------------|
| TARGET_ID       | Device ID (1~4, corresponding to CS pins) |
| Device Type     | Rendering Board/Audio Board/AI Acceleration Board, etc. |
| CS Pin Number   | Corresponding GPIO port number |
| Device Status   | Empty/Active/Offline/OTA In Progress/Fault |
| Unique ID       | Last 6 bytes of the chip MAC address |
| Current Firmware Version | Used for version verification and upgrade judgment |
| Capability Bitmap | Functions and performance parameters supported by the device |
| Driver Function Pointer | Entry for business instruction dispatch |
| Scheduling Priority | Dynamically adjusted bus access priority |

---

## 5. Rendering Function Board: Soft GPU and Multimedia Subsystem
### 5.1 Core Positioning and Responsibility Boundaries
The Rendering Function Board is a general-purpose graphics acceleration node in the Execution Layer. It **only performs rendering execution, does not perform business scheduling, and does not maintain business states**. All control instructions, playlists, and UI logic are issued by upper layers via the SPI bus, reserving seamless docking capabilities for the Top-Level Board to take over all CPU business logic in the future.

#### Core Responsibilities
- Execution of general graphics rendering pipeline: full process of vertex transformation, triangle assembly, rasterization, and fragment processing;
- Multimedia decoding: decoding of image, audio, and video formats, output to the rendering pipeline and audio interface;
- Display output driving: LCD screen DMA refresh, double buffering switching, display parameter configuration;
- Status reporting: structured logs of rendering frame rate, decoding time consumption, bus bandwidth, exception status, etc.;
- OTA firmware reception: receive firmware fragments via SPI, complete self-upgrade and rollback.

#### Non-Responsibilities
- Do not maintain business data such as playlists or album directories; all resources are scheduled by upper layers;
- Do not execute business logic judgment or UI state machines; all operations are instruction-driven;
- Do not directly access the SD card; all data is issued by the Logic Board via the bus;
- Do not manage other Function Boards; only respond to bus instructions for its own CS pin.

### 5.2 GPU-Like Fixed-Function Rendering Pipeline (Triangle + Digital Matrix Core)
Replicate the classic GPU fixed-function pipeline architecture, with **triangles as the only basic primitive** and **MVP matrix transformation as the core computing logic**. A single pipeline covers all 2D/3D graphics rendering needs, aligning with the design paradigm of early NVIDIA fixed pipelines.

#### 5.2.1 Pipeline Overview
```
Vertex Data Input → Matrix Transformation (Digital Matrix) → Primitive Assembly (Triangle) → Rasterization → Fragment Processing → Frame Buffer Output
```

#### 5.2.2 Digital Matrix: Vertex Transformation Unit
Corresponding to the GPU vertex shader function, coordinate space transformation is implemented through the **MVP (Model-View-Projection) Matrix**, which is the core implementation of the "Digital Matrix":
- **Model Matrix**: Controls translation, rotation, and scaling of graphics. All 2D transformations (image scaling/rotation, UI animation) are implemented through matrix calculation, eliminating the need to write separate transformation algorithms;
- **View Matrix**: Controls the position and orientation of the virtual camera, supports perspective translation and rotation, and provides native support for 3D scenes and scrolling UI;
- **Projection Matrix**: Adopts orthographic projection for 2D mode and can switch to perspective projection for 3D mode, with a single pipeline compatible with both modes.

**Engineering Optimization Design**:
- Adopt 16.16 fixed-point arithmetic as the main method to balance precision and performance, improving rasterization speed by more than 30% compared to pure floating-point arithmetic;
- Support matrix pre-merging: model, view, and projection matrices are pre-merged into a total transformation matrix, and each vertex only executes one matrix multiplication;
- Instruction-level input: upper layers directly issue matrix parameters and vertex lists; the Rendering Board does not persist matrix states to ensure stateless execution.

#### 5.2.3 Triangle Primitive Assembly
With triangles as the only basic primitive, all graphics are decomposed into triangle drawing, supporting three primitive topologies:
- Independent Triangles: suitable for discrete graphics drawing;
- Triangle Strips: suitable for continuous surface and rectangle drawing (1 rectangle = 2 triangles);
- Triangle Fans: suitable for circular, polygonal, and ring visualization drawing.

The assembly logic receives vertex sequences, automatically assembles them into triangle units according to topology type, and sends them to the rasterization stage; it supports back-face culling and view frustum culling to automatically discard off-screen and back-facing triangles, reducing invalid computation.

#### 5.2.4 Rasterization Engine
Converts vector triangles into screen pixel coordinates, which is the performance core of the Soft GPU. It is deeply optimized for ESP32-S3 hardware characteristics:
- Adopt the **Edge Function Scanline Algorithm**, using all-integer arithmetic to judge whether pixels are inside triangles, avoiding floating-point arithmetic overhead;
- Adopt **Tile-Based Rendering**: divide the screen into 16x16 pixel tiles, render tile by tile, with core data resident in internal SRAM, significantly reducing frequent PSRAM access (resolving PSRAM bandwidth bottlenecks);
- Support integer coordinates and sub-pixel precision, configurable to balance performance and image quality;
- Support hardware clipping to automatically skip off-screen pixel tiles.

#### 5.2.5 Fragment Processing Unit
Corresponding to the GPU fragment shader function, lightweight implementation of core pixel processing capabilities:
- Solid color filling, texture sampling, supporting nearest-neighbor and bilinear filtering modes;
- Basic special effects such as Alpha transparency blending, color overlay, and brightness adjustment;
- Reserved expansion interfaces: support for custom fragment shaders in the future to implement advanced effects such as filters and light/shadow.

### 5.3 Full-Featured Multimedia Service Stack
All graphic output of multimedia functions uniformly goes through the unified Soft GPU pipeline, verifying the universality of the pipeline while ensuring architectural consistency.

#### 5.3.1 Electronic Photo Album
- Decoding Capabilities: Support JPEG (using ESP-IDF hardware-accelerated decoding) and PNG formats, with maximum resolution adapted to screen size;
- Rendering Method: Decoded images are used as textures, and translation, scaling, rotation, and page-turning transition animations are implemented through model matrices, with all transformations uniformly calculated by the GPU pipeline;
- Expansion Capabilities: Support multi-image simultaneous display, thumbnail grids, gradient transition effects, all implemented through triangle vertices and matrix animations.

#### 5.3.2 MP3 Audio Playback + Spectrum Visualization
- Decoding Scheme: Adopt the Helix MP3 decoder, capable of real-time decoding of 128kbps/320kbps MP3 with a single thread;
- Audio Output: I2S interface DMA double-buffered output, supporting volume adjustment and channel configuration;
- Visualization Rendering: Perform FFT spectrum analysis on decoded PCM data to generate spectrum/waveform data, convert to triangle vertex sequences, and send to the GPU pipeline for rendering. Support multiple styles such as bar spectrum, ring visualization, and waveform display.

#### 5.3.3 MP4 Video Playback
In view of the ESP32-S3's lack of hardware video decoding, the **MJPEG-encapsulated MP4** scheme is adopted to balance compatibility and performance:
- Video Stream: MJPEG frame-by-frame JPEG hardware-accelerated decoding, with decoded frames used as textures sent to the GPU pipeline, supporting scaling, cropping, and full-screen adaptation;
- Audio Stream: MP3 audio track, sharing the decoding path with pure audio playback to achieve precise audio-video synchronization;
- Performance Target: Stable 24fps at 320x240 resolution, meeting embedded multimedia scenario requirements;
- Reserved Expansion: Support for MPEG-1 software decoding or lightweight video decoding acceleration using AI accelerators can be added in the future.

### 5.4 Engineering Transformation Scheme Based on RETROGO
Based on the mature ESP32 underlying framework of RETROGO (with engineering modifications), reuse its verified driver and scheduling capabilities to significantly reduce development costs and hardware adaptation risks.

#### 5.4.1 Fully Reused Core Capabilities
- **Display Driver Framework**: Compatible with mainstream SPI screens such as ST7789/ILI9341/GC9A01, supporting DMA refresh, double buffering switching, and pixel format adaptation;
- **I2S Audio Driver**: Mature and stable DMA double-buffered audio output, with configurable sampling rate, bit width, and channels;
- **PSRAM Memory Management**: Dynamic allocation of frame buffers and decoding buffers to solve memory fragmentation issues;
- **Dual-Core Scheduling Framework**: Mature architecture with parallel division of labor between PRO core and APP core, with complete inter-core communication and synchronization mechanisms;
- **Hardware Abstraction Layer**: Unified encapsulation of peripherals such as GPIO, SPI, and timers to reduce the cost of hardware changes.

#### 5.4.2 Replaced and Modified Modules
| RETROGO Native Module       | v2.0 Transformation Direction                                                                 |
|------------------------------|----------------------------------------------------------------------------------------------|
| Emulator Core (NES/GBA, etc.) | Completely removed and replaced with "Soft GPU Rendering Engine + Multimedia Decoder Cluster" |
| Local ROM File Reading Module | Modified to SPI streaming input: all firmware and media data are issued by the Logic Board via SPI, supporting edge transmission, edge decoding, and edge rendering |
| Local Key Input Processing    | Modified to SPI instruction-driven: all control instructions come from the bus; local keys are only retained for debugging and not included in business logic in official versions |
| Native 2D Drawing API         | All replaced with Soft GPU pipeline interfaces, with all graphic output uniformly going through the rendering pipeline |
| Menu and UI System            | Remove business logic and state machines, only retain instruction-driven UI rendering capabilities, with UI data issued by upper layers |

### 5.5 Dual-Core Scheduling and Performance Optimization System
Deeply optimize for the ESP32-S3 dual-core architecture, reasonably allocate rendering, decoding, communication, and IO to maximize hardware utilization.

1.  **Fixed Dual-Core Division of Labor**
    - PRO Core (Core 0): SPI communication protocol parsing + full Soft GPU rendering pipeline + LCD DMA refresh, responsible for graphics computing and bus interaction;
    - APP Core (Core 1): Multimedia decoding (audio/video/image) + FFT operation + data stream preprocessing, responsible for compute-intensive tasks;
    - The two cores transfer instructions, data, and synchronization signals through queues, with zero shared memory conflicts to ensure parallel efficiency.

2.  **Three-Level Memory Optimization**
    - Internal SRAM: Stores rasterization Tile cache, matrix operation stack, DMA buffer, and core variables, pursuing the lowest latency;
    - PSRAM: Stores double frame buffers, texture data, and decoding buffers, carrying large-capacity and high-bandwidth data;
    - Cache Prefetching: Enable SPI Flash and PSRAM cache, prefetch texture and instruction data to Cache to significantly improve access speed.

3.  **Three-Level Rendering Pipeline**
    Adopt a "Decoding → Rasterization → DMA Refresh" three-level pipeline architecture: while the current frame is output via DMA, the GPU renders the next frame, and decodes the data of the frame after next in parallel. The three stages execute in parallel, increasing the overall frame rate by more than 30%.

---

## 6. Storage System Design
### 6.1 Local Firmware and Resource Repository (SD Card)
The Logic Board is mounted with a high-speed SD card (4GB or larger recommended) as the system's "Local Firmware + Media Resource Repository". v2.0 expands the media resource directory with the complete directory structure as follows:
```text
SD Card Root Directory (/)
├── /system/                      # System Firmware Directory
│   ├── logic_firmware_v2.1.0.bin    # Logic Board firmware
│   ├── logic_firmware_v2.0.9.bin    # Historical stable version (for rollback)
│   └── rollback_flag.txt            # Rollback flag file (read when watchdog is triggered)
│
├── /devices/                     # Function Board Firmware Directory
│   ├── /renderer/                    # Rendering Board Firmware
│   │   ├── v3.2.0.bin               # Latest version firmware
│   │   ├── v3.1.5.bin               # Historical stable version
│   │   └── device_caps.json         # Capability description file for this device type
│   ├── /audio/                       # Audio Board Firmware
│   │   ├── v2.0.3.bin
│   │   └── v1.9.8.bin
│   ├── /ai_accel/                    # AI Acceleration Board Firmware
│   │   └── v1.0.1.bin
│   └── /unknown/                     # Temporary firmware staging area for unknown devices
│
├── /top_level/                   # Top-Level Board Policy File Directory
│   └── top_strategy_v5.bin          # Top-Level Board policy file (stored only by Logic Board, no write permission to the board)
│
├── /media/                       # Multimedia Resource Directory (new in v2.0)
│   ├── /images/                      # Photo Album Image Resources
│   ├── /audio/                       # MP3 Audio Resources
│   ├── /video/                       # MJPEG Video Resources
│   └── /ui/                          # UI Textures, Icons, Font Resources
│
└── /ota_logs/                    # Upgrade and Operation Log Directory
    ├── upgrade_history.log          # Records of all upgrade operations
    └── fallback_events.log          # Records of rollback and exception events
```

### 6.2 Version and Resource Index Table
The Logic Board maintains two index tables in memory, loaded from the SD card at startup and dynamically updated during operation:

#### Firmware Version Index Table
Records all available firmware versions, checksums, and file information for each device type, supporting:
- Query of the latest stable version/specified version by device type;
- CRC32 firmware integrity verification;
- Version number comparison and upgrade judgment.

```c
typedef struct {
    uint8_t device_type;        // Device type
    uint8_t major, minor, patch; // Semantic version number
    uint32_t file_offset;       // SD card file offset address
    uint32_t file_size;         // File size
    uint32_t crc32;             // Firmware integrity checksum
    uint8_t is_stable;          // 1=Stable version, 0=Beta version
} version_entry_t;
```

#### Media Resource Index Table (new in v2.0)
Records the ID, type, path, parameters, and checksum of all multimedia resources, supporting:
- Fast query and positioning by resource ID;
- Resource integrity verification;
- Rendering Board capability matching (resolution, format adaptation).

---

## 7. OTA Upgrade and Resource Distribution Mechanism
### 7.1 Extension of Dual-File Evolution System
Based on the original dual-file system, v2.0 expands media resource distribution capabilities to form three types of upgrade/distribution objects:

| File/Resource Type | Target Object       | Content                          | Distribution Path                                  |
|--------------------|---------------------|-----------------------------------|---------------------------------------------------|
| File A (Top-Level Policy) | Top-Level Board     | ESP-DL model weights + policy rules | MCP → Applied directly by Top-Level Board (bypassing Logic Board) |
| File B (Firmware Image) | Logic Board/Function Board | Complete firmware binary | MCP → Top-Level Board → Logic Board → SD Card Repository → Target Board |
| File C (Media Resource) | Shared by the entire cluster | Images, audio, video, UI resources | MCP → Top-Level Board → Logic Board → SD Card Media Directory |

### 7.2 Standardized Upgrade Process
1.  The Top-Level Board receives new firmware/policy/resource files from the server via Wi-Fi;
2.  After verifying the file integrity and version validity, the Top-Level Board issues them to the Logic Board;
3.  The Logic Board writes the files to the corresponding directories in the SD card and updates the corresponding index tables;
4.  The Top-Level Board issues upgrade/load instructions to the Logic Board at an appropriate time according to the operation policy;
5.  The Logic Board reads the corresponding firmware from the SD card and blindly forwards it to the target Function Board via SPI;
6.  After reception, the Function Board writes to the OTA backup partition and takes effect after soft reset; media resources are directly read by the Function Board on demand.

### 7.3 Offline Flashing and Resource Preloading
- **Function Board Offline Reflashing**: After replacing a faulty Function Board, the new board is inserted into the bus and reports its identity descriptor. The Logic Board automatically matches the latest stable firmware of the corresponding type in the SD card, automatically triggers the reflashing process without server or Top-Level Board intervention, and automatically registers online after flashing is completed;
- **Resource Preloading**: Multimedia resources can be pre-written to the SD card, and the system can directly call them upon power-on without cloud distribution, adapting to fully offline scenarios.

### 7.4 Atomic Rollback Mechanism
All boards (including the Logic Board itself) adopt the A/B partition OTA strategy to ensure atomic rollback in case of upgrade failure:
1.  The new firmware is written to the backup partition without affecting the currently running main partition;
2.  After the upgrade and startup, if normal heartbeat and communication are not restored within 10 seconds, the hardware watchdog is triggered;
3.  The system automatically points the startup flag back to the main partition and rolls back to the safe state before the upgrade;
4.  Rollback events are automatically recorded in logs and reported to the Top-Level Board and server.

---

## 8. Permission Isolation System
The system establishes a strict hierarchical permission chain with permissions decreasing from top to bottom, ensuring system security at both the physical and firmware layers:
`MCP Server > Top-Level Board > Logic Board > Function Board`

| Operation Permission           | Permission Attribution and Description                                  |
|--------------------------------|-----------------------------------------------------------------------|
| Upgrade Top-Level Board Policy | Directly issued by the MCP Server, verified and applied by the Top-Level Board |
| Instruct Logic Board Upgrade   | The Top-Level Board can issue instructions, and the Logic Board reads and executes from the SD card |

---

## 9. Current Validation Status
*(Content to be supplemented based on actual project progress)*

## 10. Key Technical Constraints and Countermeasures
*(Content to be supplemented based on actual project progress)*

## 11. Reserved Architecture Expansion Space
*(Content to be supplemented based on actual project progress)*

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
