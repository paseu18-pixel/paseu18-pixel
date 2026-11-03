# GitLab Profile Configuration Guide for Mateo Silva-Carvalho

## 1. Profile Settings (User Settings -> Profile)

| Field | Value |
| :--- | :--- |
| **Full Name** | `Mateo Silva-Carvalho` |
| **Job Title** | `Distributed Storage & Database Internals Engineer` |
| **Bio** | `High-throughput database storage engines, append-only WAL & distributed consensus. Kernel-bypass asynchronous I/O (io_uring), LSM trees & lockless page caches in Go, C++20 and Zig.` |
| **Location** | `Florianópolis, Brazil` |
| **Website URL** | `https://github.com/paseu18-pixel` |

---

## 2. GitLab Profile README (`paseu18/paseu18` repository)

```markdown
| [KR 한국어](./docs/locales/ko.md) | [US English](./README.md) | [JP 日本語](./docs/locales/ja.md) | [CN 简体中文](./docs/locales/zh-CN.md) | [ES Español](./docs/locales/es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./docs/locales/fr.md) | [DE Deutsch](./docs/locales/de.md) | [RU Русский](./docs/locales/ru.md) | [BR Português](./docs/locales/pt-BR.md) | [SA العربية](./docs/locales/ar.md) |

---

# Mateo Silva-Carvalho

**Distributed Storage & Database Internals Engineer | Systems Craftsman**  
*Florianópolis, Brazil | UTC-3*

> *"Disk I/O is a crime scene. Write amplification is the perpetrator. B-trees and LSM-trees are the forensic evidence."*

I build high-throughput database storage engines, append-only write-ahead logs (WAL), and distributed consensus infrastructure. This GitLab workspace hosts enterprise storage benchmark pipelines, kernel-bypass testbeds, continuous fault-injection runners, and bare-metal NVMe stress test harnesses.

---

## Storage Infrastructure & CI/CD Pipelines

- **Kernel-Bypass I/O Verification**: Dedicated GitLab CI Runners configured with rootless `io_uring` support (`IORING_FEAT_NODROP`, fixed buffers) on Linux 6.8+ real-time kernels.
- **Continuous Fault Injection**: Rigorous crash-recovery test pipelines simulating sudden power failures and kernel panics under heavy concurrent append workloads.
- **Cycle-Accurate Benchmarking**: GitLab CI benchmark stages running `fio` and custom WAL micro-benchmarks to detect latency and write-amplification regressions on pull requests.
- **Hardware Testbed Provisioning**: Bare-metal environment scripts for dual AMD EPYC storage racks and PCIe 4.0 NVMe arrays.

---

## Technical Stack & Tooling

### Core Programming Languages
| Language | Primary Focus | Application & Experience |
| :--- | :--- | :--- |
| **Go (1.23+)** | Distributed engines, consensus protocols | Custom memory arenas, zero-allocation serialization, runtime profiling |
| **C++ (C++20 / C++23)** | Low-latency storage cores, LSM trees | Concepts, SIMD intrinsics (AVX-512 / NEON), cacheline alignment |
| **Zig (0.13+)** | Systems utilities, `io_uring` runtimes | Explicit memory allocation, comptime metaprogramming, C ABI interop |
| **Rust** | Concurrent data structures, wire parsers | Lock-free synchronization, atomic operations, zero-copy wire decoders |
| **Python** | Chaos test harnesses, benchmarks | Deterministic fault-injection harnesses, throughput/latency plotting |

### Storage Engine Internals & Kernel Primitives
| Category | Stack & Kernel Interfaces |
| :--- | :--- |
| **Asynchronous I/O** | Linux `io_uring` (SQPOLL, fixed buffers), POSIX AIO, epoll event loops |
| **File System Semantics** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **Storage Protocols** | NVMe-oF (NVMe over Fabrics), PCIe 4.0/5.0 NVMe block layers, SPDK drivers |
| **Profiling & Tracing** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, flamegraphs, Valgrind Massif |

---

## Primary Repositories on GitLab

### [ring-fs-ci](https://gitlab.com/paseu18/ring-fs-ci)
*Continuous integration and stress test harnesses for the `ring-fs` userspace storage engine.*
- Multi-architecture matrix builds across x86_64 and aarch64 Linux systems.
- Sustained sequential write tests verifying over 800,000 IOPS without page corruption.
- Memory leak detection via Valgrind Massif and AddressSanitizer (ASan) on every commit.

### [wal-stress-runner](https://gitlab.com/paseu18/wal-stress-runner)
*Deterministic fault-injection runner for append-only Write-Ahead Logging (WAL) implementations.*
- Simulates power cuts during concurrent group commits using kernel `dm-flakey` target modules.
- Validates that zero uncommitted records are acknowledged and all committed transactions survive.
- Comprehensive log verification reporting embedded directly into GitLab pipeline summaries.

### [storage-perf-matrix](https://gitlab.com/paseu18/storage-perf-matrix)
*Comprehensive `fio` and YCSB benchmark suites tracking latency curves across NVMe block layers.*
- Generates deterministic SVG throughput/latency comparison graphs on merge requests.
- Rejects commits that increase p99 fsync latency by more than 3%.

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

## Get in Touch

- **GitHub Profile**: [github.com/paseu18-pixel](https://github.com/paseu18-pixel)
- **Email**: `mateo.silva.carvalho` [at] `proton.me`
- **GPG Key Fingerprint**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
```
