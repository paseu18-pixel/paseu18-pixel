| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Mateo Silva-Carvalho

**Engenheiro de Armazenamento Distribuído & Internals de Bancos de Dados | Artesão de Sistemas**  
*Florianópolis, Brasil | UTC-3*

> *"E/S de disco é uma cena de crime. A amplificação de escrita (Write Amplification) é a culpada. B-trees e LSM-trees são as evidências periciais irrefutáveis."*

Projeto e implemento motores de armazenamento de bancos de dados de altíssimo rendimento, write-ahead logs (WAL) puramente incrementais (append-only) e infraestrutura de consenso distribuído. Minha rotina gira em torno de programação de sistemas Linux em baixo nível, E/S assíncrona com bypass de kernel (`io_uring`), estruturas de dados persistentes mapeadas em memória e caches de páginas concorrentes e livres de travas (lock-free) em Go moderno, C++20 e Zig.

Longe do terminal, costumo torrar café especial brasileiro de origem única (Catuaí e Bourbon Amarelo), praticar windsurf nas ondas do Atlântico no litoral catarinense ou soldar manualmente teclados mecânicos ergonômicos divididos (split).

---

## O Que Eu Faço

- **Motores de Armazenamento de Bancos de Dados**: Projeto de árvores LSM (Log-Structured Merge) niveladas (leveled) e em camadas (tiered), WAL com zero alocações e commit em grupo adaptativo, além de políticas de substituição de cache de blocos (W-TinyLFU, 2Q, Clock-Pro).
- **Armazenamento Assíncrono & Kernel-Bypass**: Saturação máxima de largura de banda de SSDs NVMe utilizando Linux `io_uring` com buffers pré-registrados (`IORING_REGISTER_BUFFERS`), splicing de sockets com zero cópias (`splice`) e transferências diretas em blocos alinhados (`O_DIRECT`).
- **Sistemas de Consenso Distribuído**: Implementação de máquinas de estado Multi-Raft, compactação de logs por relógios vetoriais e testes determinísticos de recuperação após desastres com injeção de partições de rede.
- **Estruturas de Dados Concorrentes para Cache**: Desenvolvimento de skip lists livres de travas, árvores radix adaptativas (ART) alinhadas a linhas de cache, árvores B+ com prefixo adaptativo e alocadores de arena seguros.

---

## Stack Tecnológico & Ferramentas de Sistemas

### Principais Linguagens de Programação
| Linguagem | Domínio Principal | Experiência e Foco |
| :--- | :--- | :--- |
| **Go (1.23+)** | Motores distribuídos, protocolos de consenso | Arenas customizadas de memória, serialização zero-alloc, profiling de runtime |
| **C++ (C++20 / C++23)** | Núcleos de armazenamento de baixa latência, LSM | Concepts, intrínsecos SIMD (AVX-512 / NEON), alinhamento de cacheline |
| **Zig (0.13+)** | Utilitários de sistema, runtimes `io_uring` | Alocação explícita de memória, metaprogramação comptime, interoperabilidade C ABI |
| **Rust** | Estruturas concorrentes, decodificadores seguros | Sincronização lock-free, operações atômicas, decodificadores de rede zero-copy |
| **Python** | Bancadas de teste de caos, benchmarks | Injeção determinística de falhas, plotagem e análise de curvas de latência e vazão |

### Algoritmos & Internals de Armazenamento
| Domínio | Arquiteturas, Primitivas e Métodos |
| :--- | :--- |
| **Internals de Árvores LSM** | Heurísticas de compactação nivelada, formatos de bloco SSTable, Blocked Bloom Filters |
| **Árvores B & Indexação** | Motores de páginas B+Tree concorrentes, Adaptive Radix Trees (ART), índices trie de prefixo |
| **Políticas de Despejo de Cache** | W-TinyLFU, 2Q (Two-Queue), Clock-Pro, LRU com pool segmentado |
| **Compressão & Codificação** | Zstandard (zstd), Snappy, codificação Varint / ZigZag, Roaring Bitmaps |

### Primitivas de Kernel & Armazenamento Linux
| Categoria | Stack e Interfaces de Kernel |
| :--- | :--- |
| **E/S Assíncrona** | Linux `io_uring` (polling SQPOLL, buffers fixos), POSIX AIO, loops de eventos epoll |
| **Semântica de Sistemas de Arquivos** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **Protocolos de Armazenamento** | NVMe-oF (NVMe sobre Fabrics), camadas de blocos PCIe 4.0/5.0 NVMe, drivers SPDK |
| **Perfilamento & Rastreamento** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, flamegraphs, Valgrind Massif |

### Testes, Infraestrutura & Ferramentas
| Categoria | Ambiente e Cadeia de Ferramentas |
| :--- | :--- |
| **Compilação & Build** | CMake, Ninja, sistema de build Zig, toolchain Go, GCC / Clang 18+ |
| **Injeção de Falhas & Engenharia do Caos** | Simuladores determinísticos de máquina de estados, injetores de partição de rede estilo Jepsen |
| **Suítes de Benchmark** | `fio` (Flexible I/O Tester), YCSB, bancadas próprias de estresse de WAL |
| **Plataformas-Alvo** | Linux (Ubuntu LTS, Debian, Fedora Workstation), contêineres Alpine Linux |

---

## Principais Projetos de Código Aberto

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*Motor de armazenamento LSM embarcado e livre de travas escrito em Go com caminhos de leitura sem alocações.*
- Memtables concorrentes implementadas com skip lists lock-free e nós alinhados a linhas de cache.
- Compactação adaptativa por camadas projetada para mitigar o desgaste e a amplificação de escrita em NVMe.
- Filtros de Bloom SIMD em blocos que mantêm a taxa de falso-positivos abaixo de 0,8% com zero alocação no heap.
- Recuperação contra falhas garantida por WAL append-only com somas de verificação crc32c e descargas vetorizadas de blocos.

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*Motor de armazenamento puramente incremental em espaço do usuário construído em Zig sobre `io_uring` nativo.*
- Opera integralmente sobre buffers registrados fixos (`IORING_REGISTER_BUFFERS`) e threads dedicadas de polling no kernel (SQPOLL).
- Ultrapassa 820.000 IOPS contínuos de escrita sequencial em SSDs NVMe PCIe 4.0 comerciais.
- Gerenciador de cache de páginas em userspace com alinhamento rigoroso a páginas de 4KB e serialização direta sem cópias.

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*Bancada de simulação determinística para máquinas de estados de consenso distribuído.*
- Submete motores de replicação de logs a partições de rede assíncronas, perdas de pacotes e desvios de relógio emulado.
- Executa milhares de nós virtuais em um único processo com avanço de tempo discreto para reproduzir falhas de forma idêntica.
- Validação bem-sucedida de transições de estado em implementações de referência de Raft e Multi-Paxos.

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*Driver experimental de Virtual File System (VFS) para SQLite roteando consultas diretamente a SSDs NVMe.*
- Elimina a camada do sistema de arquivos do sistema operacional gravando direto em intervalos de blocos com drivers SPDK.
- Reduz a latência de commit de transações no percentil 99 (p99) em 42% sob alta concorrência de escritas.

---

## Invariantes de Engenharia de Armazenamento (Código de Honra)

Ao implementar rotinas de armazenamento de baixo nível, sigo impreterivelmente estes quatro pilares:

```
[Invariantes Mecânicos do Motor de Armazenamento]
├── 1. Durabilidade é Inegociável
│   └── Jamais retorne sucesso a um cliente síncrono antes que a barreira fsync / fdatasync seja completada.
├── 2. Respeito Rigoroso ao Alinhamento de Memória e Linhas de Cache
│   └── Toda estrutura de página em disco deve estar rigorosamente alinhada a 4096 bytes (compatível com O_DIRECT).
├── 3. Eliminação Total de Disputa por Travas em Hot Paths
│   └── Caminhos de leitura e append devem permanecer 100% livres de travas usando CAS atômico e ponteiros de sequência.
└── 4. Robustez Provada em Cenários Extremos
    └── Um motor de armazenamento só é confiável após sobreviver a pânicos de kernel repentinos e testes de corte de energia.
```

---

## Laboratório de Hardware & Bancada de Testes

- **Rack de Testes de Armazenamento**: Dual AMD EPYC 7543, 256GB ECC DDR4-3200, 4x Solidigm P44 Pro 2TB PCIe 4.0 NVMe (software RAID0 / JBOD)
- **Computador Principal de Trabalho**: Framework Laptop 16 (Linux Debian Sid / Wayland)
- **Dispositivos de Entrada**: Teclado mecânico dividido Corne LP (switches Gateron Low Profile Red lubrificados, layout Colemak-DH)
- **Estação de Café**: Moedor manual Timemore Chestnut C3, prensa manual de alavanca Flair 58

---

## Contato & Colaboração

- **GitHub Discussions**: Para conversas sobre arquitetura de armazenamento e detalhes de sistemas, sinta-se à vontade para abrir uma issue ou discussão nos repositórios.
- **E-mail**: `mateo.silva.carvalho` [arroba] `proton.me`
- **Fingerprint da Chave GPG**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
