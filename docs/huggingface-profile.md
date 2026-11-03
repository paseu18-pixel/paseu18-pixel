# Hugging Face Profile Configuration Guide for Mateo Silva-Carvalho

## 1. Profile Settings (Settings -> Profile)

| Field | Value |
| :--- | :--- |
| **Full Name** | `Mateo Silva-Carvalho` |
| **Bio** | `Distributed Storage & Database Internals engineer focusing on high-throughput I/O traces, vector index memory layout benchmarks, and zero-copy tensor streaming architectures.` |
| **GitHub Username** | `paseu18-pixel` |
| **Homepage / Website** | `https://github.com/paseu18-pixel` |
| **Interests** | `database-internals`, `storage-engines`, `vector-indexing`, `high-performance-computing`, `linux-io` |

---

## 2. Hugging Face Profile Introduction / Organization Overview

### Overview

Welcome to my Hugging Face systems & storage research repository. My work centers on **database storage engine internals**, **high-performance memory-mapped data structures**, and **low-latency I/O access patterns for large-scale embeddings and vector search**.

This space is dedicated to open-source systems assets for data-intensive computing: publishing realistic enterprise storage trace datasets (LSM compaction logs, Zipfian key-value workloads), memory-alignment benchmarks for multi-million vector indexes, and zero-copy NVMe streaming experiments using Linux `io_uring` and SPDK.

---

### Core Research Directions & Open Datasets

#### 1. High-Resolution Storage & I/O Trace Datasets
- **LSM-Tree Write-Amplification Traces**: Granular disk access logs recording compaction write-amplification factors (WAF) under uniform, Zipfian, and latest-skewed key-value distributions.
- **Enterprise Key-Value Workload Captures**: Anonymized read/write ratios, tombstone accumulation statistics, and bloom filter saturation patterns from production-scale workloads.

#### 2. Vector Index Memory Layout & Latency Benchmarks
- **Cacheline-Aligned Vector Layout Benchmarks**: Precision CPU cache-miss and TLB thrashing measurements comparing Flat, HNSW, and IVF-PQ index memory representations on modern x86_64 AVX-512 and ARM NEON architectures.
- **Quantization I/O Throughput Curves**: Real-world benchmarks measuring sequential vs random read degradation when streaming quantized embedding vectors directly from PCIe 4.0/5.0 NVMe drives.

#### 3. Kernel-Bypass Streaming & Zero-Copy Primitives
- **Direct NVMe Tensor Streaming (`io_uring` / SPDK)**: Reference code and datasets evaluating the elimination of filesystem page-cache copying when loading multi-gigabyte safetensors directly into memory-mapped DMA buffers.
- **Columnar vs Binary Page Eviction Models**: Comparative access latency traces testing W-TinyLFU and 2Q eviction algorithms against columnar Parquet and binary page structures.

---

### Key Open Datasets & Repositories

| Repository / Space | Category | Focus |
| :--- | :--- | :--- |
| **[lsm-compaction-traces](https://huggingface.co/datasets/paseu18/lsm-compaction-traces)** | Dataset | Fine-grained block I/O write-amplification logs across varied compaction heuristics |
| **[vector-index-cache-bench](https://huggingface.co/datasets/paseu18/vector-index-cache-bench)** | Dataset | Hardware cycle counts and cacheline miss traces for large-scale HNSW graph traversals |
| **[iouring-tensor-streamer](https://huggingface.co/spaces/paseu18/iouring-tensor-streamer)** | Space | Interactive benchmark visualizing kernel-bypass zero-copy streaming throughput |

---

### Storage Engineering Invariants

Across all data pipelines, storage benchmarks, and code repositories, strict engineering constraints are maintained:

- **Durability Guarantee**: Fsync / fdatasync barrier integrity strictly verified on all persistent write workloads.
- **Hardware Alignment**: 4096-byte page and 64-byte CPU cacheline alignment enforced across all binary datasets.
- **Zero-Allocation Data Paths**: Zero dynamic heap allocations during high-frequency read and traversal loops.

---

### Connect & Collaborate

- **GitHub Profile**: [github.com/paseu18-pixel](https://github.com/paseu18-pixel)
- **GitLab Profile**: [gitlab.com/paseu18](https://gitlab.com/paseu18)
- **Email**: `mateo.silva.carvalho` [at] `proton.me`
