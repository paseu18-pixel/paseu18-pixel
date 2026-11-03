| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Mateo Silva-Carvalho

**Ingénieur Stockage Distribué & Moteurs de Bases de Données | Artisan Systèmes**  
*Florianópolis, Brésil | UTC-3*

> *« Les E/S disque sont une scène de crime. L'amplification d'écriture en est l'auteur. Les arbres B et les arbres LSM constituent les preuves médico-légales. »*

Je conçois et développe des moteurs de stockage de bases de données à très haut débit, des journaux d'écriture anticipée (WAL) en ajout seul (append-only), ainsi que des infrastructures de consensus distribué. Mes travaux quotidiens s'articulent autour de la programmation système Linux bas niveau, des E/S asynchrones avec contournement du noyau (`io_uring`), des structures de données persistantes projetées en mémoire et des caches de pages concurrents sans verrou (lock-free) en Go moderne, C++20 et Zig.

Loin des écrans, je me consacre à la torréfaction artisanale de cafés brésiliens de spécialité d'origine unique (Catuaí et Bourbon Jaune), à la planche à voile sur les vagues atlantiques de Santa Catarina, et à la soudure manuelle de claviers mécaniques ergonomiques scindés personnalisés.

---

## Domaines d'Intervention et Spécialisations

- **Moteurs de Stockage**: Conception d'arbres LSM (Log-Structured Merge) nivelés (leveled) et par paliers (tiered), de journaux WAL à zéro allocation avec validation groupée adaptative, et d'algorithmes d'éviction de cache de blocs (W-TinyLFU, 2Q, Clock-Pro).
- **Stockage Asynchrone & Kernel-Bypass**: Saturation de la bande passante des SSD NVMe via Linux `io_uring` avec tampons enregistrés (`IORING_REGISTER_BUFFERS`), épissurage de sockets sans copie (`splice`) et transferts directs de blocs alignés (`O_DIRECT`).
- **Systèmes de Consensus Distribué**: Implémentation de machines à états Multi-Raft, compaction de journaux par horloges vectorielles et tests déterministes de reprise après sinistre par injection de fautes et de partitions réseau.
- **Structures de Données pour Caches**: Écriture de listes à sauts (skip lists) sans verrou, d'arbres radix adaptatifs (ART) alignés sur les lignes de cache, d'arbres B+ à préfixe adaptatif et d'allocateurs de mémoire par arène (arena).

---

## Stack Technique & Outillage Système

### Langages de Programmation Fondamentaux
| Langage | Domaine Principal | Expérience & Priorités |
| :--- | :--- | :--- |
| **Go (1.23+)** | Moteurs distribués, protocoles de consensus | Arènes mémoire sur mesure, sérialisation zéro allocation, profilage d'exécution |
| **C++ (C++20 / C++23)** | Cœur de stockage à faible latence, LSM | Concepts, intrinsèques SIMD (AVX-512 / NEON), alignement sur lignes de cache |
| **Zig (0.13+)** | Utilitaires système, exécuteurs `io_uring` | Gestion explicite de la mémoire, métaprogrammation comptime, interopérabilité C ABI |
| **Rust** | Structures concurrentes, analyseurs sécurisés | Primitives sans verrou, opérations atomiques, décodeurs réseau zéro copie |
| **Python** | Bancs de test de chaos, benchmarks | Cadres d'injection de pannes déterministes, tracé de courbes débit/latence |

### Architecture Interne & Algorithmes de Stockage
| Domaine | Architectures, Primitives & Méthodes |
| :--- | :--- |
| **Architecture Interne LSM** | Heuristiques de compaction nivelée, formats de blocs SSTable, filtres de Bloom par blocs |
| **Arbres B & Indexation** | Moteurs de pages B+Tree concurrents, arbres radix adaptatifs (ART), index par préfixes Trie |
| **Politiques d'Éviction de Cache** | W-TinyLFU, 2Q (Two-Queue), Clock-Pro, LRU avec regroupement par segments |
| **Compression & Encodage** | Zstandard (zstd), Snappy, encodage Varint / ZigZag, bitmaps Roaring |

### Primitives Noyau & Stockage Linux
| Catégorie | Stack & Interfaces Noyau |
| :--- | :--- |
| **E/S Asynchrones** | Linux `io_uring` (polling SQPOLL, tampons fixes), POSIX AIO, boucles d'événements epoll |
| **Sémantique Système de Fichiers** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **Protocoles de Stockage** | NVMe-oF (NVMe sur Fabrics), couches de blocs PCIe 4.0/5.0 NVMe, pilotes SPDK |
| **Profilage & Traçage** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, flamegraphs, Valgrind Massif |

### Tests, Infrastructure & Outils
| Catégorie | Environnement & Chaîne de Compilation |
| :--- | :--- |
| **Outils de Compilation** | CMake, Ninja, système de build Zig, chaîne Go, GCC / Clang 18+ |
| **Injection de Fautes & Chaos** | Simulateurs déterministes de machines d'états, injecteurs de partitions réseau style Jepsen |
| **Suites de Bancs d'Essai** | `fio` (Flexible I/O Tester), YCSB, bancs d'épreuve de charge WAL sur mesure |
| **Plateformes Cibles** | Linux (Ubuntu LTS, Debian, Fedora Workstation), conteneurs Alpine Linux |

---

## Projets Open-Source Majeurs

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*Moteur de stockage LSM embarqué et sans verrou écrit en Go avec chemins de lecture à zéro allocation.*
- Memtables concurrentes implémentées à l'aide de listes à sauts sans verrou alignées sur les lignes de cache.
- Compaction par paliers adaptative conçue pour réduire au strict minimum l'amplification d'écriture sur NVMe.
- Filtres de Bloom SIMD par blocs garantissant un taux de faux positifs sous 0,8 % sans allocation sur le tas.
- Reprise après crash via un WAL en ajout seul avec validation par somme de contrôle crc32c et vidage vectorisé de blocs.

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*Moteur de stockage append-only en espace utilisateur conçu en Zig avec `io_uring` natif.*
- Fonctionne intégralement sur des tampons fixes pré-enregistrés (`IORING_REGISTER_BUFFERS`) et des threads de scrutation noyau (SQPOLL).
- Dépasse les 820 000 IOPS soutenues en écriture séquentielle sur disque NVMe PCIe 4.0 grand public.
- Gestionnaire de cache de pages en espace utilisateur avec alignement prévisible sur 4 Ko et sérialisation directe sans copie.

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*Banc de simulation déterministe pour machines d'états de consensus distribué.*
- Éprouve les moteurs de réplication de journaux face à des partitions asynchrones, des pertes de paquets et des dérives d'horloge.
- Exécute des milliers de nœuds virtuels dans un seul processus grâce à un ordonnancement temporel discret reproductible.
- Validation des transitions d'états sur de multiples implémentations de référence Raft et Multi-Paxos.

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*Pilote Virtual File System (VFS) expérimental pour SQLite routant les requêtes directement vers des SSD NVMe.*
- Supprime la couche du système de fichiers de l'OS en écrivant directement dans des blocs via les pilotes SPDK NVMe.
- Diminue de 42 % la latence de commit de transaction au 99e percentile sous fortes charges.

---

## Invariants Techniques & Code d'Honneur

Lors de l'implémentation de composants de stockage bas niveau, quatre impératifs sont scrupuleusement respectés :

```
[Invariants Mécaniques du Moteur de Stockage]
├── 1. La durabilité n'est pas négociable
│   └── Ne jamais notifier un succès à un client synchrone avant l'exécution de la barrière fsync / fdatasync.
├── 2. Respect absolu de l'alignement mémoire et cache
│   └── Chaque structure de page sur disque doit être rigoureusement alignée sur 4096 octets (compatible O_DIRECT).
├── 3. Bannissement de la contention de verrous sur les chemins chauds
│   └── Les chemins de lecture et d'ajout doivent rester strictement sans verrou grâce aux opérations CAS et pointeurs de séquence.
└── 4. Preuve de robustesse sous stress extrême
    └── Un moteur de stockage n'est viable que s'il survit à des crashs de noyau inopinés et à des coupures de courant brutales.
```

---

## Station de Travail & Laboratoire d'Essais

- **Baie de Tests Stockage**: Bi-processeur AMD EPYC 7543, 256 Go ECC DDR4-3200, 4x Solidigm P44 Pro 2 To PCIe 4.0 NVMe (software RAID0 / JBOD)
- **Station Mobile Principale**: Framework Laptop 16 (Linux Debian Sid / Wayland)
- **Périphériques de Saisie**: Clavier mécanique scindé Corne LP (switches Gateron Low Profile Red lubrifiés, disposition sur mesure Colemak-DH)
- **Coin Café**: Moulin manuel Timemore Chestnut C3, machine expresso manuelle à levier Flair 58

---

## Échanges & Collaborations

- **GitHub Discussions**: Pour échanger sur l'architecture de stockage ou les détails d'implémentation système, n'hésitez pas à ouvrir une discussion sur mes dépôts.
- **Courriel**: `mateo.silva.carvalho` [arobase] `proton.me`
- **Empreinte de Clé GPG**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
