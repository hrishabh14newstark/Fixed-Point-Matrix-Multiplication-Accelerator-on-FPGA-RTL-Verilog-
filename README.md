# Fixed-Point-Matrix-Multiplication-Accelerator-on-FPGA-RTL-Verilog-
Target Roles: ASIC/FPGA Design Engineer, Silicon Verification Engineer, GPU Hardware Engineer.
# Fixed-Point Matrix Multiplication Accelerator on FPGA (RTL / Verilog)

A high-performance, parameterizable Systolic Array Matrix Multiplication Hardware Accelerator implemented in SystemVerilog/Verilog. Designed for FPGA deployment, this accelerator offloads dense matrix compute workloads—the fundamental building block of modern GPU Tensor Cores and Neural Network Inference Engines.

---

## 📄 Overview

Modern AI workloads and Graphics Processing Units (GPUs) spend substantial compute cycles executing General Matrix Multiplication ($	ext{GEMM}$). This project demonstrates a hardware-level acceleration engine using a **2D Systolic Array** architecture with multiply-accumulate ($	ext{MAC}$) processing elements ($	ext{PEs}$). 

By streaming matrix data through a spatial grid of processing units, memory bandwidth constraints are minimized via data reuse, maximizing compute throughput and energy efficiency.

---

## 🏗️ Architectural Overview

```
                 +-----------------------------------+
                 |           AXI4-Lite Slave         |
                 +-----------------+-----------------+
                                   |
                                   v
                 +-----------------------------------+
                 |        Input/Output FIFOs         |
                 +-----------------+-----------------+
                                   |
            Matrix A (Rows)        |        Matrix B (Columns)
                  |                v                 |
                  +-------->  Systolic Array <-------+
                               (4x4 PE Grid)
                                   |
                                   v
                            Matrix C Output
                            (Accumulated)
```

### Key Components

1. **Processing Element (PE)**:
   * **MAC Core**: Includes dynamic fixed-point multiplier and continuous accumulator.
   * **Data Pipeline Registers**: Pipelined routing registers to pass Matrix A values horizontally and Matrix B values vertically on each clock cycle.
   * **Fixed-Point Arithmetic**: Configurable $16	ext{-bit}$ fixed-point representation ($Q8.8$ or $Q12.4$) with saturation logic to prevent overflow/underflow wrap-around.

2. **$4 	imes 4$ Systolic Array**:
   * Fully parallel spatial mesh of 16 identical PEs.
   * Skewed data input feeder network to align wave-front computing across rows and columns.
   * Scalable architecture designed for easy expansion ($8 	imes 8$, $16 	imes 16$).

3. **AXI4-Lite Interface**:
   * Standard AXI4-Lite slave wrapper for seamless integration with host processors (e.g., ARM Cortex on Xilinx Zynq / MicroBlaze).
   * Memory-mapped register file for control signals (`START`, `DONE`, `RESET`, `CONFIG`), status flags, and data streaming FIFOs.

4. **Control Unit (FSM)**:
   * State machine managing data streaming, systolic computation phases, result readout, and interrupt generation upon completion.

---

## 🚀 Key Features

* **High Throughput**: Fully pipelined systolic grid achieving 1 MAC per PE per clock cycle at peak execution.
* **Efficient Data Reuse**: Inputs flow spatially across adjacent PEs, reducing global memory read accesses by up to $N	imes$.
* **AXI4-Lite Compliance**: Plug-and-play compatibility with AMD Xilinx Vivado IP Integrator and standard SOC buses.
* **Parameterizable Design**: Configurable data bit-widths, matrix dimensions ($N 	imes N$), and fractional precision using SystemVerilog parameters.
* **Comprehensive Test Suite**: Automated self-checking SystemVerilog testbenches with randomized input matrices and golden reference validation.

---

## 📂 Project Directory Structure

```
├── rtl/
│   ├── pe.v                # Single Processing Element (MAC + Registers)
│   ├── systolic_array.v    # N x N Systolic Array Grid
│   ├── input_feeder.v      # Skewing & Delay Buffers for Systolic Flow
│   ├── axi_lite_slave.v    # AXI4-Lite Register & Control Interface
│   └── matrix_accel_top.v  # Top-level integration module
├── tb/
│   ├── tb_pe.sv            # Unit Testbench for Processing Element
│   ├── tb_systolic_array.sv# Verification for N x N Grid
│   └── tb_top_axi.sv       # Top-Level System Testbench with AXI VIP
├── sim/
│   ├── Makefile            # Automation script for ModelSim / Icarus / Verilator
│   └── wave.do             # Waveform visualization config
├── constraints/
│   └── timing.xdc          # Xilinx Design Constraints (Clock & I/O)
├── docs/
│   └── architecture.png    # High-level block diagram
└── README.md
```

---

## 🛠️ Getting Started & Simulation

### Prerequisites

* **HDL Simulator**: ModelSim / QuestaSim, Icarus Verilog, or Verilator
* **Synthesis / FPGA Toolchain**: AMD Xilinx Vivado (2020.2 or later) / Intel Quartus Prime
* **Build System**: `make`

### Running Simulation (Icarus Verilog Example)

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/fpga-matrix-multiplier.git
   cd fpga-matrix-multiplier
   ```

2. **Run Unit Tests**:
   ```bash
   make sim_pe
   make sim_array
   ```

3. **Run Full Top-Level Simulation**:
   ```bash
   make sim_top
   ```

4. **View Waveforms in GTKWave**:
   ```bash
   gtkwave sim/top_tb.vcd
   ```

---

## 📊 Synthesis & Resource Utilization

*(Synthesized targeting Xilinx Artix-7 `xc7a100tcsg324-1` @ **100 MHz**)*

| Resource | Used | Available | Utilization (%) |
| :--- | :---: | :---: | :---: |
| **LUTs** | 1,420 | 63,400 | 2.24% |
| **FFs** | 1,850 | 126,800 | 1.45% |
| **DSPs (DSP48E1)** | 16 | 240 | 6.67% |
| **BRAMs** | 2 | 135 | 1.48% |

### Timing Analysis
* **Target Clock**: $100 	ext{ MHz}$ ($10 	ext{ ns}$ period)
* **Worst Negative Slack (WNS)**: $+1.84 	ext{ ns}$ (MET)
* **Total Negative Slack (TNS)**: $0.00 	ext{ ns}$

---

## 📜 Future Enhancements

* [ ] Add support for floating-point ($FP16$ / $BFloat16$) arithmetic cores.
* [ ] Integrate DMA (AXI-Full) engine for bulk burst memory transfers.
* [ ] Scale array grid to $16 	imes 16$ or $32 	imes 32$ with dynamic sparsity logic.
* [ ] Provide Python Host Driver API (using `pynq` or `cffi`) for Jupyter Notebook control on Xilinx Zynq boards.

---

## 👤 Author

**Diploma Engineer Trainee** - RTL & Hardware Acceleration Enthusiast  
* **Specialization**: Electronics Engineering / SystemVerilog & FPGA Accelerator Design  
* **LinkedIn**: [Your Profile Link]  
* **GitHub**: [Your GitHub Link]

---

## 📄 License

This project is licensed under the MIT License - see the `LICENSE` file for details.
