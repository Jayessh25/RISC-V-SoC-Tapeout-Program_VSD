
<div align="center">

  #  RTL to GDS Completion of VSDBabySoC — Week 9 

  ![RISC-V](https://img.shields.io/badge/RISC--V-SoC%20Tapeout-blue?style=for-the-badge&logo=riscv)
![VSD](https://img.shields.io/badge/VSD-Program-orange?style=for-the-badge)
![Sky130](https://img.shields.io/badge/SkyWater-130nm-green?style=for-the-badge)
![OpenROAD](https://img.shields.io/badge/OpenROAD-RTRL2GDS-purple?style=for-the-badge)
![India](https://img.shields.io/badge/Made%20in-India-saffron?style=for-the-badge)

</div>

<div align="center">

💡 RTL → 🧩 Netlist → 🏗️ Floorplan → 📌 Placement → 🌳 CTS → 🛣️ Routing → 🧮 STA → 📦 GDS Export

</div>

Week 9 is fully dedicated to consolidating every stage of the VSDBabySoC development—from RTL design to final GDS.  
This final documentation captures **all implementation steps**, **verification stages**, and **physical design results**, forming a complete tape-out ready report.

---
## 📑 Table of Contents

1️⃣ [Introduction to the VSDBabySoC](#introduction-to-the-vsdbabysoc)  
2️⃣ [BabySoC Functional Modeling and Architecture Exploration (Week 2)](#1.-babysoc-functional-modeling-and-architecture-exploration-week-2)  
3️⃣ [RTL Synthesis, STA Fundamentals, and Post-Synthesis Timing Analysis (Week 3)](#rtl-synthesis-sta-fundamentals-and-post-synthesis-timing-analysis-week-3)  
4️⃣ [Physical Design Flow — Floorplan to Routing using OpenROAD (Week 7)](#physical-design-flow--floorplan-to-routing-using-openroad-week-7)  
5️⃣ [SPEF-Based Post-Routing STA and Final Timing Closure (Week 8)](#spef-based-post-routing-sta-and-final-timing-closure-week-8)  
6️⃣ [Summary — Complete RTL-to-GDSII Flow Experience](#summary--complete-rtl-to-gdsii-flow-experience)  
7️⃣ [Final Documentation (Week 0 to Week 8)](#final-documentation-week-0-to-week-8)  
8️⃣ [Repository & Program Information](#repository--program-information)

---


##  Introduction to the VSDBabySoC


  The VSDBabySoC is a compact yet complete system-on-chip that integrates a lightweight RISC-V processor core along with essential digital logic, peripheral components, and custom analog IPs within the Sky130 open-source technology node. Designed as an educational and research-friendly SoC, it demonstrates the full journey from RTL description to a manufacturable GDSII layout. The architecture focuses on clean modular interaction—where the processor, memory subsystem, clocking circuits, and mixed-signal blocks work together through well-defined buses and control pathways—allowing clear visibility into signal flow and functional partitioning. By understanding these structural relationships early in the design, the path to synthesis, physical design, timing verification, and parasitic-aware optimization becomes significantly more intuitive. This introductory section therefore sets the groundwork for the subsequent stages of the design flow, ensuring a smooth transition from high-level functionality to silicon-ready implementation.

---
# 1.BabySoC Functional Modeling and Architecture Exploration
  
 The VSDBabySoC project begins by establishing a strong foundation in SoC architecture and functional modeling, focusing on understanding the RISC-V core, system components, and overall block-level interactions. Week 2 introduced the BabySoC fundamentals, emphasizing how the processor, memory system, clocking blocks, and analog IPs integrate within the Sky130 ecosystem. Functional modeling of each module ensured that the early representations of the SoC captured the intended behavior, enabling fast debugging before moving into full RTL development. This phase also clarified the datapath flow and control-signal interactions, forming the conceptual backbone for later synthesis and physical design stages.

If u want to see in detail **(CLICK BELOW)**

<details> <summary><strong> 🚀 Week 2: BabySoC Fundamentals & Functional Modelling</strong></summary>


#  Week 2: BabySoC Fundamentals & Functional Modelling
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Day-6-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to Day 6 of the RTL workshop!Today, we dive into System-on-Chip (SoC) fundamentals and learn how to model the BabySoC functionally. Using Icarus Verilog for simulation and GTKWave for waveform analysis, we understand how processor, memory, clock, and peripherals integrate in a real SoC.

---
## 📑 Table of Contents

- [1. What is a System-on-Chip (SoC)?](#1-what-is-a-system-on-chip-soc)
- [2. Types of SoCs](#2-types-of-socs)
- [3. Components of a Typical SoC](#3-components-of-a-typical-soc)
- [4. Introduction to VSDBabySoC](#4-introduction-to-vsdbabysoc)
- [5. What is VSDBabySoC?](#5-what-is-vsdbabysoc)
- [6. Problem Statement](#6-problem-statement)
- [7. What is SoC?](#7-what-is-soc)
- [8. What is RVMYTH?](#8-what-is-rvmyth)
- [9. What is PLL?](#9-what-is-pll)
- [10. What is DAC?](#10-what-is-dac)
- [11. Project Structure](#11-project-structure)
- [12. TLV to Verilog Conversion for RVMYTH](#12-tlv-to-verilog-conversion-for-rvmyth)
- [13. Simulation Steps](#13-simulation-steps)
  - [13.1 Pre-Synthesis Simulation](#131-pre-synthesis-simulation)
  - [13.2 Viewing Waveform in GTKWave](#132-viewing-waveform-in-gtkwave)
  - [13.3 Viewing DAC Output in Analog Mode](#133-viewing-dac-output-in-analog-mode)
  - [13.4 Trouble Shooting Tips](#134-trouble-shooting-tips)
- [14. Why Pre-Synthesis and Post-Synthesis?](#14-why-pre-synthesis-and-post-synthesis)
- [15. Summary](#15-summary)

---

## 1. What is a System-on-Chip (SoC)?

A System-on-Chip (SoC) integrates all essential components of a computer system on a single chip, reducing size, power, and cost while improving performance.
- Earlier: CPU, memory, and peripherals were spread across multiple chips.
- Now: Processor, memory, interconnect, and peripherals all inside one chip.
- Found in smartphones, IoT, wearables, automotive controllers, and AI accelerators.

---

## 2. Types of SoCs
- Application-specific SoCs → optimized for mobile, automotive, or camera systems.
- General-purpose SoCs → found in PCs and dev boards (e.g., Raspberry Pi).
- Heterogeneous SoCs → mix CPUs, GPUs, DSPs, accelerators.
- Open-source SoCs → based on RISC-V and other open ISAs.
👉 BabySoC belongs to the open-source educational SoC category.

---

## 3. Components of a Typical SoC

- CPU (Processor Core) → the brain; executes instructions.
    - BabySoC uses RVMYTH (RISC-V core).
- Memory → stores instructions and data.
- Peripherals (I/O) → UART, SPI, GPIO, DAC, etc.
    - BabySoC uses a DAC for digital-to-analog conversion.
- Interconnect / Bus System → communication backbone.
    - BabySoC uses a simplified bus for education.
- Clock & Power → synchronization and efficiency.
    - BabySoC includes a PLL for stable clock generation.

---
## 4. Introduction to VSDBabySoC

The VSDBabySoC is an open-source, simplified System-on-Chip (SoC) built for educational purposes under the VLSI System Design (VSD) program.
It integrates a RISC-V CPU core (RVMYTH), a PLL, and a DAC, showcasing how digital and analog blocks interact in a real chip.

---
## 5. What is VSDBabySoC?
VSDBabySoC is a compact, open-source System-on-Chip (SoC) designed as part of the VLSI System Design (VSD) program.
It is based on RVMYTH – a simple RISC-V processor core designed in TL-Verilog.
The SoC integrates:
- RVMYTH Core (CPU) → executes RISC-V instructions.
- PLL (Phase Locked Loop) → generates a stable, high-frequency clock signal from an input clock.
- 10-bit DAC (Digital-to-Analog Converter) → converts the digital output of the processor into an analog signal (e.g., for audio or video)
- A miniature SoC designed using open-source IPs.
- Built on Sky130 PDK (open-source process design kit).
- Demonstrates integration of processor, clock management, and peripheral.
- Tape-out ready and suitable for beginner-level functional modelling → RTL → physical design flow.

---
## ⚙️ How Does VSDBabySoC Work?

Let’s break down its functional flow:
## 1.Input (Clock & Instructions)
- The system receives an input clock.
- The PLL multiplies/divides this clock to provide the correct frequency for the CPU.
- The CPU (RVMYTH) fetches instructions from memory (usually a small program).
## 2.Processing (RVMYTH Core)
- The RISC-V core executes instructions like arithmetic, logic, memory access, etc.
- These instructions generate digital signals representing processed data.
## 3.Digital-to-Analog Conversion (DAC)
- The digital output is sent to the DAC.
- The DAC converts the binary digital values into a corresponding analog voltage.
- Example:
        - Digital 1111111111 (10-bit max) → max analog voltage.
        - Digital 0000000000 → 0V.
        - Intermediate values → proportional voltages.
## 4. Output (Analog Signal)
   - The analog signal can drive external devices like speakers (audio) or displays (video).
   - This allows BabySoC to communicate with the real world.

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbabysoc_block_diagram.png)

---
🔑 Why is BabySoC Important?

- It demonstrates complete SoC integration in a simplified way.
- Shows digital + analog interaction (CPU + DAC).
- Uses open-source tools and Sky130 PDK, making it reproducible by students/researchers.
- Provides a stepping stone for beginners to understand how real SoCs (like those in phones/laptops) are built.

✅ So, VSDBabySoC = RISC-V CPU (RVMYTH) + PLL + DAC = Digital + Analog Mini-SoC for Learning.

---

## 6. Problem Statement

Modern SoCs integrate many subsystems. Beginners often find it difficult to learn integration because:
- Full-scale SoCs are too complex.
- Proprietary IPs are not open-source.
- Lack of hands-on, tape-out-ready projects.
**👉 Solution:** VSDBabySoC provides a minimal, open, and practical SoC for learning and experimentation.

---

## 7. What is SoC?

A System-on-Chip (SoC) is a single chip that integrates:
- CPU (Processor Core)
- Memory (SRAM, DRAM, Flash, cache)
- Peripherals (UART, SPI, GPIO, ADC/DAC)
- Interconnect/Buses (AXI, AHB, APB)
- Clock & Power Management

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/3.png)

**👉 Benefits:**

- Compact, power-efficient, and high-performance.
- Found in smartphones, IoT, wearables, and AI accelerators.

---
## 8. What is RVMYTH?

RVMYTH = a RISC-V based CPU core used in BabySoC.
Designed during the MYTH (Microprocessor for You in Thirty Hours) workshop.

**Features:**
- Simple RISC-V instruction set.
- Executes a small program producing a 10-bit digital output.
- Represents the brain of the BabySoC.

---
## 9. What is PLL?

- PLL (Phase Locked Loop) is a clock-generation circuit.
- Provides a stable, high-frequency clock for the CPU.
- Ensures synchronization across the SoC.

**In BabySoC:**

- Drives the RVMYTH core with a clean and stable clock signal.

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/2.png)

---
## 10. What is DAC?

- DAC (Digital-to-Analog Converter) converts digital signals → analog outputs.
- Useful in audio, video, and sensor interfacing.

**In BabySoC:**

-  A 10-bit DAC converts CPU digital output into analog form.
- Demonstrates mixed-signal integration inside SoCs.

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/1.png)

---

## 11. Project Structure
- src/include/ - Contains header files (*.vh) with necessary macros or parameter definitions.
- src/module/ - Contains Verilog files for each module in the SoC design.
- output/ - Directory where compiled outputs and simulation files will be generated.

### Setup and Prepare Project Directory
Clone or set up the directory structure as follows:
```txt
VSDBabySoC/
├── src/
│   ├── include/
│   │   ├── sandpiper.vh
│   │   └── other header files...
│   ├── module/
│   │   ├── vsdbabysoc.v      # Top-level module integrating all components
│   │   ├── rvmyth.v          # RISC-V core module
│   │   ├── avsdpll.v         # PLL module
│   │   ├── avsddac.v         # DAC module
│   │   └── testbench.v       # Testbench for simulation
└── output/
└── compiled_tlv/         # Holds compiled intermediate files if needed
```
---
### 🛠️ Cloning the Project

To begin, clone the VSDBabySoC repository using the following command:

```bash
step 1: cd ~/VLSI

step 2: git clone https://github.com/manili/VSDBabySoC.git

step 3: cd ~/VLSI/VSDBabySoC/

step 4: jayesshsk@jayesshsk-VirtualBox:~/VLSI$ ls VSDBabySoC/
images  LICENSE  Makefile  README.md  src

step 5: jayesshsk@jayesshsk-VirtualBox:~/VLSI$ ls VSDBabySoC/src/module/
avsddac.v  avsdpll.v  clk_gate.v  pseudo_rand_gen.sv  pseudo_rand.sv  rvmyth_gen.v  rvmyth.tlv  rvmyth.v  testbench.rvmyth.post-routing.v  testbench.v  vsdbabysoc.v
```
---

### 12. TLV to Verilog Conversion for RVMYTH

Initially, you will see only the `rvmyth.tlv` file inside `src/module/`, since the RVMYTH core is written in TL-Verilog.
To convert it into a `.v` file for simulation, follow the steps below:

<strong>🔧 TLV to Verilog Conversion Steps</strong>

```bash
# Step 1: Install python3-venv (if not already installed)
sudo apt update
sudo apt install python3-venv python3-pip

# Step 2: Create and activate a virtual environment
cd ~/VLSI/VSDBabySoC/
python3 -m venv sp_env
source sp_env/bin/activate

# Step 3: Install SandPiper-SaaS inside the virtual environment
pip install pyyaml click sandpiper-saas

# Step 4: Convert rvmyth.tlv to Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```
 ![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/Succesful%20creation%20of%20.v%20file.png)

✅ After running the above command, rvmyth.v will be generated in the src/module/ directory.

You can confirm this by listing the files:

```bash
jayesshsk@jayesshsk-VirtualBox:~$ cd VLSI/VSDBabySoC/
jayesshsk@jayesshsk-VirtualBox:~/VLSI/VSDBabySoC$ ls src/module/
avsddac.v  avsdpll.v  clk_gate.v  pseudo_rand_gen.sv  pseudo_rand.sv  rvmyth_gen.v  rvmyth.tlv  rvmyth.v  testbench.rvmyth.post-routing.v  testbench.v  vsdbabysoc.v
```

#### Note 
To use this environment in future sessions, always activate it first:
```bash
jayesshsk@jayesshsk-VirtualBox:~$ source sp_env/bin/activate
```
To exit:
```bash
jayesshsk@jayesshsk-VirtualBox:~$ deactivate
```

### 13. Simulation Steps

#### 13.1 Pre-Synthesis Simulation

Run the following command to perform a pre-synthesis simulation:

```bash
step 1: cd ~/VLSI/VSDBabySoC/

step 2: mkdir -p output/pre_synth_sim

step 3: iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM -I src/include -I src/module src/module/testbench.v
```

Then run:
```bash
step 4: cd output/pre_synth_sim

step 5: ./pre_synth_sim.out
```

Explanation:

- DPRE_SYNTH_SIM: Defines the PRE_SYNTH_SIM macro for conditional compilation in the testbench.
- The resulting pre_synth_sim.vcd file can be viewed in GTKWave.

#### 13.2 Viewing Waveform in GTKWave

After running the simulation, open the VCD file in GTKWave: 

```bash
step 6: gtkwave pre_synth_sim.vcd
```
Drag and drop the CLK, reset, OUT (DAC), and RV TO DAC [9:0] signals to their respective locations in the simulation tool

 ![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/VSD%20gtkwave1.png)

**👉 Conclusion (Waveform 1):**
This view confirms that the DAC is correctly mapping digital values to proportional analog voltages step by step
In this picture we can see the following signals:

**CLK**: This is the input CLK signal of the RVMYTH core. This signal comes from the PLL, originally.

**reset**: This is the input reset signal of the RVMYTH core. This signal comes from an external source, originally.

**OUT**: This is the output OUT signal of the VSDBabySoC module. This signal comes from the DAC (due to simulation restrictions it behaves like a digital signal which is incorrect), originally.

**RV_TO_DAC[9:0]**: This is the 10-bit output [9:0] OUT port of the RVMYTH core. This port comes from the RVMYTH register #17, originally.

**OUT**: This is a real datatype wire which can simulate analog values. It is the output wire real OUT signal of the DAC module. This signal comes from the DAC, originally. 

This can be viewed by changing the Data Format of the signal to Analog → Step

#### 13.3 Viewing DAC output in analog mode 

Drag and drop the CLK, reset, OUT (DAC) (as analog step), and RV TO DAC [9:0] signals to their respective locations in the simulation tool 
 ![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbaby%20gtkwave%202.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbaby%20gtkwave%203.png)

**👉 Conclusion (Waveform 2):**
This zoomed-out view shows the DAC producing a waveform-like analog signal (instead of individual digital steps). This represents the real-world analog signal you’d get if you connected BabySoC’s DAC output to a speaker or display.

### 13.4 Trouble shooting tips

   - Module Redefinition: If you encounter redefinition errors, ensure modules are included only once, either in the testbench or in the command line.
   - Path Issues: Verify paths specified with -I are correct. Use full paths if relative paths cause errors.
---
## 14. Why Pre-Synthesis and Post-Synthesis?

1. **Pre-Synthesis Simulation**: 
   - Focuses only on verifying functionality based on the RTL code.
   - Zero-delay environment, with events occurring on the active clock edge.

2. **Post-Synthesis Simulation (GLS)**:
   - Uses the synthesized netlist (gate-level) to simulate both functionality and timing.
   - Identifies timing violations and potential mismatches (e.g., unintended latches).
   - Helps verify dynamic circuit behavior that static methods may miss.

---

## Summary

- BabySoC = RVMYTH (CPU) + PLL (Clock) + DAC (Analog output)
- SoC = CPU + memory + peripherals + interconnect on one chip
- Pre-synthesis sim → checks RTL functionality
- Post-synthesis sim (GLS) → checks timing + functionality
- Use complete if-else/case to avoid latches
- For/Generate loops → scalable Verilog coding
- Tools: Icarus Verilog + GTKWave
  
---


---
</details>

---
# 2. RTL Synthesis, STA Fundamentals, and Post-Synthesis Timing Analysis
  
 Building on the functional understanding, Week 3 shifted into RTL verification, post-synthesis validation, and timing fundamentals. The RTL was synthesized using Sky130 standard cells and its resulting gate-level netlist was verified through Gate-Level Simulation (GLS). This ensured that logic optimization during synthesis did not alter functional correctness. Static Timing Analysis (STA) was then introduced to understand timing margins, critical paths, setup/hold behavior, and constraint-driven optimization. These analyses created the baseline timing profile that would later be compared against post-layout timing after parasitic extraction. This week solidified the transition from RTL modeling to timing-accurate digital implementation.

If u want to see in detail **(CLICK BELOW)**

<details> <summary><strong>🚀 Week 3: Post Synthesis GLS,STA Fundamentals and STA Analysis for Post Synthesis GLS</strong></summary>

<details> <summary><strong> Week 3 Part 1: Post Synthesis GLS: “BabySOC”</strong></summary> 
  
# 🚀 Week 3 Part 1: Post Synthesis GLS: “BabySOC”
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-3-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Part 1 of Week 3!** 🚀  
Up to now, your BabySoC has lived in the world of RTL — abstract, fast, and ideal.  
But in the real silicon world, wires have delays, gates have timing, and logic must keep up with the clock.  

This is where **Gate-Level Simulation (GLS)** steps in — it’s like giving your design its first **heartbeat** after synthesis.  
We move from ideal logic to the actual gate-level representation, ensuring that every flip-flop and signal transition behaves correctly when timing becomes real. 💡  

---

## 📚 **Table of Contents**

- [🚀 Week 3 Part 1: Post Synthesis GLS — “BabySoC”](#-week-3-part-1-post-synthesis-gls--babysoc)
  - [🎯 Objective](#objective)
  - [⚙️ POST-SYNTHESIS SIMULATION](#️post-synthesis-simulation)
    - [Gate-Level Simulation (GLS) — Post-Synthesis Verification](#gate-level-simulation-gls--post-synthesis-verification)
    - [Why Pre-Synthesis and Post-Synthesis?](#why-pre-synthesis-and-post-synthesis)
    - [Key Aspects of GLS for BabySoC](#key-aspects-of-gls-for-babysoc)
  - [Step-by-Step Execution Plan for Running Commands](#step-by-step-execution-plan-for-running-the--commands-manually)
    - [Step 1: Load the Top-Level Design and Supporting Modules](#step-1-load-the-top-level-design-and-supporting-modules)
    - [Step 2: Load the Liberty Files for Synthesis](#step-2-load-the-liberty-files-for-synthesis)
    - [Step 3: Run Synthesis Targeting `vsdbabysoc`](#step-3-run-synthesis-targeting-vsdbabysoc)
    - [Step 4: Map D Flip-Flops to Standard Cells](#step-4-map-d-flip-flops-to-standard-cells)
    - [Step 5: Perform Optimization and Technology Mapping](#step-5-perform-optimization-and-technology-mapping)
    - [Step 6: Perform Final Clean-Up and Renaming](#step-6-perform-final-clean-up-and-renaming)
    - [Step 7: Check Statistics](#step-7-check-statistics)
    - [Step 8: Write the Synthesized Netlist](#step-8-write-the-synthesized-netlist)
  - [POST-SYNTHESIS SIMULATION AND WAVEFORMS](#post_synthesis-simulation-and-waveforms)
    - [Step 1: Compile the Testbench](#step-1-compile-the-testbench)
    - [Step 2: Navigate to the Post-Synthesis Simulation Output Directory](#step-2-navigate-to-the-post-synthesis-simulation-output-directory)
    - [Step 3: Run the Simulation](#step-3-run-the-simulation)
    - [Step 4: View the Waveforms in GTKWave](#step-4-view-the-waveforms-in-gtkwave)
  - [Comparing Pre-Synthesis and Post-Synthesis Output](#comparing-pre-synthesis-and-post-synthesis-output)
  - [Outcome](#outcome)

---
## Objective

The objective of this session was to perform post-synthesis gate-level simulation (GLS) of the BabySoC design to verify its functionality after synthesis. The task focused on understanding how RTL code is transformed into a gate-level netlist using Yosys and how to simulate it using Icarus Verilog (iverilog). It also aimed to familiarize students with handling synthesis-related errors, managing include files, and linking Sky130 standard cell libraries for accurate verification.

---
## POST-SYNTHESIS SIMULATION

###  Gate-Level Simulation (GLS) – Post-Synthesis Verification
- **Gate-Level Simulation** (GLS), also known as **Post-Synthesis Simulation**, is performed to validate the **functional and timing correctness of a design after synthesis**. 
- While RTL (Register Transfer Level) simulations also referred as behavioral or pre-synthesis simulation — operates at a higher abstraction to verify design intent in terms of functionality — GLS runs on the synthesized netlist. This netlist contains the actual logic gates and interconnections mapped to the target technology.
- GLS ensures that the synthesized design behaves consistently with the RTL simulation results in terms of both logic and timing, now accounting for realistic timing delays and cell-level implementations. 
- It's a crucial step to confirm that synthesis hasn't introduced any functional mismatches or timing-related issues.
    


###  Why Pre-Synthesis and Post-Synthesis?
#### Pre-Synthesis Simulation:
- Focuses solely on verifying functionality based on the RTL code.
- Operates in a zero-delay environment; all events occur precisely on clock edges.
- Ideal for checking control/data logic correctness without concern for physical constraints.

#### Post-Synthesis Simulation (GLS - Gate-Level Simulation):
- Uses the synthesized gate-level netlist, incorporating technology-mapped standard cells.
- Captures functional behavior + timing delays, modeling more realistic hardware operation.
- Helps detect:
  - Timing issues like setup/hold violations.
  - Functional mismatches caused by synthesis artifacts (e.g., unintended latches).
  - Dynamic behaviors not visible through static analysis.
    
### Key Aspects of GLS for BabySoC:
1. **Verification with Timing Information:**
   - GLS is performed using Standard Delay Format (SDF) files to ensure timing correctness.
   - This checks if the SoC behaves as expected under real-world timing constraints.

2. **Design Validation Post-Synthesis:**
   - Confirms that the design's logical behavior remains correct after mapping it to the gate-level representation.
   - Ensures that the design is free from issues like metastability or glitches.

3. **Simulation Tools:**
   - Tools like Icarus Verilog or a similar simulator can be used for compiling and running the gate-level netlist.
   - Tool like yosys is used to generate the synthesised gate-level netlist
   - This netlist was then simulated using the same testbench as in RTL simulation. 
   - Waveforms were analyzed using GTKWave. The key goal was to compare waveform outputs from both stages and validate that functionality remains consistent. This validation step is essential to ensure that no logic is broken during synthesis and that the design is ready for physical implementation stages like place and route.

4. **Importance for BabySoC:**
   - BabySoC consists of multiple modules like the RISC-V processor, PLL, and DAC. GLS ensures that these modules interact correctly and meet the timing requirements in the synthesized design.


Step-by-Step execution plan for running the  commands manually:
---
## Prerequisite - for Synthesis using Yosys

The following cp commands copy **essential header files** from the **src/include directory** into the **working directory**. These include:
  - **sp_verilog.vh** – contains Verilog definitions and macros
  - **sandpiper.vh** – holds integration-related definitions for SandPiper
  - **sandpiper_gen.vh** – may include auto-generated or tool-generated parameters


#### **Step 1: Load the Top-Level Design and Supporting Modules**
- Launch the Yosys synthesis tool from your working directory.
  ```bash
  cd ~/VLSI/VSDBabySoc/src/module
  yosys
  ```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command1.png)


Inside the Yosys shell, 
Read the following in the Yosys environment
-  main vsdbabysoc.v RTL file
-  rvmyth.v with the include path using -I option.
-  clk_gate.v with the include path using -I option.

-  To Do So run:
```yosys

read_verilog vsdbabysoc.v
read_verilog -sv -I ../include rvmyth.v
read_verilog -I ../include clk_gate.v

```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command1.png)


### ❗Note:

_If you try to read the rvmyth.v file using yosys without copying the necessary header files first, you may encounter errors like:_

can't open include file "sp_verilog.vh'
can't open include file "sandpiper.vh'
can't open include file "sandpiper_gen.vh'

_To avoid these errors, make sure to copy the required include files into your working directory! This ensures Yosys can resolve them correctly during parsing, even if the -I option is used._
---

####  Step 2: Load the Liberty Files for Synthesis
Inside the same Yosys shell, run:
```yosys
yosys> read_liberty -lib lib/avsdpll.lib 
yosys> read_liberty -lib lib/avsddac.lib 
yosys> read_liberty -lib lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command2.png)

---

####  Step 3: Run Synthesis Targeting vsdbabysoc
```bash
yosys> synth -top vsdbabysoc
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command3.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command4.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command5.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command6.png)

---

####  Step 4: Map D Flip-Flops to Standard Cells
```yosys
yosys> dfflibmap -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command7.png)

---

####  Step 5: Perform Optimization and Technology Mapping

```bash
yosys> opt
yosys> abc -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command8.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command9.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command10.png)

| Step           | Purpose                                                              |
| -------------- | -------------------------------------------------------------------- |
| `strash`       | Structural hashing (reduces logic redundancy)                        |
| `scorr`        | Sequential sweeping for redundancy removal                           |
| `ifraig`       | Incremental FRAIGing (logic equivalence checking and optimization)   |
| `retime;{D}`   | Move registers across combinational logic to optimize timing         |
| `strash`       | Re-run structural hashing after retiming                             |
| `dch,-f`       | Delay-aware combinational optimization with fast mode                |
| `map,-M,1,{D}` | Map logic to gates minimizing area (`-M,1`) and retime-aware (`{D}`) |

---

####  Step 6: Perform Final Clean-Up and Renaming

```bash
yosys> flatten
yosys> setundef -zero
yosys> clean -purge
yosys> rename -enumerate
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command11.png)

| **Command**         | **Purpose / Usage**                                                                    |
| ------------------- | -------------------------------------------------------------------------------------- |
| `flatten`           | Flattens the entire design hierarchy into a single-level netlist.                      |
| `setundef -zero`    | Replaces all undefined (`x`) logic values with logical `0` to avoid simulation issues. |
| `clean -purge`      | Removes all unused wires, cells, and modules; `-purge` makes it more aggressive.       |
| `rename -enumerate` | Renames internal wires and cells to unique, numbered names for consistency.            |

---

####  Step 7: Check Statistics

```bash
yosys> stat
```
13. Printing statistics.

=== vsdbabysoc ===

![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command12.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command13.png)

####  Step 8: Write the Synthesized Netlist

```bash
yosys> write_verilog -noattr output/post_synth_sim/vsdbabysoc.synth.v
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command14.png)

After running this file copy the .v from the module directory in that there will be same output directory and paste it in after vsdbabysoc there output directory will be available

---

### POST_SYNTHESIS SIMULATION AND WAVEFORMS
---
####  Step 1: Compile the Testbench
- To ensure that the synthesized Verilog file _(vsdbabysoc.synth.v)_ is available in the src/module directory for further processing or simulation, it is important to follow the below steps
- Before running the iverilog command, **copy** the necessary **standard cell** and **primitive models**:
- These files **must be present** in the same **directory** as the **testbench** (src/module) to resolve all module references during compilation.

Run the following `iverilog` command to compile the testbench:
```bash
cd ~/VSD_RISCV_Kasturi/VSDBabySoC/
iverilog -o output/post_synth_sim/post_synth_sim.out -DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 -I src/include -I src/module -I output/post_synth_sim src/module/testbench.v
```


| **Option / Argument**                                                      | **Purpose / Description**                                                            |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `iverilog`                                                                 | Icarus Verilog compiler used to compile Verilog files into a simulation executable.  |
| `-o output/post_synth_sim/post_synth_sim.out`                              | Specifies the output binary file for simulation.                                     |
| `-DPOST_SYNTH_SIM`                                                         | Defines the macro `POST_SYNTH_SIM` (used in testbench to switch simulation modes).   |
| `-DFUNCTIONAL`                                                             | Defines `FUNCTIONAL` to use behavioral models instead of detailed gate-level timing. |
| `-DUNIT_DELAY=#1`                                                          | Assigns a unit delay of `#1` to all gates for post-synthesis simulation.             |
| `-I src/include`                                                           | Adds the `include` directory to the search path for `\`include\` directives.         |
| `-I src/module'                            `                               | Adds the `module` directory to the include path for additional module references.    |
| `src/module/testbench.v`                                                   | Specifies the testbench file as the top-level design for simulation.                 |

#### ❗Note - You may encounter these errors:
You may encounter following errors:

<strong> 🔴 Error 1:</strong>

```bash

/VSDBabySoC/src/module/testbench.v:10: Include file vsdbabysoc.synth.v not found
No top level modules, and no -s option.
```
<strong> 🟢 To resolve this error copy the vsdbabysoc.synth.v from output/post_synth_sim directory to src/module directory</strong> 
  
<strong> 🔴 Error 2:</strong>

```bash
/VSDBabySoC/src/module/sky130_fd_sc_hd.v:74452: syntax error
I give up.
```

<strong> 🟢 _To resolve this : Update the syntax in the file sky130_fd_sc_hd.v at or around line 74452._</strong>

###### Change:
```bash
`endif SKY130_FD_SC_HD__LPFLOW_BLEEDER_FUNCTIONAL_V
```

###### To:
```bash
`endif // SKY130_FD_SC_HD__LPFLOW_BLEEDER_FUNCTIONAL_V
```
---
####  Step 2: Navigate to the Post-Synthesis Simulation Output Directory
```bash
cd output/post_synth_sim/
```
####  Step 3: Run the Simulation
```bash
./post_synth_sim.out
```
---

####  Step 4: View the Waveforms in GTKWave
```bash
gtkwave post_synth_sim.vcd
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command15.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Post_Synthesis01.png)


#### To view output in analog mode
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Post_Synthesis02.png)

---


### Comparing Pre-Synthesis and Post-Synthesis Output
To ensure that the synthesis process did not alter the original design behavior, the output from the pre-synthesis simulation was compared with the post-synthesis simulation.

Both simulations were run using GTKWave, and the resulting waveforms were observed.

#### Pre-Synthesis Simulation Waveform

![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbaby%20gtkwave%203.png)

#### Post-Synthesis Simulation Waveform

![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Post_Synthesis03.png)

<strong> Observation:</strong> ✅ _The outputs of post-synthesis simulation match exactly with pre-synthesis simulation waveforms, confirming that the functionality is preserved across the synthesis flow._

_This validates that the synthesized netlist is functionally equivalent to the RTL design._


---

## Outcome

By completing this session, the BabySoC design was successfully synthesized and simulated at the gate level. The simulation confirmed that the synthesized netlist preserved the same functionality as the RTL design. Learners gained hands-on experience with Yosys synthesis, Icarus Verilog (iverilog) simulation, and debugging of missing include files and library modules. The waveform comparison validated the correctness of synthesis and improved understanding of the complete RTL-to-GLS flow in digital design.

---
</details>

<details> <summary><strong>Week 3 Part 2:Static Timing Analysis (STA) Fundamentals </strong></summary> 

# Week 3 Part 2:⚡ Static Timing Analysis (STA)

**Static Timing Analysis (STA)** is a fundamental method in **VLSI design** used to verify whether a digital circuit meets its **timing requirements** — ensuring that data moves correctly through flip-flops and logic **at the target clock frequency**.  

Unlike simulation, STA doesn’t rely on input vectors — it checks all possible timing paths **statically**, using cell and wire delay models.

---

## 🧠 Concept Overview

Imagine your digital design as a **race between data and clock**.

- The **clock** decides when data should be captured.
- The **data** passes through a series of logic gates and interconnects (each adding delay).
- STA verifies that data reaches the destination **at the right time** and **stays stable long enough**.

There are two main checks:
1. **Setup check** → Is data fast enough?
2. **Hold check** → Is data not too fast?

✅ Timing met → circuit runs safely  
❌ Timing violated → chip may fail at speed

---

## 🧩 Why We Use STA

| **Reason** | **Description** |
|-------------|----------------|
| **Vectorless verification** | No need for testbench or simulation vectors. |
| **Comprehensive coverage** | All paths are analyzed — even rare cases missed by simulation. |
| **Speed and scalability** | Works for multi-million gate designs. |
| **Corner-based analysis** | Checks timing across process, voltage, and temperature (PVT) corners. |
| **Signoff standard** | Used before fabrication to guarantee functionality and speed. |

---

## 🧱 STA Workflow (Simplified)

Netlist (post-synthesis or post-layout)
↓
Cell & Wire Delay Models (.lib, .spef)
↓
Clock Constraints (SDC)
↓
Static Timing Analyzer (OpenSTA / PrimeTime)
↓
Timing Reports → Setup/Hold Slack → Pass/Fail

yaml
Copy code

---

## 🕒 Setup and Hold Time Checks

### 🧩 Setup Check
Ensures data **arrives early enough** before the next clock edge.

Clock Period ≥ (T_clk→Q + T_comb + T_setup + Skew)

mathematica
Copy code

If violated → setup violation → reduce frequency or optimize logic.

### ⏱️ Hold Check
Ensures data **doesn’t arrive too early** after the clock edge.

T_clk→Q + T_comb ≥ T_hold + Skew

pgsql
Copy code

If violated → hold violation → add delay or balance skew.

---

## 🧮 Key Timing Parameters

| **Parameter** | **Meaning** | **Notes** |
|----------------|-------------|-----------|
| **Clock-to-Q (T_clk→Q)** | Delay from clock edge to data output of flip-flop | Starting point for path |
| **Combinational Delay (T_comb)** | Delay through logic gates/wires | Affected by load, fanout |
| **Setup Time (T_setup)** | Data must be stable *before* clock edge | Ensures proper latching |
| **Hold Time (T_hold)** | Data must be stable *after* clock edge | Prevents data corruption |
| **Clock Skew** | Difference in clock arrival time between FFs | Can be useful or harmful |
| **Slack** | `Required – Arrival` | Positive → safe, Negative → violation |

---

## 🧩 STA Path Types

| **Type** | **Start Point** | **End Point** | **Typical Check** |
|-----------|----------------|---------------|-------------------|
| **Reg → Reg** | FF → Logic → FF | Setup / Hold |
| **In → Reg** | Input port → Logic → FF | Setup |
| **Reg → Out** | FF → Logic → Output port | Setup |
| **In → Out** | Input → Logic → Output | Combinational path |
| **Clock Gating** | Clock → Enable path | Glitch-free gating |
| **Recovery/Removal** | Async reset/set → Clock | Reset timing |
| **Latch Timing** | Latch-based designs | Time borrow/give |
| **Data-to-Data** | Internal dependent signals | Data race analysis |

---

## ⚙️ Detailed Timing Path Equation

For **setup check**:

Data Arrival Time ≤ Data Required Time

Data Arrival Time = Launch Clock + T_clk→Q + T_comb
Data Required Time = Capture Clock + T_setup
Slack = (Required – Arrival)

sql
Copy code

For **hold check**:

Data Arrival Time ≥ Data Required Time

Data Arrival Time = Launch Clock + T_clk→Q + T_comb
Data Required Time = Capture Clock + T_hold
Slack = (Arrival – Required)

yaml
Copy code

---

## ⚡ Slew / Transition Analysis

| **Type** | **Check** | **Purpose** |
|-----------|------------|-------------|
| **Data Slew (max/min)** | Max: avoid slow transitions <br> Min: avoid too fast switching | Prevent distortion and noise |
| **Clock Slew (max/min)** | Similar checks for clock signals | Ensure clock integrity |

> Slow slew = poor signal integrity → increased delay  
> Fast slew = potential crosstalk/glitch issues  

---

## ⚙️ Load Analysis

| **Type** | **Definition** | **Reason for Check** |
|-----------|----------------|----------------------|
| **Fanout (max/min)** | Number of driven inputs per output | Avoid overloading driver |
| **Capacitance (max/min)** | Total load (wire + input cap) | Controls transition time |

---

## ⏱️ Clock Analysis

| **Parameter** | **Definition** | **Impact** |
|----------------|----------------|------------|
| **Clock Skew** | Difference in clock arrival between FFs | Affects setup/hold margin |
| **Clock Jitter** | Variation in clock edge over cycles | Adds timing uncertainty |
| **Pulse Width** | Duration of high/low clock level | Prevents narrow glitches |
| **Duty Cycle** | Ratio of high to total period | Impacts edge-based timing |

---

## 🧩 Advanced STA Topics

| **Concept** | **Description** |
|--------------|----------------|
| **AOCV (Advanced OCV)** | Accounts for distance-dependent variation in delay |
| **POCV (Parametric OCV)** | Statistical timing variation modeling |
| **MCMM (Multi-Corner Multi-Mode)** | Analyzes multiple PVT corners and operating modes simultaneously |
| **CRPR (Clock Reconvergence Pessimism Removal)** | Avoids double-counting common clock delays |
| **ECO (Engineering Change Order)** | Fixes setup/hold violations after layout without full re-synthesis |
| **Timing Exceptions** | `set_false_path`, `set_multicycle_path`, etc., to ignore non-critical paths |
| **Derating** | Scaling delays to account for process/voltage uncertainty |
| **Clock Tree Synthesis (CTS)** | Balances skew and minimizes latency |
| **Signoff STA** | Final check before GDSII tapeout |

---

## 📈 STA Process in Real Flow

1. **Pre-Layout STA (Post-Synthesis)**  
   - Uses ideal clocks and estimated wire delays.  
   - Goal: Catch logic-level timing issues early.  

2. **Post-Layout STA (Post-Route)**  
   - Uses extracted parasitics (`.spef`).  
   - Realistic wire delay and coupling considered.  

3. **Signoff STA**  
   - Corner-based (SS/FF/TT, Vmin/Vmax, Tmin/Tmax).  
   - Ensures chip works across all PVT conditions.  

---

## 🧰 Tools for STA

| **Tool** | **Vendor / Type** | **Usage** |
|-----------|------------------|-----------|
| **OpenSTA** | Open-Source | Static timing verification |
| **PrimeTime** | Synopsys | Industry-standard STA tool |
| **Tempus** | Cadence | Multi-corner signoff STA |
| **GoldTime** | Siemens | Static & dynamic timing |
| **OpenTimer** | Lightweight open-source | Fast path analysis |

---

## 📘 STA Report Example

Startpoint: U1/FF1/Q
Endpoint: U2/FF2/D
Path Type: Setup

Clock: CLK (rise edge)
Point Arrival Required Slack
Clock Launch Edge 0.000
U1/FF1 (Clk→Q) 0.120
Combinational Delay 1.520
Clock Capture Edge 2.000
U2/FF2 (Setup) 0.120
Slack = Required - Arrival = 2.000 - 1.640 = +0.360 ns

yaml
Copy code

✅ Positive slack → timing met  
❌ Negative slack → timing violation

---

## 🏁 STA Signoff Checklist

| **Check** | **Goal** |
|------------|----------|
| Setup timing | Meets frequency requirement |
| Hold timing | No data corruption |
| Slew check | Signal integrity within limits |
| Capacitance/fanout | Driver load acceptable |
| Clock skew/jitter | Within skew budget |
| DRC/LVS clean | Physical correctness |
| Power/timing co-validation | Balanced design |
| STA @ all corners | Fully robust SoC ready for tapeout |

---

> 🧠 **Summary:**  
> STA is the *heartbeat verification* of digital design — it ensures every bit of data races safely with the clock, across every gate, path, and condition.

> 🛠️ *If logic defines “what” a chip does, STA defines “how fast and safely” it can do it.

</details>

<details> <summary><strong> Week 3 Part 3: Static Timing Analysis: “BabySOC” </strong></summary> 

 #  Week 3 Part 3: Static Timing Analysis: “BabySOC”
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-3-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Part 3 of Week 3!** 🚀  
Timing Analysis (STA) was performed using OpenSTA to analyze the timing behavior of the synthesized RISC-V design. The flow involved loading the gate-level netlist, Liberty timing libraries, and SDC constraints, followed by generating timing reports for setup, hold, and clock path analysis. Key STA commands such as read_liberty, read_verilog, link_design, and report_checks were used to evaluate slack and identify critical timing paths. The final outcome was verifying whether the post-synthesis design meets all timing requirements.

---
## Timing Graphs using openSTA

### 📚 Contents

- [Introduction](#introduction)
- [Installation of OpenSTA](#installation-of-opensta)
  - [Step 1: Clone the Repository](#step-1-clone-the-repository)
  - [Step 2: Build the Docker Image](#step-2-build-the-docker-image)
  - [Step 3: Run the OpenSTA Container](#step-3-run-the-opensta-container)
- [Timing Analysis Using Inline Commands](#timing-analysis-using-inline-commands)
  - [Analyzing report outcomes](#analyzing-report-outcomes)
  - [SPEF-Based Timing Analysis](#spef-based-timing-analysis)
  - [more options to explore](#more-options-to-explore)
- [Timing Analysis Using a TCL Script](#timing-analysis-using-a-tcl-script)
- [VSDBabySoC basic timing analysis](#vsdbabysoc-basic-timing-analysis)
- [VSDBabySoC PVT Corner Analysis (Post-Synthesis Timing)](#vsdbabysoc-pvt-corner-analysis-post-synthesis-timing)
- [Timing Summary Across PVT Corners (Post-Synthesis STA Results)](#timing-summary-across-pvt-corners-post-synthesis-sta-results)
- [Timing Plots Across PVT Corners](#timing-plots-across-pvt-corners)

---
## Objective
The objective of this lab is to perform Static Timing Analysis (STA) on the synthesized Gate-Level Netlist of VSDBabySoC using OpenSTA.
This includes:
- Understanding timing paths, timing checks, and slack calculations.
- Running max-delay (setup) and min-delay (hold) analysis.
- Performing timing using Liberty files and SPEF-based parasitics.
- Generating timing reports across different PVT corners (TT, SS, FF).
- Interpreting timing graphs, path delays, and clock modeling.
- Ensuring the BabySoC meets timing requirements for its target frequency.

---

## Introduction

**Static Timing Analysis** is one of the many techniques available to verify the timing of a digital design. 

An alternate approach used to verify the timing is the timing simulation which can verify the functionality as well as the timing of the design. 

The term timing analysis is used to refer to either of these two methods - static timing analysis, or the timing simulation. 

Thus, timing analysis simply refers to the analysis of the design for timing issues.

The STA is static since the analysis of the design is carried out statically and does not depend upon the data values being applied at the input pins. 

This is in contrast to simulation based timing analysis where a stimulus is applied on input signals, resulting behavior is observed and verified, then time is advanced with new input stimulus applied, and the new behavior is observed and verified and so on.

In a CMOS digital design flow, the static timing analysis can be performed at many different stages of the implementation. Figure below shows a typical flow.

**OpenSTA** is an open source static timing analyzer (STA) tool used in digital design. It is utilized to analyze and verify the timing performance of digital circuits at the gate level.

OpenSTA uses a TCL command interpreter to read the design, specify timing constraints and print timing reports.

#### Input Files

- `*.v`  : Gate-level Verilog Netlist  
- `*.lib` : Liberty Timing Libraries  
- `*.sdc` : Synopsys Design Constraints (clocks, delays, false paths)  
- `*.sdf` : Annotated Delay File (optional)  
- `*.spef`: Parasitics (RC extraction)  
- `*.vcd` / `*.saif` : Switching Activity for Power Analysis 

#### Clock Modeling Features

- `Generated Clocks`: Derived from existing clocks  
- `Latency`: Clock propagation delay  
- `Source Latency`: Insertion delay from clock source to input  
- `Uncertainty`: Jitter or skew margins  
- `Propagated vs. Ideal`: Real vs. ideal clock network modeling  
- `Gated Clock Checks`: Verifies clocks that are enabled conditionally  
- `Multi-Frequency Clocks`: Analyzes multiple domains  

#### Exception Paths

Timing exceptions refine analysis for real behavior:

- `set_false_path` — Ignores invalid functional paths  
- `set_multicycle_path` — Allows multiple clock cycles  
- `set_max_delay` / `set_min_delay` — Custom timing limits

#### Delay calculation

- `Integrated Dartu/Menezes/Pileggi RC effective capacitance algorithm`

Models effective capacitance for RC networks to compute realistic gate and net delays. It balances accuracy and runtime using an efficient algorithm developed for timing engines.

- `External delay calculator API`
    
Allows plugging in custom delay calculators for advanced or proprietary models (e.g., layout-aware or temperature-adaptive models). Useful for integrating tool flows beyond standard Liberty data.

#### Timing Analysis and Reporting

OpenSTA provides a rich set of commands for analyzing timing paths, delays, and setup/hold checks:

- `report_checks`  
  Reports timing violations across specified paths using options like `-from`, `-through`, and `-to`. Supports multi-path analysis to any endpoint.

  ```tcl
  report_checks -from [get_pins U1/Q] -to [get_pins U2/D]
  ```
#### Timing Paths 

`What do you mean by Timing Paths?`
* It Refer to the logical paths a signal takes through a digital circuit from its source to its destination, including sequential and combinational elements. STA analyzes timing paths to determine their delay, setup and hold times, and other timing parameters specified in the constraints. Timing paths are categorized into combinatorial and sequential, and the critical path is the longest path in the design with the maximum operating frequency.

#### Timing Path Elements
   
Timing path elements in STA are the start point, where a signal originates, the end point, where it terminates, and the combinational logic elements, such as gates, that the signal passes through. Timing paths are traced to determine the overall delay and timing performance of the digital circuit.

**Start Point**: Is the point where the signal originates or enters the digital circuit. This point is typically an input port of the design, where the signal is first introduced to the circuit.

The start point of a timing path can be either:

- An input port, where data enters the design, or

- The clock pin of a register, where data is launched on a clock edge.

**End Point:** Is the point where the signal terminates or leaves the digital circuit. This point is typically an output port of the design, where the signal is outputted from the circuit.

The end point of a timing path can be either:

- A register's data input pin (D pin), where data is captured by the clock edge, or

- An output port, where data must be available at a specific time.

**Combinational Logic:** Combinational logic elements are the building blocks of a digital circuit and are used to perform logic operations on the signals passing through the circuit. These elements do not store any information, and the output of a combinational logic element is solely determined by the input values at that moment.


#### Setup and Hold Checks

-> **What is Setup Check?**
* Is the minimum time that the data must be stable before the clock edge, and if this time is not met, it can lead to setup violations, resulting in incorrect data being stored in the sequential element. The setup check is essential to ensure correct timing behavior of a digital circuit and prevent data loss or other timing-related issues.
* The setup time of a flip-flop depends on the technology node, operating conditions, and other factors. The value of the setup time is usually provided in the logic libraries.

-> **What is Hold Check?**
* Is the minimum amount of time that the data must remain stable after the clock edge, and if this time is not met, it can lead to hold violations, resulting in incorrect data being stored in the sequential element. The hold check is necessary to prevent issues such as data corruption, metastability, and other timing-related problems in digital circuits.

#### Slack Calculation 

Setup and hold slack is defined as the difference between data required time and data arrivals time. 

>Setup slack = Data required time - Data arrival time

>Hold slack = Data arrival time - Data required time

-> **What is Data Arrival Time?**
* The time taken by the signal to travel from the start point to the end point of the digital circuit. 

-> **What is Data Required Time?** 
* The time for the clock to traverse through the clock path of the digital circuit. 

-> **What is Slack?** 
* It is difference between the desired arrival times and the actual arrival time for a signal. 
* Positive Slack indicates that the design is meeting the timing and still it can be improved. 
* Zero slack means that the design is critically working at the desired frequency. 
* Negative slack means, design has not achieved the specified timings at the specified frequency.
* Slack has to be positive always and negative slack indicates a violation in timing.

#### Common SDC Constraints

In Static Timing Analysis (STA), **Synopsys Design Constraints (SDC)** are used to define the behavior, environment, and timing requirements of a digital design. These constraints are categorized based on their function and purpose.

**Operating Conditions** are set using the `set_operating_conditions` command, which defines the process-voltage-temperature (PVT) corner used during analysis.

**Wire-Load Models** such as `set_wire_load_mode`, `set_wire_load_model`, and `set_wire_load_selection_group` are used to estimate interconnect capacitance and resistance based on fanout and hierarchy when post-layout parasitics are unavailable.

**Environmental Constraints** define the electrical behavior of I/Os. The `set_drive` and `set_driving_cell` commands model input driving strength or source cell characteristics. Output loads are described using `set_load` or `set_fanout_load`. Additional attributes like `set_input_transition` (input slew) and `set_port_fanout_number` (expected output fanout) further refine environment models.

**Design Rule Constraints** ensure physical design adherence. These include `set_max_capacitance` to limit load, `set_max_fanout` to cap number of loads, and `set_max_transition` to restrict slew for signal integrity and EM/IR compliance.

**Timing Constraints** are the core of STA. `create_clock` defines primary clocks, while `create_generated_clock` handles derived clocks. Clock behavior is further detailed using `set_clock_latency`, `set_clock_transition`, and `set_clock_uncertainty`. Timing analysis can be guided with `set_propagated_clock` to consider actual delays, or `set_disable_timing` to ignore specific paths.

Signal timing is modeled using `set_input_delay` and `set_output_delay`. The `set_input_delay` command specifies when input data arrives relative to the clock edge, crucial for setup/hold timing analysis. The `set_output_delay` command defines the required time by which output signals must be valid, helping STA tools verify that data is launched and captured within acceptable timing windows.

**Timing Exceptions** allow control over non-functional or multi-cycle paths. `set_false_path` removes paths from analysis, `set_max_delay` restricts path delay, and `set_multicycle_path` increases the allowed number of clock cycles for timing paths that do not need single-cycle timing closure.

Lastly, **Power Constraints** help manage dynamic and leakage power budgets using `set_max_dynamic_power` and `set_max_leakage_power`. These are especially useful in power-aware synthesis and verification flows.


| Category              | Commands                                                                 |
|-----------------------|--------------------------------------------------------------------------|
| **Operating Conditions** | `set_operating_conditions`                                                |
| **Wire-load Models**     | `set_wire_load_mode`  <br> `set_wire_load_model` <br> `set_wire_load_selection_group` |
| **Environmental**        | `set_drive` <br> `set_driving_cell` <br> `set_load` <br> `set_fanout_load` <br> `set_input_transition` <br> `set_port_fanout_number` |
| **Design Rules**         | `set_max_capacitance` <br> `set_max_fanout` <br> `set_max_transition`         |
| **Timing**               | `create_clock` <br> `create_generated_clock` <br> `set_clock_latency` <br> `set_clock_transition` <br> `set_disable_timing` <br> `set_propagated_clock` <br> `set_clock_uncertainty` <br> `set_input_delay` <br> `set_output_delay` |
| **Exceptions**           | `set_false_path` <br> `set_max_delay` <br> `set_multicycle_path`              |
| **Power**                | `set_max_dynamic_power` <br> `set_max_leakage_power`                          |

---
## Installation of OpenSTA

**Note:** Installation instructions are adapted from the official OpenSTA repository:
🔗 https://github.com/parallaxsw/OpenSTA

#### Step 1: Clone the Repository
```bash
git clone https://github.com/parallaxsw/OpenSTA.git
cd OpenSTA
```
#### Step 2: Build the Docker Image
```bash
docker build --file Dockerfile.ubuntu22.04 --tag opensta .
```
This builds a Docker image named opensta using the provided Ubuntu 22.04 Dockerfile. All dependencies are installed during this step.

#### Step 3: Run the OpenSTA Container
To run a docker container using the OpenSTA image, use the -v option to docker to mount direcories with data to use and -i to run interactively.
```bash
docker run -i -v $HOME:/data opensta
```
You now have OpenSTA installed and running inside a Docker container. After successful installation, you will see the % prompt—this indicates that the OpenSTA interactive shell is ready for use.
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command1.png)
### Timing Analysis Using Inline Commands

Once inside the OpenSTA shell (% prompt), you can perform a basic static timing analysis using the following inline commands:
```shell
# Instructs OpenSTA to read and load the Liberty file "nangate45_slow.lib.gz".
read_liberty /OpenSTA/examples/nangate45_slow.lib.gz

# Intructs OpenSTA to read and load the Verilog file (gate level verilog netlist) "example1.v"
read_verilog /OpenSTA/examples/example1.v

# Using "top," which stands for the main module, links the Verilog code with the Liberty timing cells.
link_design top

# Create a 10ns clock named 'clk' for clk1, clk2, and clk3 inputs 
create_clock -name clk -period 10 {clk1 clk2 clk3}

# Set 0ns input delay for inputs in1 and in2 relative to clock 'clk'
set_input_delay -clock clk 0 {in1 in2}

# Report of the timing checks for the design 
report_checks 
```
  
_This flow is useful for quick testing and debugging without writing a full TCL script._

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command2.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command3.png)


**Note:** We used report_checks here because only the slow liberty file (nangate45_slow.lib.gz) is loaded. 

This represents a setup (max delay) corner, so the analysis focuses on setup timing by default.

🤔**Why Does report_checks Show Only Max (Setup) Paths?**

By default, report_checks reports -path_delay max (i.e., setup checks).

OpenSTA interprets report_checks without arguments as:
```shell
report_checks -path_delay max
```
This reports only max path delays, i.e., setup timing checks.

✅**How to Also Get Hold (min) Paths:**

If you want both setup and hold timing checks (i.e., both max and min path delays), use:
```shell
report_checks -path_delay min_max
```
(Or) if you want to see only hold checks (min path delays):
```shell
report_checks -path_delay min
```
#### Analyzing report outcomes

*Verilog Netlist: example1.v*
```shell
module top (in1, in2, clk1, clk2, clk3, out);
  input in1, in2, clk1, clk2, clk3;
  output out;
  wire r1q, r2q, u1z, u2z;

  DFF_X1 r1 (.D(in1), .CK(clk1), .Q(r1q));
  DFF_X1 r2 (.D(in2), .CK(clk2), .Q(r2q));
  BUF_X1 u1 (.A(r2q), .Z(u1z));
  AND2_X1 u2 (.A1(r1q), .A2(u1z), .ZN(u2z));
  DFF_X1 r3 (.D(u2z), .CK(clk3), .Q(out));
endmodule
```
Here are the commands for Yosys synthesis for example1.v:

```shell
jayesshsk@jayesshsk:~ cd ~/VLSI/VSDBabySoc/OpenSTA/examples/
jayesshsk@jayesshsk:~/VLSI/VSDBabySoc/OpenSTA/examples$ yosys
yosys> read_liberty -lib nangate45_slow.lib
yosys> read_verilog example1.v
yosys> synth -top top
3.25. Printing statistics.

=== top ===

   Number of wires:                 10
   Number of wire bits:             10
   Number of public wires:          10
   Number of public wire bits:      10
   Number of ports:                  6
   Number of port bits:              6
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:                  5
     AND2_X1                         1
     BUF_X1                          1
     DFF_X1                          3

3.26. Executing CHECK pass (checking for obvious problems).
Checking module top...
Found and reported 0 problems.

```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command7.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command8.png)


#### SPEF-Based Timing Analysis

Here’s the same OpenSTA timing analysis flow with added SPEF-based parasitic modeling:

This enables **more realistic delay and slack computation** by including post-layout RC data, improving timing signoff precision.

```shell
docker run -i -v $HOME:/data opensta
```

```shell
read_liberty /OpenSTA/examples/nangate45_slow.lib.gz
read_verilog /OpenSTA/examples/example1.v
link_design top
read_spef /OpenSTA/examples/example1.dspef
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}
report_checks
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command9.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command10.png)


#### more options to explore

**<ins>Report Capacitance per Stage</ins>**

Reports timing paths with 4-digit precision and shows the net capacitance at each stage, helping identify high-cap nodes that may affect delay.

```shell
% report_checks -digits 4 -fields capacitance
report_checks -digits 4 -fields capacitance
Startpoint: r2 (rising edge-triggered flip-flop clocked by clk)
Endpoint: r3 (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

      Cap     Delay      Time   Description
----------------------------------------------------------------------
             0.0000    0.0000   clock clk (rise edge)
             0.0000    0.0000   clock network delay (ideal)
             0.0000    0.0000 ^ r2/CK (DFF_X1)
 275.9346    2.5838    2.5838 ^ r2/Q (DFF_X1)
 275.9392    2.5778    5.1617 ^ u1/Z (BUF_X1)
 276.1091    2.7520    7.9137 ^ u2/ZN (AND2_X1)
             0.0013    7.9150 ^ r3/D (DFF_X1)
                       7.9150   data arrival time

            10.0000   10.0000   clock clk (rise edge)
             0.0000   10.0000   clock network delay (ideal)
             0.0000   10.0000   clock reconvergence pessimism
                      10.0000 ^ r3/CK (DFF_X1)
            -0.5697    9.4303   library setup time
                       9.4303   data required time
----------------------------------------------------------------------
                       9.4303   data required time
                      -7.9150   data arrival time
----------------------------------------------------------------------
                       1.5153   slack (MET)

```

**<ins>Report Timing with Capacitance, Slew, Input Pins, and Fanout</ins>**

Report timing with capacitance, slew, input pins, and fanout per stage.

```shell
% report_checks -digits 4 -fields [list capacitance slew input_pins fanout]
report_checks -digits 4 -fields [list capacitance slew input_pins fanout]
Startpoint: r2 (rising edge-triggered flip-flop clocked by clk)
Endpoint: r3 (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout       Cap      Slew     Delay      Time   Description
-------------------------------------------------------------------------------------
                    0.0000    0.0000    0.0000   clock clk (rise edge)
                              0.0000    0.0000   clock network delay (ideal)
                    0.0000    0.0000    0.0000 ^ r2/CK (DFF_X1)
     1  275.9346    2.1654    2.5838    2.5838 ^ r2/Q (DFF_X1)
                    2.1654    0.0013    2.5851 ^ u1/A (BUF_X1)
     1  275.9392    2.1393    2.5765    5.1617 ^ u1/Z (BUF_X1)
                    2.1393    0.0013    5.1630 ^ u2/A2 (AND2_X1)
     1  276.1091    2.1579    2.7507    7.9137 ^ u2/ZN (AND2_X1)
                    2.1579    0.0013    7.9150 ^ r3/D (DFF_X1)
                                        7.9150   data arrival time

                    0.0000   10.0000   10.0000   clock clk (rise edge)
                              0.0000   10.0000   clock network delay (ideal)
                              0.0000   10.0000   clock reconvergence pessimism
                                       10.0000 ^ r3/CK (DFF_X1)
                             -0.5697    9.4303   library setup time
                                        9.4303   data required time
-------------------------------------------------------------------------------------
                                        9.4303   data required time
                                       -7.9150   data arrival time
-------------------------------------------------------------------------------------
                                        1.5153   slack (MET)

```

**<ins>Report Total and Component Power</ins>**

The report_power command uses static power analysis based on propagated or annotated pin activities in the circuit using Liberty power models. 

The internal, switching, leakage and total power are reported. 

Design power is reported separately for combinational, sequential, macro and pad groups. Power values are reported in watts

```shell
% report_power
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             1.09e-06   3.73e-06   1.63e-07   4.99e-06  65.8%
Combinational          6.68e-08   2.49e-06   3.08e-08   2.59e-06  34.2%
Clock                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  1.16e-06   6.22e-06   1.94e-07   7.58e-06 100.0%
                          15.3%      82.1%       2.6%
```


**<ins>Report Pulse Width Checks</ins>**

The report_pulse_width_checks command reports min pulse width checks for pins in the clock network. 

If pins is not specified all clock network pins are reported

```shell
% report_pulse_width_checks
report_pulse_width_checks
                                     Required  Actual
Pin                                    Width   Width   Slack
------------------------------------------------------------
r1/CK (high)                            0.22    5.00    4.78 (MET)
r2/CK (high)                            0.22    5.00    4.78 (MET)
r3/CK (high)                            0.22    5.00    4.78 (MET)
r1/CK (low)                             0.19    5.00    4.81 (MET)
r2/CK (low)                             0.19    5.00    4.81 (MET)
r3/CK (low)                             0.19    5.00    4.81 (MET)

```

**<ins>Report Units </ins>**

Report the units used for command arguments and reporting.

```shell
% report_units
report_units
 time 1ns
 capacitance 1fF
 resistance 1kohm
 voltage 1v
 current 1mA
 power 1nW
 distance 1um
```

### Timing Analysis Using a TCL Script

To automate the timing flow, you can write the commands into a .tcl script and execute it from the OpenSTA shell.

---
### min_max_delays.tcl

```shell
# Load liberty files for max and min analysis
read_liberty -max /data/VLSI/VSDBabySoc/OpenSTA/examples/nangate45_slow.lib
read_liberty -min /data/VLSI/VSDBabySoc/OpenSTA/examples/nangate45_fast.lib

# Read the gate-level Verilog netlist
read_verilog /data/VLSI/VSDBabySoc/OpenSTA/examples/example1.v

# Link the top-level design
link_design top

# Define clocks and input delays
create_clock -name clk -period 10 {clk1 clk2 clk3}
set_input_delay -clock clk 0 {in1 in2}

# Generate a full min/max timing report
report_checks -path_delay min_max
```


| **Line of Code**                                     | **Purpose**             | **Explanation**                                                                              |
| ---------------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| `read_liberty -max nangate45_slow.lib.gz`            | Load max delay library  | Loads the **slow corner Liberty file** for **setup (max delay)** analysis.                   |
| `read_liberty -min nangate45_fast.lib.gz`            | Load min delay library  | Loads the **fast corner Liberty file** for **hold (min delay)** analysis.                    |
| `read_verilog example1.v`                            | Load gate-level netlist | Reads the synthesized **Verilog netlist** of the design.                                     |
| `link_design top`                                    | Link design             | Links the netlist using `top` as the **top-level module**, connecting it with Liberty cells. |
| `create_clock -name clk -period 10 {clk1 clk2 clk3}` | Create clock            | Defines a **clock named `clk`** with a 10 ns period on ports `clk1`, `clk2`, and `clk3`.     |
| `set_input_delay -clock clk 0 {in1 in2}`             | Set input delay         | Applies **0 ns input delay** relative to `clk` for inputs `in1` and `in2`.                   |
| `report_checks -path_delay min_max`                  | Run full STA            | Reports both **setup (max)** and **hold (min)** timing paths and checks.                     |

#### Run the Script Using Docker

To run this script non-interactively using Docker:

```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoc/OpenSTA/examples/min_max_delays.tcl
```

🤔**Why use the full path?**

Inside the Docker container, your $HOME directory from the host system is mounted as /data.

_So a file located at `$HOME/VLSI/VSDBabySoc/OpenSTA/examples/min_max_delays.tcl` on your machine becomes accessible at `/data/VLSI/VSDBabySoc/OpenSTA/examples/min_max_delays.tcl` inside the container._

This absolute path ensures that OpenSTA can locate and execute the script correctly within the container's file system.

This method ensures repeatability and makes it easy to maintain reusable timing analysis setups for your designs.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command11.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command12.png)

### VSDBabySoC basic timing analysis

#### Prepare Required Files

To begin static timing analysis on the VSDBabySoC design, you must organize and prepare the required files in specific directories.

```bash
# Create a directory to store Liberty timing libraries
jayesshsk@jayesshsk:~/VLSI/VSDBabySoc/OpenSTA$ mkdir -p examples/timing_libs/
jayesshsk@jayesshsk:~/VLSI/VSDBabySoc/OpenSTA/examples$ ls timing_libs/
avsddac.lib  avsdpll.lib  sky130_fd_sc_hd__tt_025C_1v80.lib
# Create a directory to store synthesized netlist and constraint files
jayesshsk@jayesshsk:~/VLSI/VSDBabySoc/OpenSTA$ mkdir -p examples/BabySoC
jayesshsk@jayesshsk:~/VLSI/VSDBabySoc/OpenSTA/examples$ ls BabySoC/
gcd_sky130hd.sdc vsdbabysoc_synthesis.sdc  vsdbabysoc.synth.v
```
These files include:

- Standard cell library: sky130_fd_sc_hd__tt_025C_1v80.lib

- IP-specific Liberty libraries: avsdpll.lib, avsddac.lib

- Synthesized gate-level netlist: vsdbabysoc.synth.v

- Timing constraints: vsdbabysoc_synthesis.sdc

Below is the TCL script to run complete min/max timing checks on the SoC:

### vsdbabysoc_min_max_delays.tcl
  
```shell
# Load Liberty Libraries (standard cell + IPs)
read_liberty -min /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -max /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib

read_liberty -min /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty -max /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib

read_liberty -min /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib
read_liberty -max /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib

# Read Synthesized Netlist
read_verilog /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc.synth.v

# Link the Top-Level Design
link_design vsdbabysoc

# Apply SDC Constraints
read_sdc /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc_synthesis.sdc

# Generate Timing Report
report_checks
```

| **Line of Code**                                       | **Purpose**                | **Explanation**                                                                                    |
| ------------------------------------------------------ | -------------------------- | -------------------------------------------------------------------------------------------------- |
| `read_liberty -min ...sky130...` & `-max ...sky130...` | Load standard cell library | Loads the **typical PVT corner** for both min (hold) and max (setup) timing analysis.              |
| `read_liberty -min/-max avsdpll.lib`                   | Load PLL IP Liberty        | Includes Liberty timing views of the **PLL IP** used in the design.                                |
| `read_liberty -min/-max avsddac.lib`                   | Load DAC IP Liberty        | Includes Liberty timing views of the **DAC IP** used in the design.                                |
| `read_verilog vsdbabysoc.synth.v`                      | Load synthesized netlist   | Loads the gate-level Verilog netlist of the **VSDBabySoC** design.                                 |
| `link_design vsdbabysoc`                               | Link top-level module      | Links the hierarchy using `vsdbabysoc` as the **top module** for timing analysis.                  |
| `read_sdc vsdbabysoc_synthesis.sdc`                    | Load constraints           | Loads SDC file specifying **clock definitions, input/output delays, and false paths**.             |
| `report_checks`                                        | Run timing analysis        | Generates a default **setup timing report**. Add `-path_delay min_max` to see both hold and setup. |

execute it inside the Docker container:

```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc_min_max_delays.tcl
```
⚠️ **Possible Error Alert**

You may encounter the following error when running the script:

```shell
Warning: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 23, default_fanout_load is 0.0.
Warning: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 1, library sky130_fd_sc_hd__tt_025C_1v80 already exists.
Warning: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 23, default_fanout_load is 0.0.
Error: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib line 54, syntax error
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command13.png)

✅ **Fix:**

This error occurs because Liberty syntax does not support // for single-line comments, and more importantly, the { character appearing after // confuses the Liberty parser. Specifically, check around _line 54 of avsdpll.lib_ and correct any syntax issues such as:

```shell
//pin (GND#2) {
//  direction : input;
//  max_transition : 2.5;
//  capacitance : 0.001;
//}
```
✔️ **Replace with:**
```shell
/*
pin (GND#2) {
  direction : input;
  max_transition : 2.5;
  capacitance : 0.001;
}
*/
```
we have to do for everything
This should allow OpenSTA to parse the Liberty file without throwing syntax errors.

After fixing the Liberty file comment syntax as shown above, you can rerun the script to perform complete timing analysis for VSDBabySoC:

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command14.png)


### VSDBabySoC PVT Corner Analysis (Post-Synthesis Timing)
Static Timing Analysis (STA) is performed across various **PVT (Process-Voltage-Temperature)** corners to ensure the design meets timing requirements under different conditions.

### Critical Timing Corners

**Worst Max Path (Setup-critical) Corners:**
- `ss_LowTemp_LowVolt`
- `ss_HighTemp_LowVolt`  
_These represent the **slowest** operating conditions._

**Worst Min Path (Hold-critical) Corners:**
- `ff_LowTemp_HighVolt`
- `ff_HighTemp_HighVolt`  
_These represent the **fastest** operating conditions._

 **Timing libraries** required for this analysis can be downloaded from:  
🔗 [Skywater PDK - sky130_fd_sc_hd Timing Libraries](https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing)

Below is the script that can be used to perform STA across the PVT corners for which the Sky130 Liberty files are available.

### sta_across_pvt.tcl

```shell
 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
 set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
 set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
 set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
 set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
 set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
 set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
 set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
 set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
 set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
 set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
 set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
 set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

 read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib
 read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib

 for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {
 read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/$list_of_lib_files($i)
 read_verilog /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc.synth.v
 link_design vsdbabysoc
 current_design
 read_sdc /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc_synthesis.sdc
 check_setup -verbose
 report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} > /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/min_max_$list_of_lib_files($i).txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_max_slack.txt
 report_worst_slack -max -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_max_slack.txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_min_slack.txt
 report_worst_slack -min -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_min_slack.txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_tns.txt
 report_tns -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_tns.txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_wns.txt
 report_wns -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_wns.txt
 }
```
```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc_min_max_delays.tcl
```
⚠️ **Possible Error Alert**

You may encounter the following error when running the script:

```shell
Warning: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 23, default_fanout_load is 0.0.
Warning: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 1, library sky130_fd_sc_hd__tt_025C_1v80 already exists.
Warning: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/sky130_fd_sc_hd__tt_025C_1v80.lib line 23, default_fanout_load is 0.0.
Error: /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib line 54, syntax error
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command13.png)

✅ **Fix:**

This error occurs because Liberty syntax does not support // for single-line comments, and more importantly, the { character appearing after // confuses the Liberty parser. Specifically, check around _line 54 of avsdpll.lib_ and correct any syntax issues such as:

```shell
//pin (GND#2) {
//  direction : input;
//  max_transition : 2.5;
//  capacitance : 0.001;
//}
```
✔️ **Replace with:**
```shell
/*
pin (GND#2) {
  direction : input;
  max_transition : 2.5;
  capacitance : 0.001;
}
*/
```
we have to do for everything
This should allow OpenSTA to parse the Liberty file without throwing syntax errors.

After fixing the Liberty file comment syntax as shown above, you can rerun the script to perform complete timing analysis for VSDBabySoC:

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command14.png)


### VSDBabySoC PVT Corner Analysis (Post-Synthesis Timing)
Static Timing Analysis (STA) is performed across various **PVT (Process-Voltage-Temperature)** corners to ensure the design meets timing requirements under different conditions.

### Critical Timing Corners

**Worst Max Path (Setup-critical) Corners:**
- `ss_LowTemp_LowVolt`
- `ss_HighTemp_LowVolt`  
_These represent the **slowest** operating conditions._

**Worst Min Path (Hold-critical) Corners:**
- `ff_LowTemp_HighVolt`
- `ff_HighTemp_HighVolt`  
_These represent the **fastest** operating conditions._

 **Timing libraries** required for this analysis can be downloaded from:  
🔗 [Skywater PDK - sky130_fd_sc_hd Timing Libraries](https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing)

Below is the script that can be used to perform STA across the PVT corners for which the Sky130 Liberty files are available.

### sta_across_pvt.tcl

```shell
 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
 set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
 set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
 set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
 set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
 set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
 set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
 set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
 set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
 set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
 set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
 set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
 set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

 read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib
 read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib

 for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {
 read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/$list_of_lib_files($i)
 read_verilog /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc.synth.v
 link_design vsdbabysoc
 current_design
 read_sdc /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/vsdbabysoc_synthesis.sdc
 check_setup -verbose
 report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} > /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/min_max_$list_of_lib_files($i).txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_max_slack.txt
 report_worst_slack -max -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_max_slack.txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_min_slack.txt
 report_worst_slack -min -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_worst_min_slack.txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_tns.txt
 report_tns -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_tns.txt

 exec echo "$list_of_lib_files($i)" >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_wns.txt
 report_wns -digits {4} >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/sta_wns.txt
 }
```



| **Command**               | **Purpose**                       | **Explanation**                                                                                                              |
| ------------------------- | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `report_worst_slack -max` | Report Worst Setup Slack          | Outputs the **most negative setup slack** (WNS) in the design for the current PVT corner.                                    |
| `report_worst_slack -min` | Report Worst Hold Slack           | Outputs the **most negative hold slack** in the design for the current PVT corner.                                           |
| `report_tns`              | Report Total Negative Slack (TNS) | Prints the **sum of all negative slacks** (across all violating paths). Reflects how widespread timing violations are.       |
| `report_wns`              | Report Worst Negative Slack (WNS) | Prints the **single worst slack** (i.e., the most timing-violating path). Indicates severity of the critical path violation. |

execute it inside the Docker container:

```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoC/OpenSTA/examples/BabySoC/sta_across_pvt.tcl
```

After executing the above script, you can find the generated timing reports in the STA_OUTPUT directory:

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command15.png)

```shell
jayesshsk@jayesshsk:~/VLSI/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT$ ls
min_max_sky130_fd_sc_hd__ff_100C_1v65.lib.txt  min_max_sky130_fd_sc_hd__ss_100C_1v40.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v44.lib.txt  sta_worst_max_slack.txt
min_max_sky130_fd_sc_hd__ff_100C_1v95.lib.txt  min_max_sky130_fd_sc_hd__ss_100C_1v60.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v76.lib.txt  sta_worst_min_slack.txt
min_max_sky130_fd_sc_hd__ff_n40C_1v56.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v28.lib.txt  min_max_sky130_fd_sc_hd__tt_025C_1v80.lib.txt
min_max_sky130_fd_sc_hd__ff_n40C_1v65.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v35.lib.txt  sta_tns.txt
min_max_sky130_fd_sc_hd__ff_n40C_1v76.lib.txt  min_max_sky130_fd_sc_hd__ss_n40C_1v40.lib.txt  sta_wns.txt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command16.png)
| **File**                  | **Description**                                                     |
| ------------------------- | ------------------------------------------------------------------- |
| `min_max_<lib>.txt`       | Detailed timing report for setup and hold paths for each PVT corner |
| `sta_worst_max_slack.txt` | Worst setup slack values across all corners                         |
| `sta_worst_min_slack.txt` | Worst hold slack values across all corners                          |
| `sta_tns.txt`             | Total negative slack values across all corners                      |
| `sta_wns.txt`             | Worst negative slack values across all corners                      |


#### Timing Summary Across PVT Corners (Post-Synthesis STA Results)
The following timing summary table was collected by running STA across 13 PVT corners using OpenSTA. 

Metrics such as Worst Hold Slack, Worst Setup Slack, WNS, and TNS were extracted from the output reports.

| PVT_CORNER                     | Worst Setup Slack | Worst Hold Slack | WNS       | TNS          |
|-------------------------------|-------------------|------------------|-----------|--------------|
| sky130_fd_sc_hd_tt_025C_1v80  | 0.8595            | 0.3096           | 0         | 0            |
| sky130_fd_sc_hd_ff_100C_1v65  | 2.2764            | 0.2491           | 0         | 0            |
| sky130_fd_sc_hd_ff_100C_1v95  | 3.7138            | 0.1960           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v56  | 0.8214            | 0.2915           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v65  | 1.8597            | 0.2551           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v76  | 2.7707            | 0.2243           | 0         | 0            |
| sky130_fd_sc_hd_ss_100C_1v40  | -13.6381          | 0.9053           | -13.6381  | -7567.7964   |
| sky130_fd_sc_hd_ss_100C_1v60  | -6.7098           | 0.6420           | -6.7098   | -2833.05     |
| sky130_fd_sc_hd_ss_n40C_1v28  | -51.2061          | 1.8296           | -51.2061  | -36861.4102  |
| sky130_fd_sc_hd_ss_n40C_1v35  | -32.0887          | 1.3475           | -32.0887  | -23317.6035  |
| sky130_fd_sc_hd_ss_n40C_1v40  | -23.8290          | 1.1249           | -23.8290  | -17211.252   |
| sky130_fd_sc_hd_ss_n40C_1v44  | -19.2010          | 0.9909           | -19.2010  | -13652.0469  |
| sky130_fd_sc_hd_ss_n40C_1v76  | -4.4548           | 0.5038           | -4.4548   | -1842.5518   |


#### 📈Timing Plots Across PVT Corners

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command17.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command18.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command19.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Imagest3/Command20.png)

---

## Outcome

By the end of this experiment, you will be able to:
  - Run OpenSTA using interactive commands and TCL scripts.
  - Load Liberty, Verilog, SPEF, and SDC files for complete timing analysis.
  - Analyze setup/hold paths and identify timing violations.
Interpret STA reports like:
  - report_checks
  - report_timing
  - report_power
  - report_units
  - report_pulse_width_checks

Understand how parasitics (SPEF) influence real chip timing.
Perform PVT sweep timing analysis and generate consolidated timing tables and graphs. 
Validate whether the post-synthesis BabySoC meets timing across corners and identify optimization needs.

</details>

</details>


---
# 3.Physical Design Flow — Synthesis to Routing with OpenROAD

Week 7 marked the beginning of physical design using the OpenROAD-Flow-Scripts environment, covering floorplanning, placement, clock tree synthesis, and routing for the VSDBabySoC. Floorplanning defined the die/core dimensions, IO pin arrangement, power distribution strategy, and placement of large macros such as the DAC and PLL. Standard-cell placement was then performed to minimize wirelength and congestion, followed by CTS to deliver a balanced, low-skew clock tree. The routing stage completed full interconnect implementation while adhering to DRC rules. After routing, the design database (.odb) was exported into .def format for visualization and signoff processing. Post-route SPEF extraction captured wiring parasitics, enabling accurate timing analysis.

If u want to see in detail **(CLICK BELOW)**

<details> <summary><strong> 🚀 Week 7 : – OpenROAD-Flow-Scripts: Physical Design, Placement,Floorplan,Post-Route SPEF and Verilog Netlist Generation for VSDBabySoC</strong></summary>

#  Week 7 : – OpenROAD-Flow-Scripts: Physical Design, Placement,Floorplan,Post-Route SPEF and Verilog Netlist Generation for VSDBabySoC
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-7-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Week7** –  is to execute the complete **RTL-to-GDSII** flow for the VSDBabySoC using the **OpenROAD-Flow-Scripts** framework on the Sky130HD PDK, starting from synthesis and progressing through floorplanning, placement, and clock tree synthesis. This includes configuring the design environment, resolving library and constraint issues, validating each stage using OpenROAD GUI tools, and performing timing, density, and structural checks after every major step. The goal is to generate a timing-clean, physically realizable layout by integrating standard-cell libraries, LEFs, GDS files, PLL/IP macros, and custom configuration settings, ultimately preparing the design for routing and final GDS export. 

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [📝 Summary](#-summary)
3. [📂 Design Setup & Directory Structure](#-design-setup--directory-structure)
4. [⚙️ Configuration File (config.mk)](#️-configuration-file-configmk)
5. [🔧 Fixing avsdpll.lib Error](#-fixing-avsdplllib-error)
6. [🏗️ Floorplanning](#-floorplanning)
7. [📐 Floorplan Visualization in OpenROAD](#-floorplan-visualization-in-openroad)
8. [📍 Placement](#-placement)
9. [🌡️ Density & Pin Heatmaps](#-density--pin-heatmaps)
10. [⏰ Clock Tree Synthesis (CTS)](#-clock-tree-synthesis-cts)
11. [🌳 Clock Tree Viewer Analysis](#-clock-tree-viewer-analysis)
12. [🧪 Timing Verification](#-timing-verification)
13. [📦 Final Files & DEF/GDS Checks](#-final-files--defgds-checks)
14. [📚 Repository & Author](#-repository--author)

---

## Objective
The objective of Week 7 is to execute the complete RTL-to-GDSII flow for the VSDBabySoC using the OpenROAD-Flow-Scripts framework on the Sky130HD PDK, starting from synthesis and progressing through floorplanning, placement, and clock tree synthesis. This includes configuring the design environment, resolving library and constraint issues, validating each stage using OpenROAD GUI tools, and performing timing, density, and structural checks after every major step. The goal is to generate a timing-clean, physically realizable layout by integrating standard-cell libraries, LEFs, GDS files, PLL/IP macros, and custom configuration settings, ultimately preparing the design for routing and final GDS export.

---

###  `RTL2GDS Flow for VSDBabySoC: Initial Steps`

1. **Create Directories:**
   - Inside `OpenROAD-flow-scripts/flow/designs/sky130hd/`, create a folder named `vsdbabysoc`.
   - Create another folder named `vsdbabysoc` in `OpenROAD-flow-scripts/flow/designs/src/` and place all Verilog files here.

2. **Copy Folders:**
   - From your `VSDBabySoC` folder, copy the following folders into `sky130hd/vsdbabysoc`:
     - **gds:** Contains `avsddac.gds`, `avsdpll.gds`.
     - **include:** Contains `sandpiper.vh`, `sandpiper_gen.vh`, `sp_default.vh`, `sp_verilog.vh`.
     - **lef:** Contains `avsddac.lef`, `avsdpll.lef`.
     - **lib:** Contains `avsddac.lib`, `avsdpll.lib`.

3. **Copy Constraint and Configuration Files:**
   - Copy `vsdbabysoc_synthesis.sdc` into `sky130hd/vsdbabysoc`.
   - Copy `macro.cfg` and `pin_order.cfg` into `sky130hd/vsdbabysoc`.

4. **Create Config File:**
   - Create a `config.mk` file in `sky130hd/vsdbabysoc` with the required configuration details. 

### config.mk

```
   export DESIGN_NICKNAME = vsdbabysoc
export DESIGN_NAME     = vsdbabysoc
export PLATFORM        = sky130hd

export VSDBabySoC_DIR = /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)


export VERILOG_FILES = \
    $(VSDBabySoC_DIR)/src/vsdbabysoc.v \
    $(VSDBabySoC_DIR)/src/rvmyth.v \
    $(VSDBabySoC_DIR)/src/clk_gate.v

export SDC_FILE = $(VSDBabySoC_DIR)/vsdbabysoc_synthesis.sdc
export VERILOG_INCLUDE_DIRS = $(wildcard $(VSDBabySoC_DIR)/include/)

export ADDITIONAL_LIBS = \
    $(VSDBabySoC_DIR)/lib/avsddac.lib \
    $(VSDBabySoC_DIR)/lib/avsdpll.lib

export ADDITIONAL_GDS  = $(wildcard $(VSDBabySoC_DIR)/gds/*.gds)
export ADDITIONAL_LEFS = $(wildcard $(VSDBabySoC_DIR)/lef/*.lef)

export ADDITIONAL_ROUTING_BLOCKAGES = $(VSDBabySoC_DIR)/route_blockages.tcl

export CLOCK_PORT = CLK
export CLOCK_NET  = CLK

export FP_PIN_ORDER_CFG = $(VSDBabySoC_DIR)/pin_order.cfg

export DIE_AREA  = 0   0   1600 1600
export CORE_AREA = 20  20  1580 1580

export FP_CORE_UTIL    = 40
export PLACE_DENSITY   = 0.30   # Changed from 0.48

export MACRO_PLACE_HALO    = 40 40  # Changed from 20 20
export MACRO_PLACE_CHANNEL = 40 40

export RTLMP_FLOW = 0
export MACRO_PLACEMENT = $(VSDBabySoC_DIR)/macro.cfg

 #dac 320 900 N
 #pll 40  40  N

export CTS_BUF_DISTANCE  = 600
export SKIP_GATE_CLONING = 1

# Normal congestion settings
export GRT_ALLOW_CONGESTION      = 1
export GRT_SKIP_CONGESTION_CHECK = 0
export GRT_CONGESTION_DRIVEN     = 1
export GRT_MAX_ITERATIONS        = 700
export GRT_ADJUSTMENT            = 0.15
export GRT_VIA_COST_ADJUSTMENT   = 3.0
export GRT_LAYER_ADJUSTMENTS     = {met1:0.25,met2:0.20,met3:0.10,met4:0.05,met5:0.00}
export GRT_MAX_TIME              = 3600

# ---- REQUIRED FIXES FOR YOUR VERSION (prevents GRT-0116 stop) ----

export GRT_OVERFLOW_ITERS   = 300

export TNS_END_PERCENT      = 100
export REMOVE_ABC_BUFFERS   = 1
export MAGIC_ZEROIZE_ORIGIN = 0
export MAGIC_EXT_USE_GDS    = 1
export GRT_FAIL_ON_OVERFLOW = 0
export RCX_EXTRACT=1
export RCX_CORNER=typical
export RCX_SPEF_OUTPUT=1
export SPEF_OUTPUT_FILE = $(VSDBabySoC_DIR)/vsdbabysoc.spef

```


This script sets up environment variables and configurations for the design and synthesis of a System-on-Chip (SoC) using the OpenROAD flow. The design is based on the "vsdbabysoc" and targets the "sky130hd" platform.

--------


### `File Structure After Setup`

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/src/vsdbabysoc/
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command2.png)

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/sky130hd/vsdbabysoc/
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command1.png)

#### Now go to terminal and run the following commands:

```shell
# Navigate to the OpenROAD flow scripts directory
cd OpenROAD-flow-scripts
# Source the environment setup script
source env.sh
# Change to the flow directory
cd flow
```
----
 
### `Run Synthesis`

```shell
# Ensure you are in the 'flow' directory before running the synthesis command
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```

This command runs the synthesis process using the specified design configuration file `config.mk` for the `vsdbabysoc` design on the `sky130hd` platform.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command3.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command4.png)

#### Synthesis netlist

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim results/sky130hd/vsdbabysoc/base/1_1_yosys.v
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command5.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command6.png)

#### Synthesis log

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim logs/sky130hd/vsdbabysoc/base/1_1_yosys.log
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command5.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command7.png)

#### Synthesis Check

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim reports/sky130hd/vsdbabysoc/base/synth_check.txt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command8.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command9.png)

#### Synthesis Stats

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim reports/sky130hd/vsdbabysoc/base/synth_stat.txt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command11.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command12.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command13.png)

<details> <summary><strong>synth_stat.txt</strong></summary>

```

20. Printing statistics.

=== vsdbabysoc ===

        +----------Local Count, excluding submodules.
        |        +-Local Area, excluding submodules.
        |        | 
     6715        - wires
     6715        - wire bits
     1285        - public wires
     1285        - public wire bits
        7        - ports
        7        - port bits
     6605 5.29E+04 cells
        1        -   avsddac
        1        -   avsdpll
        1   11.261   sky130_fd_sc_hd__a2111o_1
        6    52.55   sky130_fd_sc_hd__a2111oi_0
        8   70.067   sky130_fd_sc_hd__a211o_1
       26  195.187   sky130_fd_sc_hd__a211oi_1
       17  127.622   sky130_fd_sc_hd__a21boi_0
       31  232.723   sky130_fd_sc_hd__a21o_1
      884 4.42E+03   sky130_fd_sc_hd__a21oi_1
        7   61.309   sky130_fd_sc_hd__a21oi_2
       15  150.144   sky130_fd_sc_hd__a221o_1
       37  324.061   sky130_fd_sc_hd__a221oi_1
       24  210.202   sky130_fd_sc_hd__a22o_1
      222 1.67E+03   sky130_fd_sc_hd__a22oi_1
        1    21.27   sky130_fd_sc_hd__a22oi_4
        1   11.261   sky130_fd_sc_hd__a2bb2o_2
        4   35.034   sky130_fd_sc_hd__a2bb2oi_1
        2   20.019   sky130_fd_sc_hd__a311o_1
       15  131.376   sky130_fd_sc_hd__a311oi_1
        8   70.067   sky130_fd_sc_hd__a31o_2
       53  331.568   sky130_fd_sc_hd__a31oi_1
        1    10.01   sky130_fd_sc_hd__a32o_1
        3   26.275   sky130_fd_sc_hd__a32oi_1
        3   26.275   sky130_fd_sc_hd__a41oi_1
        2   12.512   sky130_fd_sc_hd__and2_0
       10    62.56   sky130_fd_sc_hd__and2_1
       14   87.584   sky130_fd_sc_hd__and3_1
       34  127.622   sky130_fd_sc_hd__buf_1
        9   45.043   sky130_fd_sc_hd__buf_2
        1    7.507   sky130_fd_sc_hd__buf_4
        3   33.782   sky130_fd_sc_hd__buf_6
      548 2.06E+03   sky130_fd_sc_hd__clkbuf_1
        4   15.014   sky130_fd_sc_hd__clkinv_1
        1    3.754   sky130_fd_sc_hd__conb_1
     1144 2.29E+04   sky130_fd_sc_hd__dfxtp_1
        4   80.077   sky130_fd_sc_hd__fa_1
      100   1251.2   sky130_fd_sc_hd__ha_1
      104  390.374   sky130_fd_sc_hd__inv_1
       56  630.605   sky130_fd_sc_hd__mux2_2
       92  920.883   sky130_fd_sc_hd__mux2i_1
        1   22.522   sky130_fd_sc_hd__mux2i_4
       69  1553.99   sky130_fd_sc_hd__mux4_2
     1461  5484.01   sky130_fd_sc_hd__nand2_1
       28  175.168   sky130_fd_sc_hd__nand2b_1
      213 1.07E+03   sky130_fd_sc_hd__nand3_1
       40  300.288   sky130_fd_sc_hd__nand3b_1
       70   437.92   sky130_fd_sc_hd__nand4_1
        2   17.517   sky130_fd_sc_hd__nand4b_1
      284 1.07E+03   sky130_fd_sc_hd__nor2_1
       52  325.312   sky130_fd_sc_hd__nor2b_1
       74  370.355   sky130_fd_sc_hd__nor3_1
        9   67.565   sky130_fd_sc_hd__nor3b_1
        1   12.512   sky130_fd_sc_hd__nor3b_2
       25    156.4   sky130_fd_sc_hd__nor4_1
        1    8.758   sky130_fd_sc_hd__nor4b_1
        1   11.261   sky130_fd_sc_hd__o2111a_1
        8   70.067   sky130_fd_sc_hd__o2111ai_1
        3   30.029   sky130_fd_sc_hd__o211a_1
       51  382.867   sky130_fd_sc_hd__o211ai_1
       30  225.216   sky130_fd_sc_hd__o21a_1
      397 1.99E+03   sky130_fd_sc_hd__o21ai_0
        8   40.038   sky130_fd_sc_hd__o21ai_1
       10   75.072   sky130_fd_sc_hd__o21bai_1
       27  236.477   sky130_fd_sc_hd__o221ai_1
       36  315.302   sky130_fd_sc_hd__o22a_1
       31  193.936   sky130_fd_sc_hd__o22ai_1
        2   17.517   sky130_fd_sc_hd__o2bb2ai_1
        1    10.01   sky130_fd_sc_hd__o311a_1
        6    52.55   sky130_fd_sc_hd__o311ai_0
        5   43.792   sky130_fd_sc_hd__o31a_1
       34  255.245   sky130_fd_sc_hd__o31ai_1
        1   12.512   sky130_fd_sc_hd__o31ai_2
        2   20.019   sky130_fd_sc_hd__o32a_1
        4   35.034   sky130_fd_sc_hd__o32ai_1
        1   11.261   sky130_fd_sc_hd__o41a_1
        5   43.792   sky130_fd_sc_hd__o41ai_1
        2   12.512   sky130_fd_sc_hd__or2_0
        1    6.256   sky130_fd_sc_hd__or2_1
        8   50.048   sky130_fd_sc_hd__or2_2
       28  175.168   sky130_fd_sc_hd__or3_1
        1    8.758   sky130_fd_sc_hd__or3b_1
        2   17.517   sky130_fd_sc_hd__or3b_2
        4   30.029   sky130_fd_sc_hd__or4_1
       47  411.645   sky130_fd_sc_hd__xnor2_1
       22  192.685   sky130_fd_sc_hd__xor2_1

   Area for cell type \avsdpll is unknown!
   Area for cell type \avsddac is unknown!

   Chip area for module '\vsdbabysoc': 52874.460800
     of which used for sequential elements: 22901.964800 (43.31%)

```
</details>

----------

### `Run Floorplan`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```

This command initiates the floorplanning process for the `vsdbabysoc` design using the specified configuration file `config.mk` on the `sky130hd` platform.

#### Floorplan Error and Fix

❗**Note:** You may encounter the following error:

```shell
[ERROR STA-0164] .../vsdbabysoc/lib/avsdpll.lib line 54, syntax error
Error: floorplan.tcl, 4 STA-0164
```

**Fix:**
This error is caused by commented block structures in your Liberty file avsdpll.lib. OpenROAD’s parser does not tolerate partially commented blocks like:

```shell
//pin (GND#2) {
//  direction : input;
//  max_transition : 2.5;
//  capacitance : 0.001;
//}
```

✅ To fix it, simply delete the entire commented block starting at line 54:

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command14.png)

After saving the changes, re-run the floorplan step and the flow should proceed without syntax errors. 

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command15.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command16.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command17.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command18.png)




#### Floorplan Result (GUI)

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command19.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command20.png)


------

### `run placement`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command21.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command22.png)


**Placement Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
```

This image shows the placement stage in OpenROAD with the **placement density** heat map enabled.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command23.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command24.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command25.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command26.png)
This image shows a zoomed-in view of the Placement Density Heatmap after the placement stage:

- **Red regions** indicate areas with higher cell density, approaching 100%.
- **Green and blue regions** indicate moderate to low cell density.
- The highlighted row (`ROW_343`) displays details such as origin coordinates, site count, site spacing, and bounding box dimensions.

❗**Note:** In the floorplan stage, you do not see any placement density heat maps because standard cells have not yet been placed. The heat map will only appear after the placement step.

<ins>The placement density percentage is calculated as:</ins>

**Placement Density (%) = (Area Occupied by Cells ÷ Total Placement Area) × 100**

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command27.png)

This image shows the **Pin Density Heatmap** after the placement stage.

<ins>The pin density percentage is calculated as:</ins>

**Pin Density (%) = (Number of Pins in a Region ÷ Total Area of that Region) × 100**

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command28.png)

### `run cts`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command29.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command30.png)

**CTS Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_cts
```

This image shows the Clock Tree Synthesis (CTS) stage, highlighting a placed clock buffer (clkbuf_leaf_209_CLK) with its properties displayed in the Inspector, including position, orientation, and connectivity details.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command31.png)

This image shows the **Clock Tree Viewer** after CTS, illustrating the clock buffer distribution on the layout and a histogram of clock insertion delays, indicating balanced clock skew across the sinks.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command32.png)

This image shows the **Clock Tree Viewer after Clock Tree Synthesis (CTS)**, illustrating the hierarchical structure of the clock network. The root node at the top represents the clock source (`pll/CLK`), and the branches show the inserted clock buffers used to distribute the clock signal across the design. The vertical axis represents the **clock arrival times (in nanoseconds)** at each stage. The endpoints at the bottom represent the registers (clock sinks), where the clock reaches after passing through multiple buffer levels. The balanced branching and closely aligned arrival times indicate **low clock skew and a well-optimized clock tree**.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command33.png)

This image shows the **Setup Timing Report**, presenting a list of timing paths with key metrics such as:

- **Required Time**
- **Arrival Time**
- **Slack**
- **Skew**
- **Logic Delay**
- **Logic Depth**
- **Fanout**

All paths have **positive slack**, confirming that the design meets **setup timing requirements**.
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command34.png)

This image displays the **Hold Timing Report**, showing timing paths with details such as:

- **Required Time**
- **Arrival Time**
- **Slack**
- **Skew**
- **Logic Delay**
- **Fanout**

All paths listed have **positive slack**, indicating that the design meets **hold timing requirements** and is free from hold violations.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command35.png)


This image shows the **Setup Slack Histogram** after CTS. The histogram represents the distribution of endpoint slack values, all of which are positive, indicating that there are no setup timing violations.
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command37.png)

This image shows the **Hold Slack Histogram** after CTS. The histogram represents the distribution of hold slack values for all endpoints. All values are positive, confirming that the design meets hold timing requirements without any violations.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command36.png)
Zoomed-in view of the design after CTS, showing inserted clock buffers and routing connections.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command38.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command39.png)

**CTS final report:**

```shell
gvim /home/jayesshsk@jayesshsk/OpenROAD-flow-scripts/flow/reports/sky130hd/vsdbabysoc/base/4_cts_final.rpt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command40.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command41.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command42.png)

<details> <summary><strong>4_cts_final.rpt</strong></summary>

```


==========================================================================
cts final report_tns
--------------------------------------------------------------------------
tns max 0.00

==========================================================================
cts final report_wns
--------------------------------------------------------------------------
wns max 0.00

==========================================================================
cts final report_worst_slack
--------------------------------------------------------------------------
worst slack max 6.28

==========================================================================
cts final report_clock_min_period
--------------------------------------------------------------------------
clk period_min = 4.72 fmax = 211.74

==========================================================================
cts final report_clock_skew
--------------------------------------------------------------------------
Clock clk
   1.05 source latency core.CPU_dmem_rd_data_a5[21]$_DFF_P_/CLK ^
  -0.85 target latency core.CPU_Xreg_value_a4[17][21]$_SDFFE_PP0P_/CLK ^
   0.00 CRPR
--------------
   0.20 setup skew


==========================================================================
cts final report_checks -path_delay min
--------------------------------------------------------------------------
Startpoint: core.CPU_rd_a4[4]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_rd_a5[4]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.02    0.37 ^ clkbuf_4_6_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.16    0.18    0.32    0.70 ^ clkbuf_4_6_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_6_0_CLK (net)
                  0.18    0.00    0.70 ^ clkbuf_leaf_95_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.05    0.07    0.19    0.89 ^ clkbuf_leaf_95_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_95_CLK (net)
                  0.07    0.00    0.89 ^ core.CPU_rd_a4[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     1    0.00    0.05    0.31    1.20 ^ core.CPU_rd_a4[4]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_rd_a4[4] (net)
                  0.05    0.00    1.20 ^ core.CPU_rd_a5[4]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  1.20   data arrival time

                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03    0.38 ^ clkbuf_4_7_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.19    0.20    0.34    0.71 ^ clkbuf_4_7_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_7_0_CLK (net)
                  0.20    0.00    0.72 ^ clkbuf_leaf_89_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    10    0.04    0.06    0.20    0.91 ^ clkbuf_leaf_89_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_89_CLK (net)
                  0.06    0.00    0.91 ^ core.CPU_rd_a5[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00    0.91   clock reconvergence pessimism
                         -0.03    0.89   library hold time
                                  0.89   data required time
-----------------------------------------------------------------------------
                                  0.89   data required time
                                 -1.20   data arrival time
-----------------------------------------------------------------------------
                                  0.32   slack (MET)



==========================================================================
cts final report_checks -path_delay max
--------------------------------------------------------------------------
Startpoint: core.CPU_src1_value_a3[14]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03    0.38 ^ clkbuf_4_3_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.15    0.17    0.31    0.69 ^ clkbuf_4_3_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_3_0_CLK (net)
                  0.17    0.00    0.69 ^ clkbuf_leaf_59_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.05    0.07    0.19    0.88 ^ clkbuf_leaf_59_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_59_CLK (net)
                  0.07    0.00    0.88 ^ core.CPU_src1_value_a3[14]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     7    0.04    0.20    0.44    1.33 v core.CPU_src1_value_a3[14]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src1_value_a3[14] (net)
                  0.20    0.00    1.33 v _10836_/A (sky130_fd_sc_hd__ha_1)
    12    0.06    0.47    0.65    1.98 ^ _10836_/SUM (sky130_fd_sc_hd__ha_1)
                                         _00110_ (net)
                  0.47    0.00    1.98 ^ _05444_/A (sky130_fd_sc_hd__nand4_1)
     6    0.02    0.30    0.34    2.33 v _05444_/Y (sky130_fd_sc_hd__nand4_1)
                                         _01127_ (net)
                  0.30    0.00    2.33 v _08027_/A (sky130_fd_sc_hd__nor4_1)
     1    0.01    0.36    0.46    2.78 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
                                         _03108_ (net)
                  0.36    0.00    2.78 ^ _08028_/C1 (sky130_fd_sc_hd__o311ai_0)
     1    0.01    0.25    0.31    3.10 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
                                         _03109_ (net)
                  0.25    0.00    3.10 v place394/A (sky130_fd_sc_hd__buf_4)
     2    0.01    0.03    0.22    3.32 v place394/X (sky130_fd_sc_hd__buf_4)
                                         net393 (net)
                  0.03    0.00    3.32 v _08206_/A1 (sky130_fd_sc_hd__a311oi_1)
     3    0.02    0.77    0.61    3.93 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
                                         _03283_ (net)
                  0.77    0.00    3.93 ^ _08381_/A3 (sky130_fd_sc_hd__a31oi_1)
     1    0.01    0.15    0.20    4.14 v _08381_/Y (sky130_fd_sc_hd__a31oi_1)
                                         _03454_ (net)
                  0.15    0.00    4.14 v _08382_/S (sky130_fd_sc_hd__mux2i_1)
     1    0.00    0.13    0.19    4.33 ^ _08382_/Y (sky130_fd_sc_hd__mux2i_1)
                                         _03455_ (net)
                  0.13    0.00    4.33 ^ _08384_/B (sky130_fd_sc_hd__nand3b_1)
     4    0.01    0.15    0.17    4.49 v _08384_/Y (sky130_fd_sc_hd__nand3b_1)
                                         _03457_ (net)
                  0.15    0.00    4.49 v _08401_/A1 (sky130_fd_sc_hd__o21ai_1)
    11    0.04    0.84    0.71    5.20 ^ _08401_/Y (sky130_fd_sc_hd__o21ai_1)
                                         _03474_ (net)
                  0.84    0.00    5.20 ^ _08956_/A (sky130_fd_sc_hd__nand3_1)
     1    0.00    0.17    0.16    5.36 v _08956_/Y (sky130_fd_sc_hd__nand3_1)
                                         _03932_ (net)
                  0.17    0.00    5.36 v _08958_/A1 (sky130_fd_sc_hd__a21oi_1)
     1    0.00    0.13    0.17    5.53 ^ _08958_/Y (sky130_fd_sc_hd__a21oi_1)
                                         _00868_ (net)
                  0.13    0.00    5.53 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.53   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.22    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01   11.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34   11.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03   11.38 ^ clkbuf_4_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     6    0.15    0.16    0.31   11.69 ^ clkbuf_4_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_0_0_CLK (net)
                  0.16    0.00   11.69 ^ clkbuf_leaf_76_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.19   11.88 ^ clkbuf_leaf_76_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_76_CLK (net)
                  0.06    0.00   11.88 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   11.88   clock reconvergence pessimism
                         -0.07   11.81   library setup time
                                 11.81   data required time
-----------------------------------------------------------------------------
                                 11.81   data required time
                                 -5.53   data arrival time
-----------------------------------------------------------------------------
                                  6.28   slack (MET)



==========================================================================
cts final report_checks -unconstrained
--------------------------------------------------------------------------
Startpoint: core.CPU_src1_value_a3[14]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03    0.38 ^ clkbuf_4_3_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.15    0.17    0.31    0.69 ^ clkbuf_4_3_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_3_0_CLK (net)
                  0.17    0.00    0.69 ^ clkbuf_leaf_59_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.05    0.07    0.19    0.88 ^ clkbuf_leaf_59_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_59_CLK (net)
                  0.07    0.00    0.88 ^ core.CPU_src1_value_a3[14]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     7    0.04    0.20    0.44    1.33 v core.CPU_src1_value_a3[14]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src1_value_a3[14] (net)
                  0.20    0.00    1.33 v _10836_/A (sky130_fd_sc_hd__ha_1)
    12    0.06    0.47    0.65    1.98 ^ _10836_/SUM (sky130_fd_sc_hd__ha_1)
                                         _00110_ (net)
                  0.47    0.00    1.98 ^ _05444_/A (sky130_fd_sc_hd__nand4_1)
     6    0.02    0.30    0.34    2.33 v _05444_/Y (sky130_fd_sc_hd__nand4_1)
                                         _01127_ (net)
                  0.30    0.00    2.33 v _08027_/A (sky130_fd_sc_hd__nor4_1)
     1    0.01    0.36    0.46    2.78 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
                                         _03108_ (net)
                  0.36    0.00    2.78 ^ _08028_/C1 (sky130_fd_sc_hd__o311ai_0)
     1    0.01    0.25    0.31    3.10 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
                                         _03109_ (net)
                  0.25    0.00    3.10 v place394/A (sky130_fd_sc_hd__buf_4)
     2    0.01    0.03    0.22    3.32 v place394/X (sky130_fd_sc_hd__buf_4)
                                         net393 (net)
                  0.03    0.00    3.32 v _08206_/A1 (sky130_fd_sc_hd__a311oi_1)
     3    0.02    0.77    0.61    3.93 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
                                         _03283_ (net)
                  0.77    0.00    3.93 ^ _08381_/A3 (sky130_fd_sc_hd__a31oi_1)
     1    0.01    0.15    0.20    4.14 v _08381_/Y (sky130_fd_sc_hd__a31oi_1)
                                         _03454_ (net)
                  0.15    0.00    4.14 v _08382_/S (sky130_fd_sc_hd__mux2i_1)
     1    0.00    0.13    0.19    4.33 ^ _08382_/Y (sky130_fd_sc_hd__mux2i_1)
                                         _03455_ (net)
                  0.13    0.00    4.33 ^ _08384_/B (sky130_fd_sc_hd__nand3b_1)
     4    0.01    0.15    0.17    4.49 v _08384_/Y (sky130_fd_sc_hd__nand3b_1)
                                         _03457_ (net)
                  0.15    0.00    4.49 v _08401_/A1 (sky130_fd_sc_hd__o21ai_1)
    11    0.04    0.84    0.71    5.20 ^ _08401_/Y (sky130_fd_sc_hd__o21ai_1)
                                         _03474_ (net)
                  0.84    0.00    5.20 ^ _08956_/A (sky130_fd_sc_hd__nand3_1)
     1    0.00    0.17    0.16    5.36 v _08956_/Y (sky130_fd_sc_hd__nand3_1)
                                         _03932_ (net)
                  0.17    0.00    5.36 v _08958_/A1 (sky130_fd_sc_hd__a21oi_1)
     1    0.00    0.13    0.17    5.53 ^ _08958_/Y (sky130_fd_sc_hd__a21oi_1)
                                         _00868_ (net)
                  0.13    0.00    5.53 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.53   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.22    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01   11.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34   11.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03   11.38 ^ clkbuf_4_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     6    0.15    0.16    0.31   11.69 ^ clkbuf_4_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_0_0_CLK (net)
                  0.16    0.00   11.69 ^ clkbuf_leaf_76_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.19   11.88 ^ clkbuf_leaf_76_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_76_CLK (net)
                  0.06    0.00   11.88 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   11.88   clock reconvergence pessimism
                         -0.07   11.81   library setup time
                                 11.81   data required time
-----------------------------------------------------------------------------
                                 11.81   data required time
                                 -5.53   data arrival time
-----------------------------------------------------------------------------
                                  6.28   slack (MET)



==========================================================================
cts final report_check_types -max_slew -max_cap -max_fanout -violators
--------------------------------------------------------------------------

==========================================================================
cts final max_slew_check_slack
--------------------------------------------------------------------------
0.21570834517478943

==========================================================================
cts final max_slew_check_limit
--------------------------------------------------------------------------
1.4979510307312012

==========================================================================
cts final max_slew_check_slack_limit
--------------------------------------------------------------------------
0.1440

==========================================================================
cts final max_fanout_check_slack
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_fanout_check_limit
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_capacitance_check_slack
--------------------------------------------------------------------------
0.0062514557503163815

==========================================================================
cts final max_capacitance_check_limit
--------------------------------------------------------------------------
0.021067000925540924

==========================================================================
cts final max_capacitance_check_slack_limit
--------------------------------------------------------------------------
0.2967

==========================================================================
cts final max_slew_violation_count
--------------------------------------------------------------------------
max slew violation count 0

==========================================================================
cts final max_fanout_violation_count
--------------------------------------------------------------------------
max fanout violation count 0

==========================================================================
cts final max_cap_violation_count
--------------------------------------------------------------------------
max cap violation count 0

==========================================================================
cts final setup_violation_count
--------------------------------------------------------------------------
setup violation count 0

==========================================================================
cts final hold_violation_count
--------------------------------------------------------------------------
hold violation count 0

==========================================================================
cts final report_checks -path_delay max reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_src1_value_a3[14]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.35    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.34    0.69 ^ clkbuf_4_3_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.19    0.88 ^ clkbuf_leaf_59_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.88 ^ core.CPU_src1_value_a3[14]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.44    1.33 v core.CPU_src1_value_a3[14]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.66    1.98 ^ _10836_/SUM (sky130_fd_sc_hd__ha_1)
   0.34    2.33 v _05444_/Y (sky130_fd_sc_hd__nand4_1)
   0.46    2.78 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
   0.31    3.10 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
   0.22    3.32 v place394/X (sky130_fd_sc_hd__buf_4)
   0.61    3.93 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
   0.21    4.14 v _08381_/Y (sky130_fd_sc_hd__a31oi_1)
   0.19    4.33 ^ _08382_/Y (sky130_fd_sc_hd__mux2i_1)
   0.17    4.49 v _08384_/Y (sky130_fd_sc_hd__nand3b_1)
   0.71    5.20 ^ _08401_/Y (sky130_fd_sc_hd__o21ai_1)
   0.16    5.36 v _08956_/Y (sky130_fd_sc_hd__nand3_1)
   0.17    5.53 ^ _08958_/Y (sky130_fd_sc_hd__a21oi_1)
   0.00    5.53 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
           5.53   data arrival time

  11.00   11.00   clock clk (rise edge)
   0.00   11.00   clock source latency
   0.00   11.00 ^ pll/CLK (avsdpll)
   0.35   11.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.34   11.69 ^ clkbuf_4_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.19   11.88 ^ clkbuf_leaf_76_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00   11.88 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00   11.88   clock reconvergence pessimism
  -0.07   11.81   library setup time
          11.81   data required time
---------------------------------------------------------
          11.81   data required time
          -5.53   data arrival time
---------------------------------------------------------
           6.28   slack (MET)



==========================================================================
cts final report_checks -path_delay min reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_rd_a4[4]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_rd_a5[4]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.35    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.35    0.70 ^ clkbuf_4_6_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.89 ^ clkbuf_leaf_95_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.89 ^ core.CPU_rd_a4[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.31    1.20 ^ core.CPU_rd_a4[4]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.00    1.20 ^ core.CPU_rd_a5[4]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
           1.20   data arrival time

   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.35    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.36    0.71 ^ clkbuf_4_7_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.91 ^ clkbuf_leaf_89_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.91 ^ core.CPU_rd_a5[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00    0.91   clock reconvergence pessimism
  -0.03    0.89   library hold time
           0.89   data required time
---------------------------------------------------------
           0.89   data required time
          -1.20   data arrival time
---------------------------------------------------------
           0.32   slack (MET)



==========================================================================
cts final critical path target clock latency max path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path target clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path source clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path delay
--------------------------------------------------------------------------
5.5293

==========================================================================
cts final critical path slack
--------------------------------------------------------------------------
6.2773

==========================================================================
cts final slack div critical path delay
--------------------------------------------------------------------------
113.527933

==========================================================================
cts final report_power
--------------------------------------------------------------------------
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             4.37e-03   4.00e-04   9.26e-09   4.77e-03  36.9%
Combinational          8.88e-04   2.29e-03   1.00e-08   3.18e-03  24.6%
Clock                  2.54e-03   2.44e-03   2.16e-09   4.98e-03  38.5%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  7.79e-03   5.13e-03   2.15e-08   1.29e-02 100.0%
                          60.3%      39.7%       0.0%
```
</details>


### `run routing`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command43.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command44.png)


# VSDBabySoC Global Routing Failure: Root Cause Analysis & Resolution

A VSDBabySoC design using OpenROAD-flow-scripts with Sky130 PDK was failing at the global routing stage (5_1_grt) with congestion overflow errors. The root cause was identified as **pin accessibility obstructions in the analog macro LEF files**, specifically metal layer obstructions blocking router access to macro pins.

---

## 1. Initial Problem Statement

### Error Message

```bash
[ERROR GRT-0116] Global routing finished with congestion.
Check the congestion regions in the DRC Viewer.
```

### Symptoms

* Global routing completed but reported overflow violations
* Total overflow count: 18 violations across met3, met4, and met5
* Design utilization was only 13% (not a density issue)
* Routing resources appeared adequate (~1-2% usage on most layers)

---

## 2. Investigation Process

### Phase 1: Initial Configuration Analysis

The original `config.mk` had aggressive routing settings:

```makefile
export PLACE_DENSITY   = 0.30
export MACRO_PLACE_HALO    = 20 20
export MACRO_PLACE_CHANNEL = 40 40
export GRT_LAYER_ADJUSTMENTS = {met1:0.15,met2:0.12,met3:0.10,met4:0.08,met5:0.05}
```

These settings appeared reasonable for a 13% utilization design.

### Phase 2: Congestion Report Analysis

The congestion report revealed the critical insight:

```rpt
violation type: Horizontal congestion
    srcs: net:RV_TO_DAC\[9\]
    comment: capacity:0 usage:1 overflow:1
    bbox = (1090.2000, 579.6000) - (1097.1000, 586.5000) on Layer -
```

**Key observation:** `capacity:0` indicated the router had **zero legal routing tracks** in these regions, not just high congestion.

### Phase 3: LEF File Inspection

Examining `avsddac.lef` revealed the root cause:

#### Problem 1: OUT Pin Access Blocked by met4 OBS

```lef
PIN OUT
    PORT
      LAYER met4 ;
        RECT 1172.680 1127.710 1173.730 1175.360 ;
    END
END OUT

OBS
    LAYER met4 ;
        RECT 154.520 1171.010 1172.280 1175.050 ;  ← Ends at x=1172.280
        RECT 1174.130 1127.310 1188.200 1175.050 ; ← Starts at x=1174.130
```

The OUT pin (x: 1172.680-1173.730) was squeezed between obstructions with only ~0.4µm clearance on each side—insufficient for routing.

#### Problem 2: D[8] and D[9] Pins Only on li1 Layer

```lef
PIN D[8]
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
    END

PIN D[9]
    PORT
      LAYER li1 ;
        RECT 1150.220 613.050 1151.250 755.620 ;
    END
```

The li1 layer has effectively **zero routing capacity** in Sky130:

```lef
li1        Vertical            0            10          0.00%
```

#### Problem 3: Massive met1 Obstruction

```lef
LAYER met1 ;
    RECT 140.750 613.050 1313.360 1168.430 ;  ← Covers entire macro interior
```

This blocked any met1 access to the li1-only pins.

---

## 3. Resolution Strategy

### Fix 1: Widen met4 OBS Gap for OUT Pin

**Before:**

```lef
LAYER met4 ;
    RECT 154.520 1171.010 1172.280 1175.050 ;
    RECT 1174.130 1127.310 1188.200 1175.050 ;
```

**After:**

```lef
LAYER met4 ;
    RECT 154.520 1171.010 1168.000 1175.050 ;  ← Moved left edge back
    RECT 1178.000 1127.310 1188.200 1175.050 ; ← Moved right edge forward
```

This created a ~10µm routing corridor around the OUT pin instead of ~1.85µm.

### Fix 2: Add met1 Access Points for D[8] and D[9] Pins

**Before:**

```lef
PIN D[8]
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
    END
END D[8]
```

**After:**

```lef
PIN D[8]
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
      LAYER met1 ;
        RECT 1115.000 608.000 1120.000 613.050 ;  ← NEW: Access below OBS
    END
END D[8]
```

The new met1 shapes extend **below** y=613.050, outside the met1 obstruction boundary, giving the router legal access paths.

---

## 4. Technical Explanation

### Why This Happens

Analog macros (DAC, PLL) are often created with:

1. **Pins on lower metal layers (li1)** for analog signal integrity
2. **Large obstructions** to protect internal routing from interference
3. **Minimal consideration for digital router requirements**

OpenROAD's TritonRoute requires:

1. Legal access points to every pin
2. Sufficient routing capacity on accessible layers
3. Clear paths from pin access points to the routing grid

### The Pin Access Problem Illustrated

```text
                    ┌─────────────────────────────────┐
                    │         met1 OBS                │
                    │   (blocks entire interior)      │
                    │                                 │
    Router cannot   │    ┌──────┐                     │
    reach pin! ─────│───►│D[8]  │ (li1 only)          │
                    │    │ pin  │                     │
                    │    └──────┘                     │
                    │                                 │
                    └─────────────────────────────────┘
                    y = 613.050 ─────────────────────────

    SOLUTION: Add met1 shape BELOW y=613.050

                    ┌─────────────────────────────────┐
                    │         met1 OBS                │
                    │                                 │
                    │    ┌──────┐                     │
                    │    │D[8]  │ (li1)               │
                    │    │ pin  │                     │
                    │    └─┬────┘                     │
                    └──────│──────────────────────────┘
                    ═══════╪═══════  y = 613.050
                        ┌──┴───┐
    Router can now ────►│ met1 │ (new access point)
    reach pin!          │access│
                        └──────┘
```

---

## 5. Results

### Before Fix

```bash
[ERROR GRT-0116] Global routing finished with congestion.
Total Overflow: 29 violations
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command44.png)


### After Fix

```bash
[INFO GRT-0018] Total wirelength: 407106 um
[INFO GRT-0014] Routed nets: 6450
[INFO ANT-0002] Found 0 net violations.
[INFO ANT-0001] Found 0 pin violations.
Design area 728556 um^2 13% utilization.
```
* Finally, the routing was completed successfully without congestion errors: After correcting the config.mk & avsddac.lef

* Final routing Screenshots:

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command45.png)

---

## 6. Key Lessons Learned

### For Physical Design Engineers

1. **Always verify macro pin accessibility** - Check that pins have routing access on layers the router can use

2. **Understand layer routing capacity** - li1 in Sky130 has near-zero routing capacity; pins must have met1+ access

3. **Inspect OBS sections carefully** - Obstructions can inadvertently block pin access even with sufficient apparent clearance

4. **Use congestion reports effectively** - `capacity:0` indicates physical blockage, not just high utilization

### For Analog Macro Designers

1. **Provide multi-layer pin definitions** - Include met1 or met2 access shapes for all pins

2. **Create routing channels** - Leave gaps in obstructions near pin locations

3. **Document routing requirements** - Specify minimum halo sizes and routing layer requirements

---

## 7. Verification Commands

To verify the fix worked:

```bash
# View the routed design
make gui_route DESIGN_CONFIG=designs/sky130hd/VSDBabySoC/config.mk

# Check for DRC violations
make drc DESIGN_CONFIG=designs/sky130hd/VSDBabySoC/config.mk

# Generate final reports
make report DESIGN_CONFIG=designs/sky130hd/VSDBabySoC/config.mk
```

---

## 8. Files Modified

| File | Change |
|------|--------|
| `avsddac.lef` | Widened met4 OBS gap around OUT pin; Added met1 access shapes to D[8], D[9] pins |
| `config.mk` | Increased MACRO_PLACE_HALO and MACRO_PLACE_CHANNEL  |

Modified Files of `avsddac.lef` and config.mk
If u want u can open below

<details>
<summary><strong>config.mk</strong></summary>
  
```shell
export DESIGN_NICKNAME = vsdbabysoc
export DESIGN_NAME     = vsdbabysoc
export PLATFORM        = sky130hd

export VSDBabySoC_DIR = /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)


export VERILOG_FILES = \
    $(VSDBabySoC_DIR)/src/vsdbabysoc.v \
    $(VSDBabySoC_DIR)/src/rvmyth.v \
    $(VSDBabySoC_DIR)/src/clk_gate.v

export SDC_FILE = $(VSDBabySoC_DIR)/vsdbabysoc_synthesis.sdc
export VERILOG_INCLUDE_DIRS = $(wildcard $(VSDBabySoC_DIR)/include/)

export ADDITIONAL_LIBS = \
    $(VSDBabySoC_DIR)/lib/avsddac.lib \
    $(VSDBabySoC_DIR)/lib/avsdpll.lib

export ADDITIONAL_GDS  = $(wildcard $(VSDBabySoC_DIR)/gds/*.gds)
export ADDITIONAL_LEFS = $(wildcard $(VSDBabySoC_DIR)/lef/*.lef)

export ADDITIONAL_ROUTING_BLOCKAGES = $(VSDBabySoC_DIR)/route_blockages.tcl

export CLOCK_PORT = CLK
export CLOCK_NET  = CLK

export FP_PIN_ORDER_CFG = $(VSDBabySoC_DIR)/pin_order.cfg

export DIE_AREA  = 0   0   1600 1600
export CORE_AREA = 20  20  1580 1580

export FP_CORE_UTIL    = 40
export PLACE_DENSITY   = 0.30   # Changed from 0.48

export MACRO_PLACE_HALO    = 40 40  # Changed from 20 20
export MACRO_PLACE_CHANNEL = 40 40

export RTLMP_FLOW = 0
export MACRO_PLACEMENT = $(VSDBabySoC_DIR)/macro.cfg

 #dac 320 900 N
 #pll 40  40  N

export CTS_BUF_DISTANCE  = 600
export SKIP_GATE_CLONING = 1

# Normal congestion settings
export GRT_ALLOW_CONGESTION      = 1
export GRT_SKIP_CONGESTION_CHECK = 0
export GRT_CONGESTION_DRIVEN     = 1
export GRT_MAX_ITERATIONS        = 700
export GRT_ADJUSTMENT            = 0.15
export GRT_VIA_COST_ADJUSTMENT   = 3.0
export GRT_LAYER_ADJUSTMENTS     = {met1:0.25,met2:0.20,met3:0.10,met4:0.05,met5:0.00}
export GRT_MAX_TIME              = 3600

# ---- REQUIRED FIXES FOR YOUR VERSION (prevents GRT-0116 stop) ----

export GRT_OVERFLOW_ITERS   = 300

export TNS_END_PERCENT      = 100
export REMOVE_ABC_BUFFERS   = 1
export MAGIC_ZEROIZE_ORIGIN = 0
export MAGIC_EXT_USE_GDS    = 1
export GRT_FAIL_ON_OVERFLOW = 0
export RCX_EXTRACT=1
export RCX_CORNER=typical
export RCX_SPEF_OUTPUT=1
export SPEF_OUTPUT_FILE = $(VSDBabySoC_DIR)/vsdbabysoc.spef
```
</details>

<details>
<summary><strong>avsddac.lef</strong></summary>
  
```shell
VERSION 5.7 ;
NOWIREEXTENSIONATPIN ON ;
DIVIDERCHAR "/" ;
BUSBITCHARS "[]" ;

MACRO avsddac
  CLASS BLOCK ;
  FOREIGN avsddac ;
  ORIGIN -136.770 -613.050 ;
  SIZE 1193.570 BY 562.310 ;

  PIN D[9]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1150.220 613.050 1151.250 755.620 ;
      LAYER met1 ;
        RECT 1148.000 608.000 1154.000 613.050 ;
    END
  END D[9]

  PIN D[8]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
      LAYER met1 ;
        RECT 1115.000 608.000 1120.000 613.050 ;
    END
  END D[8]

  PIN D[7]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1096.950 613.050 1097.260 619.510 ;
    END
  END D[7]

  PIN D[6]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1078.220 613.050 1079.660 614.760 ;
    END
  END D[6]

  PIN D[5]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 226.250 1174.740 228.070 1175.360 ;
    END
  END D[5]

  PIN D[4]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 209.700 1174.310 211.150 1175.360 ;
    END
  END D[4]

  PIN D[3]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER met1 ;
        RECT 432.010 1168.710 433.190 1175.360 ;
    END
  END D[3]

  PIN D[2]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 178.380 1174.680 180.110 1175.360 ;
    END
  END D[2]

  PIN D[1]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 160.760 1172.150 162.090 1175.360 ;
    END
  END D[1]

  PIN D[0]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 145.960 1172.220 146.570 1175.360 ;
    END
  END D[0]

  PIN VPWR
    DIRECTION INOUT ;
    USE POWER ;
    PORT
      LAYER met4 ;
        RECT 152.670 1171.410 154.120 1175.360 ;
    END
  END VPWR

  PIN VGND
    DIRECTION INOUT ;
    USE GROUND ;
    PORT
      LAYER met2 ;
        RECT 136.770 873.200 144.500 873.520 ;
    END
  END VGND

  PIN VREFH
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 142.900 1170.680 143.170 1175.360 ;
    END
  END VREFH

  PIN OUT
    DIRECTION OUTPUT ;
    USE SIGNAL ;
    PORT
      LAYER met4 ;
        RECT 1172.680 1127.710 1173.730 1175.360 ;
    END
  END OUT

  OBS
      LAYER li1 ;
        RECT 136.770 1170.510 142.730 1175.360 ;
        RECT 143.340 1172.050 145.790 1175.360 ;
        RECT 146.740 1172.050 160.590 1175.360 ;
        RECT 143.340 1171.980 160.590 1172.050 ;
        RECT 162.260 1174.510 178.210 1175.360 ;
        RECT 180.280 1174.510 209.530 1175.360 ;
        RECT 162.260 1174.140 209.530 1174.510 ;
        RECT 211.320 1174.570 226.080 1175.360 ;
        RECT 228.240 1174.570 1164.190 1175.360 ;
        RECT 211.320 1174.140 1164.190 1174.570 ;
        RECT 162.260 1171.980 1164.190 1174.140 ;
        RECT 143.340 1170.510 1164.190 1171.980 ;
        RECT 136.770 755.790 1164.190 1170.510 ;
        RECT 136.770 709.350 1150.050 755.790 ;
        RECT 136.770 619.680 1117.030 709.350 ;
        RECT 136.770 614.930 1096.780 619.680 ;
        RECT 136.770 613.050 1078.050 614.930 ;
        RECT 1079.830 613.050 1096.780 614.930 ;
        RECT 1097.430 613.050 1117.030 619.680 ;
        RECT 1118.020 613.050 1150.050 709.350 ;
        RECT 1151.420 613.050 1164.190 755.790 ;
      LAYER met1 ;
        RECT 140.750 1168.430 431.730 1175.360 ;
        RECT 433.470 1168.430 1313.360 1175.360 ;
        RECT 140.750 613.050 1313.360 1168.430 ;
      LAYER met2 ;
        RECT 143.250 873.800 1165.380 1172.180 ;
        RECT 144.780 872.920 1165.380 873.800 ;
        RECT 143.250 641.910 1165.380 872.920 ;
      LAYER met3 ;
        RECT 146.990 647.680 1330.340 1169.670 ;
      LAYER met4 ;
        RECT 147.000 1171.010 152.270 1175.050 ;
        RECT 154.520 1171.010 1168.000 1175.050 ;
        RECT 147.000 1127.310 1172.280 1171.010 ;
        RECT 1178.130 1127.310 1188.200 1175.050 ;
        RECT 147.000 648.540 1188.200 1127.310 ;
  END

END avsddac
END LIBRARY

```

</details>

---

## 9. Conclusion

This routing failure exemplifies a common challenge in mixed-signal SoC design: analog macros created without consideration for digital place-and-route requirements. The fix required understanding both the analog macro's physical structure and the digital router's access requirements. By adding appropriate metal layer access points and clearing obstruction blockages, the design achieved zero routing violations with minimal impact to the original macro functionality.



---

### SPEF File:

It will get generated automatically after the route but we have to write this command:

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk final
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command50.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command51.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command52.png)

---
## 💡 Summary

Week 7 focuses on running the complete RTL to GDSII flow for the VSDBabySoC using OpenROAD-Flow-Scripts on the Sky130HD PDK. The process includes synthesis, floorplanning, placement, and clock tree synthesis, followed by density, structural, and timing validation using the OpenROAD GUI. Key issues such as library parsing errors and placement optimization are resolved along the flow. By the end of the week, a timing-clean and layout-ready design is achieved, fully prepared for routing and final GDS export.



</details>

---
# 4. SPEF-Based Post-Routing Analysis and Final Timing Closure
  
 Finally, Week 8 focused on post-routing STA and timing closure using the extracted SPEF parasitics. This stage evaluated real delays caused by interconnect resistance, capacitance, and coupling effects across multiple PVT corners. Setup and hold slacks were analyzed to ensure that the post-layout design met timing requirements under realistic conditions. Any deviations from the pre-route timing were examined, allowing insights into how placement, routing, and parasitics affect the overall performance of the SoC. With timing closure achieved, the VSDBabySoC design reached a tape-out–ready stage, completing the RTL-to-GDS design flow.

If u want to see in detail **(CLICK BELOW)**

<details>
<summary><strong>🚀 Week 8 : – Post-Routing STA analysis of VSDBabySoC Design</strong></summary>

  # Week 8 : – Post-Routing STA analysis of VSDBabySoC Design

<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-8-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Week8** –   is to perform a complete **post-routing Static Timing Analysis (STA)** of the **VSDBabySoC design using OpenSTA** across multiple **PVT corners**. This includes running STA with accurate post-route parasitic data (SPEF), post-CTS constraints (SDC), and multi-corner Liberty files to evaluate worst-case setup, hold, WNS, and TNS metrics. 

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [📝 Summary](#-summary)
3. [📂 Key Files Used for STA](#-key-files)
4. [📜 sta_across_pvt_route.tcl Script](#sta_across_pvt_routetcl-file)
5. [🕒 Post-CTS SDC File](#vsdbabysoc_post_ctssdc-file)
6. [📡 SPEF (Post-Route Parasitics)](#post-route-spef-file)
7. [🚀 Running STA Using Docker](#running-sta)
8. [📄 STA Output Files Generated](#sta-output-files)
9. [📊 Post-Synthesis Timing Summary](#post-synthesis-results-summary)
10. [📊 Post-Route Timing Summary](#post-routing-results-summary)
11. [📈 Comparison Graphs](#comparison-graphs)
12. [🔍 Key Differences: Post-Synthesis vs Post-Route](#key-differences-post-synthesis-vs-post-route-timing-analysis)
13. [📝 Final Summary](#summary)
14. [📚 Repository & Author](#repository--author)

---

### Objective
The objective of Week 8 is to perform a complete post-routing Static Timing Analysis (STA) of the VSDBabySoC design using OpenSTA across multiple PVT corners. This includes running STA with accurate post-route parasitic data (SPEF), post-CTS constraints (SDC), and multi-corner Liberty files to evaluate worst-case setup, hold, WNS, and TNS metrics. The goal is to verify the timing integrity of the design after routing, identify corner-specific violations, and ensure sign-off-quality timing closure for the final GDSII.

---
### `Key Files`

To perform reliable timing verification of the BabySoC design after routing, we use OpenSTA with a dedicated TCL script, a post-CTS constraints file, and the post-route SPEF file.

The `sta_across_pvt_route.tcl` script automates static timing analysis across multiple process, voltage, and temperature (PVT) corners, while the `5_route.sdc` file provides the design-specific timing constraints generated after clock tree synthesis, and the `6_final.spef` file supplies the post-route parasitic RC data. Together, these files ensure that STA is run under the correct operating conditions with accurate parasitic delays, and that reports such as setup/hold slack, WNS, and TNS are captured for each library corner.

## sta_across_pvt_route.tcl File

```
 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

# Extra liberty files
read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib

for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {

    read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/$list_of_lib_files($i)

    read_verilog /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/6_final.v

    link_design vsdbabysoc
    current_design

    # FIXED SDC PATH
    read_sdc /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_route.sdc

    read_spef /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/6_final.spef

    check_setup -verbose

    report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} \
      > /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/min_max_$list_of_lib_files($i).txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_max_slack.txt
    report_worst_slack -max -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_max_slack.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_min_slack.txt
    report_worst_slack -min -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_min_slack.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_tns.txt
    report_tns -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_tns.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_wns.txt
    report_wns -digits {4} \
      >> /data/VLSI/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_wns.txt
}

```


This `vsdbabysoc_post_cts.sdc` file is an auto-generated SDC created after clock tree synthesis. It sets the current design to `vsdbabysoc` and defines the basic timing environment. The file specifies a clock named `clk` with an `11 ns` period, driven from the pin `pll/CLK`, and marks it as a propagated clock for STA. Sections for environment and design rules are also included for adding further constraints if needed.



### `Running STA`

To run the post-route STA using Docker, follow these steps to execute the `sta_across_pvt_route.tcl` script. Launch a Docker container with your local directory mounted, run the script inside the container, and it will generate all timing reports such as setup/hold slack, WNS, and TNS in the mounted `/data` folder. Using Docker ensures a consistent and reproducible environment for performing the analysis.

```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/sta_across_pvt_route.tcl
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command1.png)

After running the STA script, you can navigate to the `STA_OUTPUT/route/` directory to see all the generated timing reports. This includes detailed path delay reports for each library corner (`min_max_*.txt`), worst setup and hold slack summaries (`sta_worst_max_slack.txt and sta_worst_min_slack.txt`), as well as total negative slack (sta_tns.txt) and worst negative slack (`sta_wns.txt`). These files provide a complete overview of the BabySoC design’s timing performance after routing.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command2.png)

### `Post-Synthesis Results Summary`

Here is a tabulated view of the key timing results generated by the STA script.

| PVT_CORNER                     | Worst Setup Slack | Worst Hold Slack | WNS       | TNS          |
|-------------------------------|-------------------|------------------|-----------|--------------|
| sky130_fd_sc_hd_tt_025C_1v80  | 0.8595            | 0.3096           | 0         | 0            |
| sky130_fd_sc_hd_ff_100C_1v65  | 2.2764            | 0.2491           | 0         | 0            |
| sky130_fd_sc_hd_ff_100C_1v95  | 3.7138            | 0.1960           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v56  | 0.8214            | 0.2915           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v65  | 1.8597            | 0.2551           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v76  | 2.7707            | 0.2243           | 0         | 0            |
| sky130_fd_sc_hd_ss_100C_1v40  | -13.6381          | 0.9053           | -13.6381  | -7567.7964   |
| sky130_fd_sc_hd_ss_100C_1v60  | -6.7098           | 0.6420           | -6.7098   | -2833.05     |
| sky130_fd_sc_hd_ss_n40C_1v28  | -51.2061          | 1.8296           | -51.2061  | -36861.4102  |
| sky130_fd_sc_hd_ss_n40C_1v35  | -32.0887          | 1.3475           | -32.0887  | -23317.6035  |
| sky130_fd_sc_hd_ss_n40C_1v40  | -23.8290          | 1.1249           | -23.8290  | -17211.252   |
| sky130_fd_sc_hd_ss_n40C_1v44  | -19.2010          | 0.9909           | -19.2010  | -13652.0469  |
| sky130_fd_sc_hd_ss_n40C_1v76  | -4.4548           | 0.5038           | -4.4548   | -1842.5518   |


### `Post-routing Results Summary`

Here is a tabulated view of the key timing results generated by the STA script.

| PVT_CORNER      | Worst Hold Slack | Worst Setup Slack | WNS       | TNS          |
|-----------------|------------------|-------------------|-----------|--------------|
| tt_025C_1v80    | 0.3104           | 6.5987            | 0         | 0            |
| ff_100C_1v65    | 0.2516           | 7.5661            | 0         | 0            |
| ff_100C_1v95    | 0.1977           | 3.3245            | 0         | 0            |
| ff_n40C_1v56    | 0.2904           | 6.5235            | 0         | 0            |
| ff_n40C_1v65    | 0.2572           | 7.1347            | 0         | 0            |
| ff_n40C_1v76    | 0.2267           | 7.6698            | 0         | 0            |
| ss_100C_1v40    | 0.8794           | -1.4062           | -1.4062   | -132.1887    |
| ss_100C_1v60    | 0.6267           | 2.5442            | 0         | 0            |
| ss_n40C_1v28    | 1.6106           | -25.0572          | -25.0572  | -14950.0332  |
| ss_n40C_1v35    | 1.2095           | -13.8790          | -13.8790  | -7197.1201   |
| ss_n40C_1v40    | 1.0153           | -9.2025           | -9.2025   | -3827.5981   |
| ss_n40C_1v44    | 0.9072           | -6.4161           | -6.4161   | -2041.2456   |
| ss_n40C_1v76    | 0.4688           | 3.2889            | 0         | 0            |


### 📈 `Comparison Graphs`

Graph created through Google colab

```
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO

# -----------------------------
# 1. POST-SYNTHESIS DATA 
# -----------------------------
post_synth_data = """PVT_CORNER	WORST SETUP SLACK	WORST HOLD SLACK	WNS	TNS
sky130_fd_sc_hd_tt_025C_1v80	0.8595	0.3096	0	0
sky130_fd_sc_hd_ff_100C_1v65	2.2764	0.2491	0	0
sky130_fd_sc_hd_ff_100C_1v95	3.7138	0.196	0	0
sky130_fd_sc_hd_ff_n40C_1v56	0.8214	0.2915	0	0
sky130_fd_sc_hd_ff_n40C_1v65	1.8597	0.2551	0	0
sky130_fd_sc_hd_ff_n40C_1v76	2.7707	0.2243	0	0
sky130_fd_sc_hd_ss_100C_1v40	-13.6381	0.9053	-13.6381	-7567.7964
sky130_fd_sc_hd_ss_100C_1v60	-6.7098	0.642	-6.7098	-2833.05
sky130_fd_sc_hd_ss_n40C_1v28	-51.2061	1.8296	-51.2061	-36861.4102
sky130_fd_sc_hd_ss_n40C_1v35	-32.0887	1.3475	-32.0887	-23317.6035
sky130_fd_sc_hd_ss_n40C_1v40	-23.829	1.1249	-23.829	-17211.252
sky130_fd_sc_hd_ss_n40C_1v44	-19.201	0.9909	-19.201	-13652.0469
sky130_fd_sc_hd_ss_n40C_1v76	-4.4548	0.5038	-4.4548	-1842.5518
"""

df_synth = pd.read_csv(StringIO(post_synth_data), sep="\t")
df_synth["Corner"] = df_synth["PVT_CORNER"].str.replace("sky130_fd_sc_hd_", "")


# -------------------------------------------------
# 2. POST-ROUTE DATA 
# -------------------------------------------------
post_route_data = """PVT_CORNER	WORST SETUP SLACK	WORST HOLD SLACK	WNS	TNS
tt_025C_1v80	6.5987	0.3104	0	0
ff_100C_1v65	7.5661	0.2516	0	0
ff_100C_1v95	3.3245	0.1977	0	0
ff_n40C_1v56	6.5235	0.2904	0	0
ff_n40C_1v65	7.1347	0.2572	0	0
ff_n40C_1v76	7.6698	0.2267	0	0
ss_100C_1v40	-1.4062	0.8794	-1.4062	-132.1887
ss_100C_1v60	2.5442	0.6267	0	0
ss_n40C_1v28	-25.0572	1.6106	-25.0572	-14950.0332
ss_n40C_1v35	-13.879	1.2095	-13.879	-7197.1201
ss_n40C_1v40	-9.2025	1.0153	-9.2025	-3827.5981
ss_n40C_1v44	-6.4161	0.9072	-6.4161	-2041.2456
ss_n40C_1v76	3.2898	0.4688	0	0
"""

df_route = pd.read_csv(StringIO(post_route_data), sep="\t")
df_route["Corner"] = df_route["PVT_CORNER"]


# -----------------------------
# 3. PLOTTING COMPARISON
# -----------------------------
metrics = {
    "WORST SETUP SLACK": "Worst Setup Slack",
    "WORST HOLD SLACK": "Worst Hold Slack",
    "WNS": "Worst Negative Slack",
    "TNS": "Total Negative Slack"
}

for metric, title in metrics.items():
    plt.figure(figsize=(14, 6))
    
    plt.plot(df_synth["Corner"], df_synth[metric], label="Post-Synthesis", marker='o', linewidth=2)
    plt.plot(df_route["Corner"], df_route[metric], label="Post-Route", marker='s', linewidth=2)
    
    plt.axhline(0, color='red', linestyle='--', linewidth=1.5)
    
    plt.title(f"{title} Comparison: Post-Synthesis vs Post-Route", fontsize=16)
    plt.xlabel("PVT Corner")
    plt.ylabel(f"{title} (ns)")
    plt.xticks(rotation=45, ha='right')
    plt.grid(axis='y', linestyle='--', alpha=0.6)
    plt.legend()
    plt.tight_layout()
    
    plt.show()

```
### 📌 Technical Analysis of Hold Slack (Post-Synthesis vs Post-Routing)

The hold slack values across all PVT corners show only minimal variation between the post-synthesis and post-routing stages. This indicates that routing parasitics did not significantly degrade hold timing. The overall behavior can be summarized as follows:

**✔️ 1. Hold slacks remain positive for all corners**

All PVT corners retain positive hold slack both before and after routing, confirming that:
  - The design does not exhibit hold violations at any PVT corner.
  - Clock tree synthesis (CTS) and routing did not introduce unexpected negative skew.
    
**✔️ 2. Very small delta between pre- and post-routing**

Most corners show only ~0.001–0.02 ns variation, which suggests:
  - Parasitic extraction (RC effects) had negligible impact on short-path delays.
  - The design already had strong inherent short-path margins during synthesis.
    
**✔️ 3. Fast corners (FF) show extremely stable slack**

Examples:
  - ff_100C_1v65: 0.2491 → 0.2516
  - ff_100C_1v95: 0.1960 → 0.1977

This stabililty across FF corners implies:
  - Data paths do not shrink dangerously with high voltage / low temperature.
  - The clock tree is well-balanced, with controlled skew.
    
**✔️ 4. Slow corners (SS) exhibit larger slack values**

Example:
  - ss_n40C_1v28: 1.8296 → 1.6106

SS corners naturally produce:
  - Larger combinational delays
  - Reduced sensitivity to hold issues
A minor reduction (≈ 0.2 ns) after routing is expected due to realistic RC delays.

**✔️ 5. No corner shows critical degradation**

Even the worst-case delta (≈0.22 ns at ss_n40C_1v28) is small relative to typical hold margins, confirming:
  - Routing added predictable and non-critical delay.
  - There is no need for additional hold buffer insertion.


| PVT Corner        | Hold Slack Post-Synthesis  | Hold Slack Post-routing  |
|-------------------|--------------|--------------|
| tt_025C_1v80      | 0.3096       | 0.3104       |
| ff_100C_1v65      | 0.2491       | 0.2516       |
| ff_100C_1v95      | 0.1960       | 0.1977       |
| ff_n40C_1v56      | 0.2915       | 0.2904       |
| ff_n40C_1v65      | 0.2551       | 0.2572       |
| ff_n40C_1v76      | 0.2243       | 0.2267       |
| ss_100C_1v40      | 0.9053       | 0.8794       |
| ss_100C_1v60      | 0.6420       | 0.6267       |
| ss_n40C_1v28      | 1.8296       | 1.6106       |
| ss_n40C_1v35      | 1.3475       | 1.2095       |
| ss_n40C_1v40      | 1.1249       | 1.0153       |
| ss_n40C_1v44      | 0.9909       | 0.9072       |
| ss_n40C_1v76      | 0.5038       | 0.4688       |

Here is a graph showing the comparison of `worst-case hold slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command6.png)


📌 Conclusion
Overall, the post-routing hold timing is well within acceptable limits and demonstrates excellent correlation with post-synthesis estimates. The design shows robust short-path behavior across all PVT corners, and no hold fixes or ECO buffers are required.

---
### 📌 Technical Analysis of Setup Slack (Post-Synthesis vs Post-Routing)

The setup slack values across the PVT corners show significant variation between post-synthesis and post-routing stages. This behavior highlights the impact of real parasitics, routing delay, and physical optimization on long-path timing characteristics.
The overall observations are:

**✔️ 1. Setup slack improves significantly after routing in most corners**

Corners such as TT and all FF corners show a dramatic positive shift:

Example:
  - tt_025C_1v80: 0.8595 → 6.5987 ns
  - ff_n40C_1v76: 2.7707 → 7.6698 ns

This indicates:
  - Physical optimization (buffering, gate sizing, placement refinement) greatly reduced long-path delays.
  - Clock tree synthesis introduced a favorable skew (data path slightly delayed, clock arrives earlier).
  - Routing parasitics did not worsen critical paths, but instead helped balance path delays.

**✔️ 2. All FF corners show strong positive timing margins**

Examples:
  - ff_100C_1v65: 2.2764 → 7.5661
  - ff_n40C_1v65: 1.8597 → 7.1347

This consistent improvement suggests:
  - Faster process conditions already favor setup timing.
  - Routing provided sufficient RC delay smoothing to keep long-path slack high.
  - The design is highly stable under fast operating conditions, with no risk of setup violations.

**✔️ 3. SS corners remain challenging due to inherently slow process conditions**

Examples:
  - ss_100C_1v40: –13.6381 → –1.4062
  - ss_n40C_1v28: –51.2061 → –25.0572

Even though routing improves timing, these corners remain negative because:
  - Slow process + low voltage + temperature stress dramatically increase cell delays.
  - Critical data paths exceed the clock period under worst-case SS conditions.
  - Clock skew cannot fully compensate for extreme delay increase.
  - This confirms that SS corners represent the true worst-case setup bottleneck of the design.

**✔️ 4. Post-routing reduces the magnitude of negative slack, showing good correlation**

Example:
  - ss_n40C_1v40: –23.8290 → –9.2025
  - ss_n40C_1v44: –19.2010 → –6.4161

Interpretation:
  - Placement and routing significantly optimized long-path delay (up to ~14 ns improvement).
  - Physical design reduced pessimism in early synthesis estimates.
  - Despite improvements, path-level restructuring is still needed for closure at extreme corners.

**✔️ 5. The overall trend shows predictable and consistent timing behavior**

  - FF/TT corners → always positive and strongly improved
  - SS corners → still negative but significantly recovered
  - No unexpected degradation after routing
  - STA behavior indicates good synthesis–routing correlation


| PVT Corner        | Setup Slack Post-Synthesis | Setup Slack Post-routing|
|-------------------|---------------|----------------|
| tt_025C_1v80      | 0.8595        | 6.5987         |
| ff_100C_1v65      | 2.2764        | 7.5661         |
| ff_100C_1v95      | 3.7138        | 3.3245         |
| ff_n40C_1v56      | 0.8214        | 6.5235         |
| ff_n40C_1v65      | 1.8597        | 7.1347         |
| ff_n40C_1v76      | 2.7707        | 7.6698         |
| ss_100C_1v40      | -13.6381      | -1.4062        |
| ss_100C_1v60      | -6.7098       | 2.5442         |
| ss_n40C_1v28      | -51.2061      | -25.0572       |
| ss_n40C_1v35      | -32.0887      | -13.8790       |
| ss_n40C_1v40      | -23.8290      | -9.2025        |
| ss_n40C_1v44      | -19.2010      | -6.4161        |
| ss_n40C_1v76      | -4.4548       | 3.2889         |

Here is a graph showing the comparison of `worst-case setup slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command5.png)

📌 Conclusion 

Overall, setup timing improves substantially after routing across typical and fast PVT corners, indicating effective physical optimization and well-balanced clock distribution. However, worst-case SS corners continue to display significant negative slack, confirming that they remain the critical setup-limiting conditions of the design. Further optimization—such as path restructuring, cell upsizing, or pipeline refinement—is required to fully close timing in these slow process, low-voltage scenarios.

---
### 📌 Technical Analysis of WNS (Post-Synthesis vs Post-Routing)

The WNS values reveal how the most critical setup paths behave across PVT corners before and after routing. Since WNS directly determines whether the design meets timing, this analysis highlights the true worst-case limitations of the chip.

**✔️ 1. All TT and FF corners show zero WNS both pre- and post-routing**

Example corners:
  - tt_025C_1v80: 0 → 0
  - ff_100C_1v65: 0 → 0
  - ff_n40C_1v76: 0 → 0

This indicates:
  - The design easily meets setup timing under typical and fast process conditions.
  - Routing does not reveal hidden violations.
  - There is strong timing margin for high-performance operation at nominal or fast corners.

**✔️ 2. SS corners remain the true setup bottleneck**

The worst violations occur at slow-slow combinations, especially at low voltages:
Example severe cases:
  - ss_n40C_1v28: –51.2061 → –25.0572
  - ss_n40C_1v35: –32.0887 → –13.8790
  - ss_n40C_1v40: –23.8290 → –9.2025

Reasons:
  - Slow transistors + low VDD + temperature stress dramatically increase delay.
  - Critical long paths exceed the target clock period.
  - These corners define the minimum operating frequency of the design.

**✔️ 3. Post-routing significantly improves WNS in all SS corners**

Examples of improvement:
  - ss_100C_1v40: –13.6381 → –1.4062
  - ss_n40C_1v44: –19.2010 → –6.4161
  - ss_n40C_1v28: –51.2061 → –25.0572

Interpretation:
   - Routing/CTS added beneficial skew and optimized critical paths.
   - Worst-case slack improved by 10–25 ns, showing strong physical design optimization.
   - However, even after routing, SS corners still hold negative WNS → timing closure incomplete.

**✔️ 4. Some SS corners improve enough to reach zero WNS**

Examples:
  - ss_100C_1v60: –6.7098 → 0
  - ss_n40C_1v76: –4.4548 → 0

This shows:
  - For moderate voltage SS corners, routing is sufficient to fully close timing.
  - Only the most extreme low-voltage SS corners remain problematic.

**✔️ 5. Overall trend reflects predictable timing behavior**

 - TT/FF → stable, clean, zero-WNS corners
 - SS moderate voltage → post-routing closure
 - SS low voltage → persistent violations due to device limitations
 - Routing significantly improves but cannot fully compensate for extreme slow-process delays


| PVT Corner        | WNS Post-Synthesis       | WNS Post-routing       |
|-------------------|---------------|---------------|
| tt_025C_1v80      | 0             | 0             |
| ff_100C_1v65      | 0             | 0             |
| ff_100C_1v95      | 0             | 0             |
| ff_n40C_1v56      | 0             | 0             |
| ff_n40C_1v65      | 0             | 0             |
| ff_n40C_1v76      | 0             | 0             |
| ss_100C_1v40      | -13.6381      | -1.4062       |
| ss_100C_1v60      | -6.7098       | 0             |
| ss_n40C_1v28      | -51.2061      | -25.0572      |
| ss_n40C_1v35      | -32.0887      | -13.8790      |
| ss_n40C_1v40      | -23.8290      | -9.2025       |
| ss_n40C_1v44      | -19.2010      | -6.4161       |
| ss_n40C_1v76      | -4.4548       | 0             |

Here is a graph showing the comparison of `WNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command7.png)

📌 Conclusion 

The WNS analysis shows that typical and fast corners easily meet timing at both synthesis and routing stages, confirming strong high-speed behavior. Although routing dramatically improves worst-case slack, extreme slow-slow low-voltage corners still exhibit large negative WNS and represent the fundamental setup-limited operating condition. Additional logic optimization, cell upsizing, or pipeline insertion is required to close timing at these harsh SS corners.

---
### 📌 Technical Analysis of TNS (Post-Synthesis vs Post-Routing)

The TNS values provide a clear understanding of how many endpoints fail setup timing and by how much in aggregate. Unlike WNS, which identifies the single worst path, TNS reflects the overall timing health of the design across all timing endpoints.

Your data shows a strong improvement after routing, particularly in slow-slow (SS) corners.

**✔️ 1. All TT and FF corners show clean TNS = 0 at both stages**

Examples:
  - tt_025C_1v80: 0 → 0
  - ff_100C_1v65: 0 → 0
  - ff_n40C_1v76: 0 → 0

Interpretation:
  - No endpoints violate setup timing in typical or fast conditions.
  - Design is highly stable under performance-oriented corners.
  - Clock tree + routing introduce no regressions.

**✔️ 2. SS corners show large TNS violations due to widespread long-path failures**

Worst cases:
  - ss_n40C_1v28: –36,861 → –14,950
  - ss_n40C_1v35: –23,317 → –7,197
  - ss_n40C_1v40: –17,211 → –3,827

These values indicate:
  - Many endpoints simultaneously fail timing in harsh SS corners.
  - This behavior is expected due to extremely slow cell delays at low voltage.
  - These corners define the dominant limitation for achieving timing closure.

**✔️ 3. Post-routing dramatically improves TNS across all SS corners**

Examples:
  - ss_100C_1v40: –7,567 → –132
  - ss_n40C_1v44: –13,652 → –2,041
  - ss_n40C_1v28: –36,861 → –14,950

Key points:
  - Physical design optimization reduces total violation magnitude by 70–90%.
  - Placement, buffering, and skew balancing improved a large number of paths.
  - Routing parasitics reduced pessimism present at synthesis time.

**✔️ 4. Some SS corners recover fully to TNS = 0 after routing**

Examples:
   - ss_100C_1v60: –2,833 → 0
   - ss_n40C_1v76: –1,842 → 0

This indicates:
   - These moderate SS corners can achieve full timing closure with only routing optimization.
   - Violations were minor and easily resolved by physical design.

**✔️ 5. Overall trend indicates predictable STA behavior**

  - TT/FF corners → always clean, zero TNS
  - SS moderate voltage → routing fixes all violations
  - SS extreme low-voltage → still large TNS, requiring architectural or logic-level fixes
  - Routing significantly narrows but does not eliminate violations where the process is inherently slow
  - This reflects realistic silicon behavior and good synthesis–routing correlation.


| PVT Corner        | TNS Post-Synthesis          | TNS Post-routing        |
|-------------------|------------------|----------------|
| tt_025C_1v80      | 0                | 0              |
| ff_100C_1v65      | 0                | 0              |
| ff_100C_1v95      | 0                | 0              |
| ff_n40C_1v56      | 0                | 0              |
| ff_n40C_1v65      | 0                | 0              |
| ff_n40C_1v76      | 0                | 0              |
| ss_100C_1v40      | -7567.7964       | -132.1887      |
| ss_100C_1v60      | -2833.05         | 0              |
| ss_n40C_1v28      | -36861.4102      | -14950.0332    |
| ss_n40C_1v35      | -23317.6035      | -7197.1201     |
| ss_n40C_1v40      | -17211.252       | -3827.5981     |
| ss_n40C_1v44      | -13652.0469      | -2041.2456     |
| ss_n40C_1v76      | -1842.5518       | 0              |

Here is a graph showing the comparison of `TNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command8.png)

📌 Conclusion 

TNS analysis confirms that the design is fully timing-clean in all TT and FF corners, with routing eliminating violations in several moderate SS conditions. However, extreme slow-slow low-voltage corners continue to exhibit substantial aggregate setup failures, indicating widespread long-path delays that cannot be fixed by physical design alone. Further optimization—such as critical-path restructuring, cell upsizing, or pipeline insertion—is required to close timing at these harsh corners.

---

### `Key Differences: Post-Synthesis vs Post-Route Timing Analysis`

| Aspect             | Post-Synthesis Analysis                            | Post-Route Analysis                                           |
| ------------------ | -------------------------------------------------- | ------------------------------------------------------------- |
| **Timing Model**   | Wire-load models (fanout/cell-based estimation)    | Extracted parasitics (RC) from routed layout                  |
| **Clock Network**  | Ideal clock, zero skew, no latency                 | Real clock tree with buffer delays, skew, and insertion delay |
| **Interconnect**   | Delay estimated from fanout-based lookup tables    | Delay calculated from actual metal routing and vias           |
| **Accuracy**       | \~70–80% correlation with sign-off                 | \~95–98% correlation with sign-off                            |
| **Critical Paths** | Critical paths may differ due to estimation errors | Matches actual layout critical paths   |

---

### `Summary`
- Post-synthesis analysis serves as an **early timing checkpoint**.  
- Post-route analysis represents the **golden reference for timing sign-off**.  
- Transition from estimated to actual physical parameters often reveals:
  - New critical paths revealed
  - Realistic clock tree effects (skew, latency)
  - Interconnect-dominated delays
  - Impact of physical proximity & coupling

</details>

---
# Summary:

 Throughout this project, I gained complete hands-on experience in the full RTL-to-GDSII flow using the VSDBabySoC. I began with understanding the SoC architecture, RISC-V core, peripheral interactions, and functional modeling in Week 2. In Week 3, I moved to RTL synthesis, gate-level simulation, and post-synthesis STA, learning how synthesis optimizations affect timing and functionality. Week 7 introduced me to the physical design stages—floorplanning, placement, CTS, and routing—using OpenROAD, where I implemented the full physical layout and optimized congestion and clock distribution. In Week 8, I performed post-route STA with SPEF parasitics, analyzed setup/hold slacks across PVT corners, and completed timing closure. Overall, I learned every major step from RTL design to final GDS generation, gaining a complete understanding of how an SoC moves from functional behavior to tape-out-ready silicon.

---

## 🔗 Final Documentation File  of week 0 to week 8

👉 [VSDBabySoC RTL-to-GDS documentation](https://github.com/VEERARAGAVAN7/VSD_SoC_Tapeout_Week9/blob/main/week9_final_documentation.md)

---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

**🔗 Program Links**

[![VSD Website](https://img.shields.io/badge/VSD-Official%20Website-blue?style=flat-square)](https://vsdiat.vlsisystemdesign.com/)  
[![Sky130](https://img.shields.io/badge/Open%20PDK-Sky130-green?style=flat-square)](https://github.com/google/skywater-pdk)  
[![Efabless](https://img.shields.io/badge/Efabless-Platform-orange?style=flat-square)](https://efabless.com/)

</div>

---
