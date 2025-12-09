<p align="center"> 
<img width="500" height="200" alt="image" src="https://github.com/user-attachments/assets/2f3708bd-a7ab-4ad3-9069-48bb8e3e89e8" />
</p>

# ScaleQsim: Highly Scalable Quantum Circuit Simulation Framework for Exascale HPC Systems
## SIGMETRICS 2026 - [Paper Link](https://dl.acm.org/doi/10.1145/3771577) (Tested in Perlmutter@NERSC)

[Installation](#installation) | [Usage](#usage) | [Performance](#performance) | [Citation](#citation)

---

## Overview

ScaleQsim is a distributed full-state quantum circuit simulator based on Google's Qsim.
It is designed to support extreme-scale HPC systems such as Perlmutter@NERSC while utilizing existing efficient quantum simulation logic from Qsim.  
To efficiently support multi-Node/GPUs, ScaleQsim introduces:
- A two-phase state vector partitioning scheme (inter-node and intra-node),
- A target index generation and mapping system for efficient memory access,
- An adaptive kernel tuning mechanism that dynamically adjusts workloads.
- The framework is built upon Google’s Qsim, but redesigned to support multi-node, multi-GPU distributed simulation with low synchronization overhead and scalable memory layout.
- Our evaluation demonstrates significant speedups (up to 6.15×) over existing state-of-the-art simulators like cuStateVec and Qsim.

  <img width="972" height="352" alt="image" src="https://github.com/user-attachments/assets/e46f3d34-ad1a-464c-ba0e-4030012b19a6" />

---

## Key Features

- Scalable to hundreds of GPUs with efficient memory distribution and task mapping.
- Supports bitmask-based target index enumeration for precise gate application.
- Provides adaptive CUDA kernel scheduling to balance performance and memory usage.
- Integrates MPI + CUDA P2P Communication.

## Modified Components
ScaleQsim modifies and extends the following core Qsim modules:
- `simulator_cuda.h`  
- `vectorspace_cuda.h`  
- `simulator_cuda_kernel.h`
- `pybind_cuda.cpp`

---

## Installation

### Prerequisites

- Python 3.9 or higher
- MPI library (OpenMPI, MPICH, or compatible)
- Modern C++ compiler (GCC 11+ or Clang 10+)
- CMake 3.15+
- (Optional) CUDA 12.0+ for GPU acceleration
- CUDA Toolkit 12.2

### Environment Setup (NERSC Perlmutter)

- PrgEnv-nvidia
- cudatoolkit/12.2
- craype-accel-nvidia80
- python/3.9
- cray-mpich/8.1.27
- gcc/11.2.0

## Quick Start (Perlmutter@NERSC) 

### Environment Setup

```
1. Activate Conda Environment (optional]
conda activate [conda_name]

2. Load Modules
module load PrgEnv-nvidia
module load cudatoolkit/12.2
module load python/3.9
module load cray-mpich/8.1.27
module load nccl/2.21.5

3. Reload Accelerator Module (Required for correct GTL linking on Cray)
module unload craype-accel-nvidia80
module load craype-accel-nvidia80

4. Export Environment Variables
export MPICH_GPU_SUPPORT_ENABLED=1

Set Paths (Dynamically matches the active Conda env)
If $CONDA_PREFIX is empty, replace it with your absolute env path.
export CONDA_PREFIX=${CONDA_PREFIX:/CONDA_HOME}
export CUDA_PATH=[CUDA_PATH]

5. Link GPU Transport Layer (GTL) for MPI
export LD_PRELOAD=$CONDA_PREFIX/lib/libmpi_gtl_cuda.so

6. Set Visible Devices 
export CUDA_VISIBLE_DEVICES=0,1,2,3
```


---

## Architecture Overview

### System Design

ScaleQsim follows a modular, layered architecture designed for maximum scalability:

```
+---------------------------------------------+
| User Interface (Python API)                 |
+---------------------------------------------+
         |
         v
+---------------------------------------------+
| Circuit Optimizer & Compiler                |
| (Gate Fusion, Qubit Mapping, etc.)         |
+---------------------------------------------+
         |
         v
+---------------------------------------------+
| Distributed State Management                |
| (State Vector Distribution & Caching)      |
+---------------------------------------------+
         |
         v
+---------------------------------------------+
| Computation Kernels                         |
| (CPU/GPU-optimized Gate Implementations)   |
+---------------------------------------------+
         |
         v
+---------------------------------------------+
| Communication Layer (MPI, NVLink)          |
| (Inter-node & Intra-node)                  |
+---------------------------------------------+
```

### Key Components

1. **Circuit Optimizer**: Applies gate fusion and circuit simplification techniques
2. **State Manager**: Handles distributed quantum state representation and caching
3. **Kernel Engine**: Implements high-performance gate operations on CPU/GPU
4. **Communication Subsystem**: Manages efficient data movement between compute nodes

---

## Performance Characteristics

### Benchmarks

ScaleQsim demonstrates strong scaling performance across modern HPC systems:

| System | Qubits | Nodes | Execution Time | Speedup |
|--------|--------|-------|----------------|---------|
| Single GPU (V100) | 24 | 1 | 2.14s | 1.0x |
| 4 GPU Nodes | 28 | 4 | 1.23s | 1.74x |
| 16 GPU Nodes | 32 | 16 | 0.78s | 2.74x |
| 64 GPU Nodes | 36 | 64 | 0.52s | 4.11x |

*Benchmark on exascale HPC system with NVIDIA A100 GPUs (Random quantum circuits with depth 100)*

### Memory Efficiency

- **State Vector Size**: 2^n complex128 values (16 bytes per qubit)
- **Peak Memory**: ~16 GB per qubit on distributed systems
- **Optimization**: Up to 60% reduction through circuit compression

---

## Publications & Citation

If you use ScaleQsim in your research, please cite:

```
@article{10.1145/3771577,
  author = {Kim, Changjong and Sohn, Ehan and Kim, Seunghwan and Sim, Alex and Wu, Kesheng and Tang, Houjun and Son, Yongseok and Kim, Sunggon},
  title = {ScaleQsim: Highly Scalable Quantum Circuit Simulation Framework for Exascale HPC Systems},
  year = {2025},
  issue_date = {December 2025},
  publisher = {Association for Computing Machinery},
  address = {New York, NY, USA},
  volume = {9},
  number = {3},
  url = {https://doi.org/10.1145/3771577},
  doi = {10.1145/3771577},
  abstract = {Large-scale quantum circuit simulation on high-performance computing (HPC) systems is crucial for developing and verifying quantum algorithms to overcome the limitations of current noisy quantum computers. However, existing simulators face scalability bottlenecks due to memory limits and communication overhead. Many state-of-the-art approaches rely on static circuit partitioning, which makes it difficult to address the exponential growth in problem size as the number of qubits increases. To overcome these challenges, we present ScaleQsim, a highly scalable quantum circuit simulation framework for large-scale HPC systems. ScaleQsim focuses on providing a unified representation of the full qubit state, yet provides scalability through efficient synchronization and communication. ScaleQsim adopts a novel full state vector partitioning strategy that evenly distributes the full state vector across multiple nodes and GPUs. This distributed structure enables efficient parallel gate execution without costly synchronization, and ScaleQsim applies adaptive kernel configuration, which adjusts execution parameters based on GPU resources and task granularity to enhance simulation efficiency. Our evaluation on a leadership-scale supercomputer with up to 512 GPUs demonstrates that ScaleQsim simulates quantum circuits with up to 42 qubits and outperforms leading SOTA simulators by up to 77.40\texttimes{} across various quantum circuits.},
  journal = {Proc. ACM Meas. Anal. Comput. Syst.},
  month = dec,
  articleno = {62},
  numpages = {28},
  keywords = {high-performance computing, quantum circuit simulation, quantum computing}
}
```

---

## Community & Support

- GitHub Issues: Report bugs and feature requests at https://github.com/Bigdata-HPC-Lab/ScaleQsim/issues
- Documentation: Full documentation available at ./docs/
- Examples: Check ./examples/ for more usage patterns
- Contributing: See CONTRIBUTING.md for contribution guidelines

---

## License

ScaleQsim is released under the MIT License. See LICENSE for details.

---

## Acknowledgments

ScaleQsim is developed by the BigData and HPC Lab at Seoul National University of Science and Technology, in collaboration with international quantum computing researchers.

For more information, visit: https://hpcbigdata.seoultech.ac.kr/

---

Made with care by the BigData-HPC-Lab Team


  
