| [KR 한국어](./docs/locales/ko.md) | [US English](./README.md) | [JP 日本語](./docs/locales/ja.md) | [CN 简体中文](./docs/locales/zh-CN.md) | [ES Español](./docs/locales/es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./docs/locales/fr.md) | [DE Deutsch](./docs/locales/de.md) | [RU Русский](./docs/locales/ru.md) | [SA العربية](./docs/locales/ar.md) | [IN हिन्दी](./docs/locales/hi.md) |

---

# Mateo Silva-Carvalho

**Distributed Storage & Database Internals Engineer | Systems Craftsman**  
*Florianópolis, Brazil | UTC-3*

> *"Disk I/O is a crime scene. Write amplification is the perpetrator. B-trees and LSM-trees are the forensic evidence."*

I build high-throughput database storage engines, append-only write-ahead logs (WAL), and distributed consensus infrastructure. My day-to-day focus centers on low-level Linux systems programming, kernel-bypass asynchronous I/O (`io_uring`), memory-mapped persistent data structures, and lockless concurrent page caches in modern Go, C++20, and Zig. 

Outside the terminal, you will find me roasting single-origin Brazilian specialty coffee (Catuaí and Yellow Bourbon), windsurfing the Atlantic chop off Santa Catarina's coast, or hand-soldering custom split ergonomic mechanical keyboards.

---

## What I Do

- **Database Storage Engines**: Designing leveled and tiered Log-Structured Merge (LSM) trees, zero-allocation Write-Ahead Logging (WAL) with adaptive group commits, and block cache eviction policies (W-TinyLFU, 2Q, Clock-Pro).
- **Asynchronous Storage & Kernel Bypass**: Maximizing NVMe SSD saturation using Linux `io_uring` with registered buffers (`IORING_REGISTER_BUFFERS`), zero-copy socket splicing, and direct aligned block transfers (`O_DIRECT`).
- **Distributed Consensus Systems**: Implementing Multi-Raft state machines, vector clock log compaction, and deterministic crash-recovery testing via simulated network partitions and fault injection.
- **Cache-Conscious Data Structures**: Writing lock-free skip lists, cacheline-aligned radix trees (ART), adaptive prefix B+trees, and memory-safe arena allocators.

---

## Technical Stack & Systems Tooling

### Core Programming Languages
| Language | Primary Domain | Experience & Focus |
| :--- | :--- | :--- |
| **Go (1.23+)** | Distributed engines, consensus protocols | Custom memory arenas, zero-alloc serialization, runtime profiling |
| **C++ (C++20 / C++23)** | Low-latency storage internals, LSM trees | Concepts, SIMD intrinsics (AVX-512 / NEON), cacheline alignment |
| **Zig (0.13+)** | Systems utilities, `io_uring` runtimes | Explicit memory allocation, comptime metaprogramming, C ABI interop |
| **Rust** | Concurrent data structures, safe parsers | Lock-free synchronization, atomic operations, zero-copy wire decoders |
| **Python** | Chaos test harnesses, benchmarks, analysis | Deterministic fault-injection harnesses, plotting throughput/latency curves |

### Storage Engine Internals & Algorithms
| Domain | Architectures, Primitives & Methods |
| :--- | :--- |
| **LSM-Tree Internals** | Leveled compaction heuristics, SSTable block formats, Blocked Bloom filters |
| **B-Tree & Indexing** | Concurrent B+Tree page engines, Adaptive Radix Trees (ART), Trie prefix indexes |
| **Cache Policies** | W-TinyLFU, 2Q (Two-Queue), Clock-Pro, LRU with segment pooling |
| **Compression & Encoding** | Zstandard (zstd), Snappy, Varint/ZigZag encoding, Roaring Bitmaps |

### Linux Storage & Kernel Primitives
| Category | Stack & Kernel Interfaces |
| :--- | :--- |
| **Asynchronous I/O** | Linux `io_uring` (SQPOLL, fixed buffers), POSIX AIO, epoll event loops |
| **File System Semantics** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **Storage Protocols** | NVMe-oF (NVMe over Fabrics), PCIe 4.0/5.0 NVMe block layers, SPDK drivers |
| **Profiling & Tracing** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, flamegraphs, Valgrind Massif |

### Testing, Infrastructure & Tooling
| Category | Environment & Toolchain |
| :--- | :--- |
| **Build & Toolchains** | CMake, Ninja, Zig build system, Go toolchain, GCC / Clang 18+ |
| **Fault Injection & Chaos** | Deterministic state-machine simulators, Jepsen-style network partition injection |
| **Benchmarking Suites** | `fio` (Flexible I/O Tester), YCSB (Yahoo! Cloud Serving Benchmark), custom WAL stress harnesses |
| **Target Platforms** | Linux (Ubuntu LTS, Debian, Fedora Workstation), Alpine Linux containers |

---

## Featured Open-Source Projects

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*An embedded, lock-free LSM storage engine written in Go with zero-allocation read paths.*
- Implements concurrent memtables using lock-free skip lists with cacheline-aligned nodes.
- Features adaptive tiered compaction designed specifically to minimize NVMe write amplification.
- Employs blocked SIMD Bloom filters that reduce false-positive rates to under 0.8% with zero heap allocations.
- Handles crash recovery with an append-only WAL utilizing crc32c checksum validation and vectorized block flushing.

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*High-performance userspace append-only storage engine built in Zig via native `io_uring`.*
- Operates entirely on registered fixed buffers (`IORING_REGISTER_BUFFERS`) and dedicated kernel submission poller threads.
- Achieves over 820,000 sustained sequential write IOPS on consumer PCIe 4.0 NVMe hardware.
- Fully userspace page cache manager with predictable 4KB page alignment and direct hardware zero-copy serialization.

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*A deterministic simulation harness for distributed consensus state machines.*
- Tests distributed log replication engines against simulated asynchronous network partitions, message loss, and random clock drift.
- Runs thousands of virtual nodes in a single process using discrete virtual time stepping for reproducible bug reproduction.
- Validated state transitions across multiple Raft and Multi-Paxos reference implementations.

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*Experimental custom Virtual File System (VFS) driver routing SQLite queries directly to NVMe SSDs.*
- Eliminates the OS filesystem layer by writing directly to block ranges through SPDK userspace NVMe drivers.
- Reduces p99 transaction commit latency by 42% on high-frequency transaction workloads.

---

## Storage Engine Invariants & Code of Honor

When writing low-level storage routines, these four invariants are strictly enforced:

```
[Storage Engine Mechanical Invariants]
├── 1. Durability is Non-Negotiable
│   └── Never return success to a synchronous client before an fsync / fdatasync barrier completes.
├── 2. Respect Memory & Cacheline Alignment
│   └── All on-disk page structures must be strictly aligned to 4096-byte boundaries (O_DIRECT safe).
├── 3. Eliminate Lock Contention on Hot Paths
│   └── Read and append paths must remain entirely lock-free using atomic CAS and sequence pointers.
└── 4. Validate Under Extreme Adversity
    └── A storage engine is only as good as its survival rate under sudden kernel panics and power-loss tests.
```

---

## Hardware & Benchmarking Rig

- **Storage Testbed Rack**: Dual AMD EPYC 7543, 256GB ECC DDR4-3200, 4x Solidigm P44 Pro 2TB PCIe 4.0 NVMe (software RAID0 / JBOD)
- **Primary Workstation**: Framework Laptop 16 (Linux Debian Sid / Wayland)
- **Input Hardware**: Corne LP split mechanical keyboard (lubed Gateron Low Profile Red switches, custom Colemak-DH layout)
- **Coffee Station**: Timemore Chestnut C3 manual grinder, Cafelat Robot manual lever espresso press

---

## Get in Touch

- **GitHub Discussions**: Feel free to open an issue or discussion on my repositories for storage engine architecture talks.
- **Email**: `mateo.silva.carvalho` [at] `proton.me`
- **GPG Key Fingerprint**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
