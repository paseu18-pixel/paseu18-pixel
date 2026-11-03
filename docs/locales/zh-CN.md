| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# 马特奥·席尔瓦-卡瓦略 (Mateo Silva-Carvalho)

**分布式存储与数据库内核工程师 | 系统工匠**  
*巴西弗洛里亚诺波利斯 | UTC-3*

> *“磁盘 I/O 是犯罪现场，写放大（Write Amplification）是罪魁祸首，而 B 树与 LSM 树则是无可辩驳的法医证据。”*

我专注于构建高吞吐量数据库存储引擎、纯追加写前日志（WAL）以及分布式共识基础设施。日常研发工作主要围绕底层 Linux 系统编程、内核旁路异步 I/O（`io_uring`）、内存映射持久化数据结构，以及基于现代 Go、C++20 和 Zig 的无锁并发页缓存展开。

离开终端后，我热衷于烘焙巴西单一产地精品咖啡（Catuaí 与 Yellow Bourbon）、在圣卡塔琳娜海滨的大西洋海浪中风帆冲浪，以及手工焊接定制人体工学分体机械键盘。

---

## 核心领域与工作方向

- **数据库存储引擎**：设计分层（Leveled）与阶梯式（Tiered）LSM（Log-Structured Merge）树、基于自适应组提交的零分配 WAL（Write-Ahead Logging），以及块缓存淘汰算法（W-TinyLFU、2Q、Clock-Pro）。
- **异步存储与内核旁路**：利用预注册固定缓冲区（`IORING_REGISTER_BUFFERS`）、零拷贝套接字拼接（`splice`）与严格对齐的块直接传输（`O_DIRECT`），通过 Linux `io_uring` 彻底榨干 NVMe SSD 的带宽极限。
- **分布式共识系统**：实现 Multi-Raft 状态机、向量时钟日志压缩，并通过模拟网络分区与故障注入进行确定性崩溃恢复验证。
- **缓存敏感型并发数据结构**：编写无锁跳表、缓存行对齐的自适应基数树（ART）、前缀自适应 B+ 树，以及内存安全的 Arena 内存分配器。

---

## 技术栈与底层工具体系

### 核心编程语言
| 语言 | 主攻领域 | 经验与关注重点 |
| :--- | :--- | :--- |
| **Go (1.23+)** | 分布式引擎、共识协议 | 自定义内存 Arena、零分配序列化、运行时剖析 |
| **C++ (C++20 / C++23)** | 低延迟存储内核、LSM 树 | Concepts 约束、SIMD 指令集（AVX-512 / NEON）、缓存行对齐 |
| **Zig (0.13+)** | 系统级工具、`io_uring` 运行时 | 显式内存分配、comptime 编译期元编程、C ABI 无缝互操作 |
| **Rust** | 并发数据结构、安全解析器 | 无锁同步原语、原子操作、零拷贝网络协议解码器 |
| **Python** | 混沌测试脚手架、基准测试 | 确定性故障注入框架、吞吐量与延迟曲线可视化分析 |

### 存储引擎内核与核心算法
| 领域 | 架构、底层原语与工程方法 |
| :--- | :--- |
| **LSM 树内部机制** | 分层压缩启发式算法、SSTable 数据块格式、块级布隆过滤器（Blocked Bloom Filters） |
| **B 树与索引技术** | 并发 B+ 树页引擎、自适应基数树（ART）、Trie 前缀索引 |
| **缓存淘汰策略** | W-TinyLFU、2Q（Two-Queue）、Clock-Pro、基于分段池化的 LRU |
| **压缩与数据编码** | Zstandard (zstd)、Snappy、变长整数（Varint）/ ZigZag 编码、Roaring Bitmaps |

### Linux 存储与内核原语
| 类别 | 技术栈与内核接口 |
| :--- | :--- |
| **异步 I/O** | Linux `io_uring`（SQPOLL 轮询、固定缓冲区）、POSIX AIO、epoll 事件循环 |
| **文件系统语义** | `O_DIRECT`、`fallocate`、`fdatasync`、`sync_file_range`、`mmap` / `madvise` |
| **存储底层协议** | NVMe-oF（以太网/光纤 NVMe）、PCIe 4.0/5.0 NVMe 块层、SPDK 用户态驱动 |
| **性能分析与追踪** | `perf`、eBPF（`bpftrace`）、Linux `ftrace`、火焰图（flamegraphs）、Valgrind Massif |

### 测试体系、基础设施与工具链
| 类别 | 运行环境与工具链 |
| :--- | :--- |
| **构建系统** | CMake、Ninja、Zig 构建系统、Go 工具链、GCC / Clang 18+ |
| **故障注入与混沌工程** | 确定性状态机模拟器、Jepsen 风格网络分区注入器 |
| **基准测试套件** | `fio`（灵活 I/O 测试器）、YCSB、自定义 WAL 压力测试脚手架 |
| **目标运行平台** | Linux（Ubuntu LTS、Debian、Fedora Workstation）、Alpine Linux 容器 |

---

## 代表性开源项目

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*基于 Go 构建的嵌入式无锁 LSM 存储引擎，具备零分配读路径。*
- 实现具有缓存行对齐节点的无锁跳表并发 Memtable。
- 采用自适应分层压缩设计，专门用于最大限度降低 NVMe 写放大（Write Amplification）。
- 集成块级 SIMD 布隆过滤器，在零堆内存分配的前提下将假阳性率压低至 0.8% 以下。
- 采用追加写 WAL 配合 crc32c 校验码与向量化块刷盘，提供高可靠的崩溃恢复能力。

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*在 Zig 中完全基于原生 `io_uring` 打造的高性能用户态追加写存储引擎。*
- 完全运行于注册固定缓冲区（`IORING_REGISTER_BUFFERS`）与专用内核提交轮询（SQPOLL）线程之上。
- 在消费级 PCIe 4.0 NVMe 硬件上实现了超过 820,000 IOPS 的持续顺序写吞吐。
- 用户态页缓存管理器，具备确定性的 4KB 页面对齐与硬件直通零拷贝序列化能力。

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*专为分布式共识状态机打造的确定性模拟测试框架。*
- 在模拟的异步网络分区、报文丢失与随机时钟漂移环境下深度检验日志复制引擎。
- 在单个进程中通过离散虚构时钟步进驱动数千个虚拟节点，确保所有偶发并发缺陷 100% 可复现。
- 已全面验证多套 Raft 及 Multi-Paxos 参考实现的状态转移正确性。

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*实验性 SQLite 虚拟文件系统（VFS）驱动，将 SQL 查询直接路由至 NVMe SSD。*
- 借助 SPDK 用户态 NVMe 驱动直接写入块设备寻址空间，完全绕开 OS 文件系统开销。
- 在高频事务场景下将 p99 事务提交延迟有效降低 42%。

---

## 存储引擎工程准则（铁律）

在编写底层存储代码时，我始终严格恪守以下四项原则：

```
[存储引擎机械级铁律]
├── 1. 持久性（Durability）绝无妥协余地
│   └── 在 fsync / fdatasync 屏障完成前，严禁向同步客户端返回写入成功。
├── 2. 严守内存与缓存行物理对齐
│   └── 所有磁盘数据页结构必须严格按 4096 字节边界对齐（满足 O_DIRECT 要求）。
├── 3. 热路径上坚决杜绝锁竞争
│   └── 读取与追加写入路径必须通过原子 CAS 与序列指针保持完全无锁化。
└── 4. 历经极端逆境方能验证健壮性
    └── 未能经受住突然断电与内核崩溃模拟测试的存储系统，不能算作可靠。
```

---

## 硬件工作台与测试环境

- **存储测试机架**：双路 AMD EPYC 7543，256GB ECC DDR4-3200，4x Solidigm P44 Pro 2TB PCIe 4.0 NVMe（软件 RAID0 / JBOD）
- **主力工作机**：Framework Laptop 16（Linux Debian Sid / Wayland 架构）
- **输入设备**：Corne LP 分体式机械键盘（润滑 Gateron Low Profile 矮红轴，定制 Colemak-DH 键位）
- **咖啡角**：泰摩 Chestnut C3 手摇磨豆机，Cafelat Robot 手动杠杆式浓缩咖啡机

---

## 联系与技术交流

- **GitHub Discussions**：关于存储引擎架构与底层内部机制的技术探讨，欢迎在各仓库提 Issue 或发起 Discussions。
- **电子邮箱**：`mateo.silva.carvalho` [at] `proton.me`
- **GPG 密钥指纹**：`9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
