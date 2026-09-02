# IoT-Based Smart Street Lighting and Energy Optimization System
**Course:** ECA4714 – Principles of Digital System Design  
**Assignment Title:** Design, implement/simulate, secure, verify, and evaluate an IoT-Based Smart Street Lighting and Energy Optimization System using sensors, a microcontroller, and Verilog HDL.

---

## 1. Project Overview & Architectural Summary
This repository contains the complete, original, and rigorously verified engineering design for an **IoT-Based Smart Street Lighting and Energy Optimization System**. The architecture integrates:
- **Ambient Light & Motion Sensing**: LDR twilight sensor with Schmitt-trigger hysteresis and PIR motion sensor with low-latency hardware interrupt capture.
- **Embedded Supervisory Microcontroller**: Arduino-compatible supervisory firmware for sensor sampling, signal conditioning, Verilog bus interfacing, and non-blocking JSON telemetry reporting.
- **Verilog HDL Digital Subsystem**: Synthesizable, hardware-accelerated RTL core featuring:
  - **Combinational Sensor Logic (`sensor_logic.v`)**: K-map minimized Boolean equations implementing daylight-priority logic ($O = D$, $R = \overline{D} \cdot \overline{M}$, $L = \overline{D} \cdot M$).
  - **5-State Synchronous Moore FSM (`lighting_fsm.v`)**: S0 (SAFE), S1 (DAY), S2 (NIGHT_IDLE), S3 (MOTION_ACTIVE), and S4 (FAULT_SAFE).
  - **8-Bit PWM Generator (`pwm_generator.v`)**: Free-running counter and digital comparator generating calibrated duty cycles (0%, 30.2%, 100%, and 9.8% fail-safe beacon).
  - **Fault-Safe Supervisory Unit (`fault_safe.v`)**: Real-time anomaly detection and emergency override logic.
  - **Top-Level Structural Integration (`smart_street_light_top.v`)**: Unites all sub-modules and outputs an 8-bit packed IoT telemetry byte.
- **Self-Checking Testbench (`smart_street_light_tb.v`)**: Rigorous verification suite executing 13 test cases (TC01 to TC13) with automated assertion checking and VCD waveform dumping.

---

## 2. Directory Structure

```text
smart_street_light/
│
├── rtl/                                  # Synthesizable Verilog HDL Core
│   ├── sensor_logic.v                    # Combinational Boolean decision logic
│   ├── fault_safe.v                      # Fault supervisory and override unit
│   ├── lighting_fsm.v                    # 5-State synchronous Moore FSM
│   ├── pwm_generator.v                   # 8-Bit digital PWM counter & comparator
│   └── smart_street_light_top.v          # Top-level structural integration
│
├── tb/                                   # Formal Verification Suite
│   └── smart_street_light_tb.v           # 13-Testcase self-checking testbench
│
├── simulation/                           # Simulation Artifacts & Execution Logs
│   ├── smart_street_light_sim.vvp        # Compiled Icarus Verilog simulation binary
│   ├── simulation_log.txt                # Console PASS/FAIL execution log
│   └── waveform.vcd                      # Value Change Dump waveform data
│
├── microcontroller/                      # Embedded Firmware
│   └── smart_street_light_mcu.ino        # Arduino C++ supervisory firmware
│
├── diagrams/                             # Academic Technical Diagrams (PNG)
│   ├── generate_diagrams.py              # Script generating all vector diagrams
│   ├── figure_1_system_architecture.png  # Figure 1: Overall System Architecture
│   ├── figure_2_hardware_interface.png   # Figure 2: Hardware / Sensor Interface
│   ├── figure_3_boolean_gate_logic.png   # Figure 3: Gate-Level Logic Schematic
│   ├── figure_4_karnaugh_maps.png        # Figure 4: Karnaugh Map Minimization
│   ├── figure_5_fsm_state_diagram.png    # Figure 5: FSM State Transition Diagram
│   ├── figure_6_pwm_generator_block.png  # Figure 6: PWM Generator Architecture
│   ├── figure_7_microcontroller_architecture.png # Figure 7: MCU Interconnect
│   ├── figure_8_microcontroller_flowchart.png    # Figure 8: Firmware Flowchart
│   ├── figure_9_iot_monitoring_architecture.png  # Figure 9: IoT Telemetry Architecture
│   ├── figure_10_verification_testbench.png      # Figure 10: Testbench Framework
│   └── figure_11_timing_diagram.png      # Figure 11: System Timing Diagram
│
├── screenshots/                          # Authentic Simulation Waveform Plots
│   ├── figure_12_simulation_waveform_tc01_tc10.png # Figure 12: Actual Simulation Waveform
│   └── figure_13_pwm_modes_simulation.png          # Figure 13: Actual PWM Output Cycles
│
├── report/                               # Comprehensive Academic Report
│   ├── build_full_report.py              # Automated DOCX and PDF report builder
│   ├── ECA4714_Smart_Street_Lighting_Final_Report.docx # Formatted Word Report (25+ pages)
│   └── ECA4714_Smart_Street_Lighting_Final_Report.pdf  # Formatted PDF Report
│
├── source_archive/                       # Complete Compressed Project Archive
│   └── smart_street_light_archive.zip    # Full portable archive
│
└── README.md                             # This documentation
```

---

## 3. Step-by-Step Compilation & Simulation Instructions

### Prerequisites
- **Icarus Verilog (`iverilog`, `vvp`)** and **GTKWave** (or use the portable Icarus Verilog in `tools/iverilog/bin/`).
- **Python 3.8+** with `matplotlib`, `python-docx`, and `docx2pdf` (or `reportlab`).

### Step 1: Compile the Verilog RTL and Testbench
Run the following command in terminal:
```bash
iverilog -o smart_street_light/simulation/smart_street_light_sim.vvp \
    smart_street_light/rtl/sensor_logic.v \
    smart_street_light/rtl/fault_safe.v \
    smart_street_light/rtl/lighting_fsm.v \
    smart_street_light/rtl/pwm_generator.v \
    smart_street_light/rtl/smart_street_light_top.v \
    smart_street_light/tb/smart_street_light_tb.v
```

### Step 2: Execute the Simulation Testbench
```bash
vvp smart_street_light/simulation/smart_street_light_sim.vvp
```

### Step 3: View the Authentic Simulation Waveforms
To open the generated VCD waveform in GTKWave:
```bash
gtkwave smart_street_light/simulation/waveform.vcd
```

---

## 4. Verification Results Summary

```text
================================================================================
   ECA4714: SMART STREET LIGHTING & ENERGY OPTIMIZATION - TESTBENCH EXECUTION   
================================================================================
Time (ns) | Test ID | Description                     | State | Mode | Fault | Result
--------------------------------------------------------------------------------
    40000 | TC01    | Master System Reset (R=1)        |   000   |  11  |   0   | PASS
   100000 | TC02    | Daylight + No Motion (D=1, M=0)  |   001   |  00  |   0   | PASS
   140000 | TC03    | Daylight + Motion (D=1, M=1)     |   001   |  00  |   0   | PASS
   180000 | TC04    | Darkness + No Motion (D=0, M=0)  |   010   |  01  |   0   | PASS
   220000 | TC05    | Darkness + Motion (D=0, M=1)     |   011   |  10  |   0   | PASS
   300000 | TC06    | Transition: Daylight to Darkness |   010   |  01  |   0   | PASS
   380000 | TC07    | Transition: No Motion to Motion  |   011   |  10  |   0   | PASS
   420000 | TC08    | Transition: Motion to No Motion  |   010   |  01  |   0   | PASS
   460000 | TC09    | Fault Activation (F=1 -> S4)     |   100   |  11  |   1   | PASS
   500000 | TC10    | Fault Recovery (F=0 -> S3 Normal)|   011   |  10  |   0   | PASS
  5650000 | TC11    | PWM OFF Mode (Duty = 0/256 = 0%) |   001   |  00  |   0   | PASS
 15430000 | TC12    | PWM REDUCED Mode (Duty=77/256)   |   010   |  01  |   0   | PASS
 25670000 | TC13    | PWM FULL Mode (Duty=256/256=100%)|   011   |  10  |   0   | PASS
--------------------------------------------------------------------------------
VERIFICATION SUMMARY:
  TOTAL TEST CASES EXECUTED : 13
  TOTAL TEST CASES PASSED   : 13
  TOTAL TEST CASES FAILED   : 0
  OVERALL VERIFICATION STATUS: >>> ALL TESTS PASSED SUCCESSFULLY <<<
================================================================================
```

---

## 5. Academic Mapping
- **UN Sustainable Development Goals**: SDG 9 (Target 9.4), SDG 12 (Target 12.2), SDG 13 (Target 13.2).
- **Washington Accord Knowledge Profile**: Fully mapped across WK1 to WK8.
