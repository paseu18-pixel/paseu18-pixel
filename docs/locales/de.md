| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Mateo Silva-Carvalho

**Ingenieur für Verteilte Speichersysteme & Datenbankinterna | Systemhandwerker**  
*Florianópolis, Brasilien | UTC-3*

> *„Platten-I/O ist ein Tatort. Schreibverstärkung (Write Amplification) ist der Täter. B-Bäume und LSM-Bäume sind die forensischen Beweise.“*

Ich entwickle hochdurchsatzfähige Speicher-Engines für Datenbanken, reine Anfügeprotokolle (Append-Only Write-Ahead-Logs) und Infrastrukturen für verteilten Konsens. Mein Arbeitsalltag dreht sich um hardwarenahe Linux-Systemprogrammierung, kernel-bypassende asynchrone I/O (`io_uring`), speicherabgebildete persistente Datenstrukturen und sperrenfreie (lock-free) seitenbasierte Caches in modernem Go, C++20 und Zig.

Abseits des Terminals röste ich brasilianischen Single-Origin-Spezialitätenkaffee (Catuaí und Yellow Bourbon), gehe an den Stränden von Santa Catarina im Atlantik windsurfen oder löte von Hand maßgeschneiderte ergonomische Split-Tastaturen.

---

## Kernbereiche & Schwerpunkte

- **Datenbank-Speicher-Engines**: Entwurf von stufenweisen (leveled) und geschichteten (tiered) LSM-Bäumen (Log-Structured Merge), allokationsfreien Write-Ahead-Logs (WAL) mit adaptiven Gruppen-Commits sowie Block-Cache-Verdrängungsstrategien (W-TinyLFU, 2Q, Clock-Pro).
- **Asynchrone I/O & Kernel-Bypass**: Maximale Ausschöpfung von NVMe-SSDs über Linux `io_uring` mit registrierten Festpuffern (`IORING_REGISTER_BUFFERS`), Zero-Copy-Socket-Spleißen (`splice`) und direkt ausgerichteten Blockübertragungen (`O_DIRECT`).
- **Verteilte Konsenssysteme**: Implementierung von Multi-Raft-Zustandsmaschinen, Vektoruhr-Log-Kompaktierung und deterministische Ausfallsicherheitsprüfungen durch simulierte Netzwerkpartitionen und Fehlereinspeisung.
- **Cache-optimierte Datenstrukturen**: Entwicklung sperrenfreier Skip-Listen, an Cache-Zeilen ausgerichteter adaptiver Radix-Bäume (ART), adaptiver Präfix-B+Bäume und speichersicherer Arena-Allokatoren.

---

## Technologiestack & Systemwerkzeuge

### Zentrale Programmiersprachen
| Sprache | Hauptdomäne | Erfahrung & Schwerpunkt |
| :--- | :--- | :--- |
| **Go (1.23+)** | Verteilte Engines, Konsensprotokolle | Eigene Speicher-Arenen, allokationsfreie Serialisierung, Laufzeitprofilierung |
| **C++ (C++20 / C++23)** | Latenzarme Speicherkerne, LSM-Bäume | Concepts, SIMD-Intrinsiken (AVX-512 / NEON), Ausrichtung an Cache-Zeilen |
| **Zig (0.13+)** | Systemwerkzeuge, `io_uring`-Laufzeiten | Explizite Speicherverwaltung, Comptime-Metaprogrammierung, C-ABI-Interoperabilität |
| **Rust** | Nebenläufige Strukturen, sichere Parser | Sperrenfreie Synchronisation, atomare Operationen, Zero-Copy-Netzwerkdecoder |
| **Python** | Chaos-Testumgebungen, Benchmarks | Deterministische Fehlereinspeisung, Visualisierung von Durchsatz- und Latenzkurven |

### Speicher-Engine-Interna & Algorithmen
| Domäne | Architekturen, Primitive & Methoden |
| :--- | :--- |
| **LSM-Baum-Interna** | Stufenweise Kompaktierungsheuristiken, SSTable-Blockformate, Blocked Bloom Filter |
| **B-Bäume & Indizierung** | Nebenläufige B+Baum-Seiten-Engines, Adaptive Radix Trees (ART), Trie-Präfix-Indizes |
| **Cache-Verdrängungsstrategien** | W-TinyLFU, 2Q (Two-Queue), Clock-Pro, LRU mit Segment-Pooling |
| **Kompression & Kodierung** | Zstandard (zstd), Snappy, Varint- / ZigZag-Kodierung, Roaring Bitmaps |

### Linux-Speicher & Kernel-Primitive
| Kategorie | Stack & Kernel-Schnittstellen |
| :--- | :--- |
| **Asynchrone I/O** | Linux `io_uring` (SQPOLL, registrierte Puffer), POSIX AIO, epoll-Ereignisschleifen |
| **Dateisystem-Semantik** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **Speicherprotokolle** | NVMe-oF (NVMe over Fabrics), PCIe 4.0/5.0 NVMe-Blockschichten, SPDK-Treiber |
| **Profilierung & Ablaufverfolgung** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, Flamegraphs, Valgrind Massif |

### Testinfrastruktur & Werkzeuge
| Kategorie | Umgebung & Toolchain |
| :--- | :--- |
| **Build-Systeme** | CMake, Ninja, Zig-Build-System, Go-Toolchain, GCC / Clang 18+ |
| **Fehlerinjektion & Chaos-Engineering** | Deterministische Zustandsmaschinensimulatoren, Jepsen-artige Netzwerktrennungs-Injektoren |
| **Benchmark-Suiten** | `fio` (Flexible I/O Tester), YCSB, maßgeschneiderte WAL-Stresstest-Umgebungen |
| **Zielplattformen** | Linux (Ubuntu LTS, Debian, Fedora Workstation), Alpine-Linux-Container |

---

## Ausgewählte Open-Source-Projekte

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*In Go geschriebene, eingebettete und sperrenfreie LSM-Speicher-Engine mit allokationsfreien Lesepfaden.*
- Implementiert nebenläufige Memtables mittels sperrenfreier Skip-Listen mit an Cache-Zeilen ausgerichteten Knoten.
- Bietet adaptive, geschichtete Kompaktierung zur drastischen Reduzierung der NVMe-Schreibverstärkung.
- Verwendet blockbasierte SIMD-Bloom-Filter, die Fehlalarmraten ohne Heap-Allokationen unter 0,8 % halten.
- Garantiert Ausfallsicherheit durch ein reines Anfüge-WAL mit crc32c-Prüfsummen und vektorisiertem Block-Flushing.

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*Hochleistungsfähige Append-Only-Speicher-Engine im Userspace, entwickelt in Zig auf Basis von nativem `io_uring`.*
- Arbeitet vollständig mit vorregistrierten Puffern (`IORING_REGISTER_BUFFERS`) und dedizierten Kernel-Polling-Threads (SQPOLL).
- Erzielt über 820.000 kontinuierliche sequentielle Schreib-IOPS auf PCIe 4.0 NVMe-Hardware für Endanwender.
- Userspace-Page-Cache-Manager mit deterministischer 4-KB-Seiten-Ausrichtung und direkter Zero-Copy-Serialisierung.

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*Eine deterministische Simulationsumgebung für verteilte Konsens-Zustandsmaschinen.*
- Überprüft Protokollreplikations-Engines unter simulierten asynchronen Netzwerkpartitionen, Paketverlusten und Taktdrifts.
- Führt Tausende virtueller Knoten in einem einzigen Prozess mit diskreten Zeitschritten zur exakten Fehlerreproduktion aus.
- Zustandsübergänge für mehrere Raft- und Multi-Paxos-Referenzimplementierungen erfolgreich verifiziert.

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*Experimenteller SQLite-VFS-Treiber (Virtual File System), der SQL-Abfragen direkt an NVMe-SSDs weiterleitet.*
- Umgeht das Dateisystem des Betriebssystems durch direktes Schreiben in Blockbereiche über SPDK-NVMe-Treiber im Userspace.
- Senkt die p99-Transaktions-Commit-Latenz bei hochfrequenten Schreiblasten um 42 %.

---

## Invarianten der Speicherentwicklung (Ehrenkodex)

Beim Schreiben hardwarenaher Speichercodes gelten diese vier Prinzipien uneingeschränkt:

```
[Mechanische Invarianten der Speicher-Engine]
├── 1. Dauerhaftigkeit (Durability) ist nicht verhandelbar
│   └── Einem synchronen Client wird niemals Erfolg gemeldet, bevor die fsync / fdatasync-Barriere vollendet ist.
├── 2. Konsequente Speicher- und Cache-Zeilen-Ausrichtung
│   └── Alle Platten-Seitenstrukturen müssen strikt an 4096-Byte-Grenzen ausgerichtet sein (O_DIRECT-konform).
├── 3. Vermeidung von Sperrenkonflikten auf kritischen Pfaden
│   └── Lese- und Anfügepfade müssen durch atomare CAS-Operationen und Sequenzzeiger vollständig sperrenfrei bleiben.
└── 4. Bewährung unter extremen Bedingungen
    └── Ein Speichersystem ist erst dann vertrauenswürdig, wenn es simulierte Kernel-Panics und Stromausfälle übersteht.
```

---

## Hardware- & Test-Labor

- **Speicherprüfstand-Rack**: Dual AMD EPYC 7543, 256GB ECC DDR4-3200, 4x Solidigm P44 Pro 2TB PCIe 4.0 NVMe (Software-RAID0 / JBOD)
- **Haupt-Arbeitsgerät**: Framework Laptop 16 (Linux Debian Sid / Wayland)
- **Eingabegeräte**: Corne LP geteilte mechanische Tastatur (geschmierte Gateron Low Profile Red Switches, Colemak-DH-Tastenbelegung)
- **Kaffee-Ecke**: Timemore Chestnut C3 Handmühle, Cafelat Robot manuelle Handhebel-Espressomaschine

---

## Kontakt & Austausch

- **GitHub Discussions**: Für fachliche Diskussionen über Speicherarchitekturen und Systeminterna nutzen Sie gern die Issues oder Discussions in meinen Repositories.
- **E-Mail**: `mateo.silva.carvalho` [at] `proton.me`
- **GPG-Schlüsselfingerabdruck**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
