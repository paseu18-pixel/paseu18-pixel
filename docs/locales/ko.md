| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# 마테오 실바-카르발류 (Mateo Silva-Carvalho)

**분산 스토리지 & 데이터베이스 내부 엔진 엔지니어 | 시스템 크래프트맨**  
*브라질 플로리아노폴리스 | UTC-3*

> *"디스크 I/O는 범죄 현장입니다. 쓰기 증폭(Write Amplification)은 진범이며, B-Tree와 LSM-Tree는 그 포렌식 증거입니다."*

고처리량 데이터베이스 스토리지 엔진, 추가 전용(Append-only) Write-Ahead Log(WAL), 그리고 분산 합의 인프라를 설계하고 구축합니다. 저수준 리눅스 시스템 프로그래밍, 커널 바이패스 비동기 I/O(`io_uring`), 메모리 매핑 영속 데이터 구조체, 그리고 락프리 동시성 페이지 캐시를 모던 Go, C++20, Zig 언어로 구현하는 데 집중하고 있습니다.

터미널을 벗어나면 브라질 단일 원산지 스페셜티 커피(카투아이, 옐로우 버번)를 직접 로스팅하거나, 산타카타리나 해변에서 대서양 파도를 가르며 윈드서핑을 즐기거나, 커스텀 분할 인체공학 기계식 키보드를 직접 납땜하여 조립합니다.

---

## 전문 분야 및 주요 업무

- **데이터베이스 스토리지 엔진**: 레벨형(Leveled) 및 티어형(Tiered) LSM(Log-Structured Merge) 트리 설계, 적응형 그룹 커밋 기반 제로 할당 WAL(Write-Ahead Logging), 블록 캐시 교체 정책(W-TinyLFU, 2Q, Clock-Pro) 구현.
- **비동기 스토리지 & 커널 바이패스**: 사전 등록 고정 버퍼(`IORING_REGISTER_BUFFERS`), 제로카피 소켓 스플라이싱(`splice`), 정렬 블록 직접 전송(`O_DIRECT`)을 통한 리눅스 `io_uring` 기반 NVMe SSD 대역폭 한계 포화.
- **분산 합의 시스템**: Multi-Raft 상태 머신, 벡터 클럭 로그 압축, 시뮬레이션 기반 네트워크 단절 및 결함 주입을 통한 결정론적 크래시 복구 검증.
- **캐시 친화적 동시성 자료구조**: 락프리 스킵 리스트, 캐시라인 정렬 적응형 기수 트리(Adaptive Radix Tree, ART), 적응형 접두사 B+Tree, 메모리 안전 아레나 메모리 할당기 개발.

---

## 기술 스택 및 시스템 도구

### 핵심 프로그래밍 언어
| 언어 | 주력 도메인 | 경험 및 핵심 집중 영역 |
| :--- | :--- | :--- |
| **Go (1.23+)** | 분산 엔진, 합의 프로토콜 | 커스텀 메모리 아레나, 제로 할당 직렬화, 런타임 프로파일링 |
| **C++ (C++20 / C++23)** | 초저지연 스토리지 코어, LSM 트리 | Concepts, SIMD 내장 함수(AVX-512 / NEON), 캐시라인 정렬 |
| **Zig (0.13+)** | 시스템 유틸리티, `io_uring` 런타임 | 명시적 메모리 할당, 컴파일타임(comptime) 메타프로그래밍, C ABI 상호운용 |
| **Rust** | 동시성 자료구조, 고속 파서 | 락프리 동기화, 원자적 연산, 제로카피 와이어 디코더 |
| **Python** | 카오스 테스트 하네스, 벤치마크 | 결정론적 결함 주입 하네스, 처리량/지연시간 곡선 시각화 분석 |

### 스토리지 엔진 코어 & 알고리즘
| 도메인 | 아키텍처, 프리미티브 및 기법 |
| :--- | :--- |
| **LSM-Tree 내부 구조** | 레벨형 컴팩션 휴리스틱, SSTable 블록 포맷, 블록화 블룸 필터(Blocked Bloom Filters) |
| **B-Tree & 인덱싱** | 동시성 B+Tree 페이지 엔진, 적응형 기수 트리(ART), 트라이(Trie) 접두사 인덱스 |
| **캐시 교체 정책** | W-TinyLFU, 2Q (Two-Queue), Clock-Pro, 세그먼트 풀링 기반 LRU |
| **압축 & 인코딩** | Zstandard (zstd), Snappy, 가변길이 정수(Varint)/지그재그 인코딩, Roaring Bitmaps |

### 리눅스 스토리지 & 커널 프리미티브
| 카테고리 | 스택 및 커널 인터페이스 |
| :--- | :--- |
| **비동기 I/O** | Linux `io_uring` (SQPOLL, fixed buffers), POSIX AIO, epoll 이벤트 루프 |
| **파일 시스템 시맨틱** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **스토리지 프로토콜** | NVMe-oF (NVMe over Fabrics), PCIe 4.0/5.0 NVMe 블록 계층, SPDK 드라이버 |
| **프로파일링 & 추적** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, flamegraphs, Valgrind Massif |

### 테스트, 인프라 및 도구
| 카테고리 | 환경 및 툴체인 |
| :--- | :--- |
| **빌드 & 툴체인** | CMake, Ninja, Zig build system, Go toolchain, GCC / Clang 18+ |
| **결함 주입 & 카오스** | 결정론적 상태 머신 시뮬레이터, Jepsen 스타일 네트워크 단절 주입기 |
| **벤치마킹 스위트** | `fio` (Flexible I/O Tester), YCSB, 커스텀 WAL 부하 테스트 하네스 |
| **타깃 플랫폼** | Linux (Ubuntu LTS, Debian, Fedora Workstation), Alpine Linux 컨테이너 |

---

## 주요 오픈소스 프로젝트

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*제로 할당 읽기 경로를 탑재한 Go 기반 임베디드 락프리 LSM 스토리지 엔진.*
- 캐시라인 정렬 노드를 갖춘 락프리 스킵 리스트 기반 동시성 멤테이블(Memtable) 구현.
- NVMe 쓰기 증폭(Write Amplification)을 최소화하도록 설계된 적응형 티어드 컴팩션 탑재.
- 힙 할당 없이 오탐율을 0.8% 미만으로 억제하는 블록형 SIMD 블룸 필터 적용.
- crc32c 체크섬 검증과 벡터 블록 플러시를 적용한 추가 전용 WAL 기반 크래시 복구 보장.

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*네이티브 `io_uring` 기반 Zig로 작성된 고성능 유저스페이스 추가 전용 스토리지 엔진.*
- 고정 등록 버퍼(`IORING_REGISTER_BUFFERS`)와 전용 커널 제출 폴러(SQPOLL) 스레드만으로 동작.
- 소비자용 PCIe 4.0 NVMe 환경에서 지속 순차 쓰기 820,000 IOPS 이상 달성.
- 예측 가능한 4KB 페이지 정렬과 하드웨어 직결 제로카피 직렬화를 갖춘 유저스페이스 페이지 캐시 매니저.

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*분산 합의 상태 머신을 위한 결정론적 시뮬레이션 하네스.*
- 가상 비동기 네트워크 단절, 메시지 유실, 무작위 클럭 드리프트 환경에서 로그 복제 엔진을 검증.
- 단일 프로세스 내에서 가상 이산 시간을 단계별로 제어하여 수천 개의 가상 노드를 버그 재현 가능하게 실행.
- 다수의 Raft 및 Multi-Paxos 참조 구현체 상태 전이 검증 완료.

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*SQLite 쿼리를 NVMe SSD에 직접 라우팅하는 실험적 커스텀 가상 파일 시스템(VFS) 드라이버.*
- SPDK 유저스페이스 NVMe 드라이버를 통해 블록 범위에 직접 기록하여 OS 파일 시스템 오버헤드 제거.
- 고빈도 트랜잭션 워크로드에서 p99 트랜잭션 커밋 지연 시간을 42% 단축.

---

## 스토리지 엔진 불변식 & 엔지니어링 철칙

저수준 스토리지 루틴을 작성할 때 엄격히 고수하는 4대 철칙입니다:

```
[스토리지 엔진 메커니컬 불변식]
├── 1. 지속성(Durability)은 절대 타협 불가
│   └── fsync / fdatasync 배리어가 완료되기 전에는 동기 클라이언트에게 성공을 반환하지 않는다.
├── 2. 메모리 및 캐시라인 정렬 엄수
│   └── 모든 디스크 페이지 구조체는 반드시 4096바이트 경계에 엄격히 정렬한다 (O_DIRECT 호환).
├── 3. 핫 경로(Hot Path)의 락 경합 영구 배제
│   └── 읽기 및 추가(Append) 경로는 원자적 CAS와 시퀀스 포인터를 사용하여 완전한 락프리를 유지한다.
└── 4. 극한의 역경 테스트를 통한 무결성 입증
    └── 급작스러운 커널 패닉과 전원 차단 테스트를 통과하지 못한 스토리지는 완성된 것이 아니다.
```

---

## 하드웨어 및 벤치마킹 워크스테이션

- **스토리지 테스트베드 랙**: Dual AMD EPYC 7543, 256GB ECC DDR4-3200, 4x Solidigm P44 Pro 2TB PCIe 4.0 NVMe (소프트웨어 RAID0 / JBOD)
- **메인 워크스테이션**: Framework Laptop 16 (Linux Debian Sid / Wayland 환경)
- **입력 하드웨어**: Corne LP 분할형 기계식 키보드 (윤활 처리된 Gateron Low Profile Red 스위치, 커스텀 Colemak-DH 배열)
- **커피 스테이션**: 타임모어 Chestnut C3 핸드밀, 카펠랏 로봇(Cafelat Robot) 수동 레버 에스프레소 머신

---

## 소통 및 협업

- **GitHub Discussions**: 스토리지 아키텍처 및 내부 구조에 관한 기술적 토론은 언제든 저장소 이슈나 디스커션을 열어주세요.
- **이메일**: `mateo.silva.carvalho` [at] `proton.me`
- **GPG 키 핑거프린트**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
