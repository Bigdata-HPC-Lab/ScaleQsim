# ScaleQsim: Highly Scalable Quantum Circuit Simulation Framework for Exascale HPC Systems

[Installation](#installation) | [Usage](#usage) | [Performance](#performance) | [Citation](#citation)

---

## Overview

ScaleQsim is a distributed full-state quantum circuit simulator designed for large-scale HPC systems.  
To efficiently support multi-Node/GPUs, ScaleQsim introduces:
- A two-phase state vector partitioning scheme (inter-node and intra-node),
- A target index generation and mapping system for efficient memory access,
- An adaptive kernel tuning mechanism that dynamically adjusts workloads.
- The framework is built upon Google’s Qsim, but redesigned to support multi-node, multi-GPU distributed simulation with low synchronization overhead and scalable memory layout. Our evaluation demonstrates significant speedups (up to 6.15×) over existing state-of-the-art simulators like cuStateVec and Qsim.
  

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

- Python 3.8 or higher
- MPI library (OpenMPI, MPICH, or compatible)
- Modern C++ compiler (GCC 9+ or Clang 10+)
- CMake 3.15+
- (Optional) CUDA 11.0+ for GPU acceleration


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
@article{scaleqsim2024,
  title={ScaleQsim: Highly Scalable Quantum Circuit Simulation Framework for Exascale HPC Systems},
  author={Bigdata-HPC-Lab and Seoul National University of Science and Technology},
  journal={ACM SIGMETRICS},
  year={2024},
  doi={10.1145/3771577}
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


  
