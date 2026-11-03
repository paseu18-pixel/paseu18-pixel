| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Mateo Silva-Carvalho

**Ingeniero de Almacenamiento Distribuido e Internos de Bases de Datos | Artesano de Sistemas**  
*Florianópolis, Brasil | UTC-3*

> *"La E/S de disco es la escena del crimen. La amplificación de escritura es el autor material. Los árboles B y los árboles LSM son las pruebas forenses irrefutables."*

Diseño y construyo motores de almacenamiento de bases de datos de alto rendimiento, registros de escritura anticipada (WAL) basados en anexado continuo (append-only) e infraestructura de consenso distribuido. Mi labor cotidiana se enfoca en programación de sistemas Linux de bajo nivel, E/S asíncrona sin intervención del kernel (`io_uring`), estructuras de datos persistentes con mapeo en memoria y cachés de páginas concurrentes sin bloqueos (lock-free) en Go moderno, C++20 y Zig.

Fuera de la terminal, me dedico a tostar café de especialidad brasileño de origen único (Catuaí y Bourbon Amarillo), a practicar windsurf en las olas del Atlántico en las costas de Santa Catarina o a soldar a mano teclados mecánicos ergonómicos divididos personalizados.

---

## Especialización y Áreas de Trabajo

- **Motores de Almacenamiento**: Diseño de árboles LSM (Log-Structured Merge) escalonados (leveled) y por niveles (tiered), registros WAL de asignación cero con confirmación grupal adaptativa (group commit) y políticas de desalojo de caché de bloques (W-TinyLFU, 2Q, Clock-Pro).
- **Almacenamiento Asíncrono y Kernel-Bypass**: Saturación de ancho de banda en unidades SSD NVMe mediante Linux `io_uring` con búferes fijos registrados (`IORING_REGISTER_BUFFERS`), empalme de sockets de copia cero (`splice`) y transferencias directas de bloques alineados (`O_DIRECT`).
- **Sistemas de Consenso Distribuido**: Implementación de máquinas de estados Multi-Raft, compactación de registros mediante relojes vectoriales y pruebas deterministas de recuperación ante fallos con inyección de particiones de red.
- **Estructuras de Datos Concurrentes para Caché**: Desarrollo de listas por salto (skip lists) sin bloqueos, árboles radix adaptativos (ART) alineados a líneas de caché, árboles B+ con prefijo adaptativo y asignadores de memoria tipo arena.

---

## Pila Tecnológica y Herramientas de Sistemas

### Lenguajes de Programación Principales
| Lenguaje | Dominio Principal | Enfoque y Experiencia |
| :--- | :--- | :--- |
| **Go (1.23+)** | Motores distribuidos, protocolos de consenso | Arenas de memoria a medida, serialización sin asignaciones, perfilado en tiempo de ejecución |
| **C++ (C++20 / C++23)** | Núcleo de almacenamiento de baja latencia, LSM | Concepts, intrínsecos SIMD (AVX-512 / NEON), alineación a líneas de caché |
| **Zig (0.13+)** | Utilidades de sistema, entornos `io_uring` | Asignación de memoria explícita, metaprogramación comptime, interoperabilidad ABI de C |
| **Rust** | Estructuras concurrentes, decodificadores seguros | Sincronización sin bloqueos, operaciones atómicas, decodificadores de protocolo sin copia |
| **Python** | Marcos de prueba de caos, benchmarks | Pruebas de inyección de fallos deterministas, análisis y gráficos de latencia/rendimiento |

### Algoritmos e Internos de Almacenamiento
| Dominio | Arquitecturas, Primitivas y Métodos |
| :--- | :--- |
| **Estructura Interna de LSM** | Heurística de compactación por niveles, formatos de bloque SSTable, filtros de Bloom en bloque |
| **Árboles B e Indexación** | Motores de páginas B+Tree concurrentes, árboles radix adaptativos (ART), índices de prefijo Trie |
| **Políticas de Desalojo de Caché** | W-TinyLFU, 2Q (Two-Queue), Clock-Pro, LRU con segmentación de fondos (pools) |
| **Compresión y Codificación** | Zstandard (zstd), Snappy, codificación Varint / ZigZag, mapas de bits Roaring |

### Primitivas de Kernel y Almacenamiento en Linux
| Categoría | Pila e Interfaces de Kernel |
| :--- | :--- |
| **E/S Asíncrona** | Linux `io_uring` (sondeo SQPOLL, búferes fijos), POSIX AIO, bucles de eventos epoll |
| **Semántica del Sistema de Archivos** | `O_DIRECT`, `fallocate`, `fdatasync`, `sync_file_range`, `mmap` / `madvise` |
| **Protocolos de Almacenamiento** | NVMe-oF (NVMe sobre Fabrics), capas de bloques PCIe 4.0/5.0 NVMe, controladores SPDK |
| **Perfilado y Trazado** | `perf`, eBPF (`bpftrace`), Linux `ftrace`, flamegraphs, Valgrind Massif |

### Pruebas, Infraestructura y Herramientas
| Categoría | Entorno y Cadena de Herramientas |
| :--- | :--- |
| **Sistemas de Construcción** | CMake, Ninja, Zig build system, Go toolchain, GCC / Clang 18+ |
| **Ingeniería del Caos** | Simuladores deterministas de máquinas de estado, inyectores de partición de red estilo Jepsen |
| **Bancos de Pruebas** | `fio` (Flexible I/O Tester), YCSB, entornos propios de prueba de estrés para WAL |
| **Plataformas de Destino** | Linux (Ubuntu LTS, Debian, Fedora Workstation), contenedores Alpine Linux |

---

## Proyectos Destacados de Código Abierto

### [ouroboros-kv](https://github.com/mateo-silva/ouroboros-kv)
*Motor de almacenamiento LSM embebido y sin bloqueos escrito en Go con rutas de lectura de asignación cero.*
- Memtables concurrentes implementadas mediante listas de salto sin bloqueos alineadas a la línea de caché.
- Compactación escalonada adaptativa creada específicamente para minimizar la amplificación de escritura en unidades NVMe.
- Filtros de Bloom SIMD bloqueados que mantienen la tasa de falsos positivos por debajo del 0.8% sin asignaciones de memoria dinámica.
- Recuperación ante caídas mediante WAL de anexado con validación de suma de comprobación crc32c y volcado vectorial de bloques.

### [ring-fs](https://github.com/mateo-silva/ring-fs)
*Motor de almacenamiento append-only en espacio de usuario desarrollado en Zig con `io_uring` nativo.*
- Opera íntegramente sobre búferes fijos registrados (`IORING_REGISTER_BUFFERS`) e hilos dedicados de sondeo en el kernel (SQPOLL).
- Alcanza más de 820.000 IOPS sostenidas de escritura secuencial en unidades NVMe PCIe 4.0 de gama comercial.
- Administrador de caché de páginas en espacio de usuario con alineación predecible de páginas de 4KB y serialización de copia cero directa.

### [paxos-forge](https://github.com/mateo-silva/paxos-forge)
*Plataforma de simulación determinista para máquinas de estado de consenso distribuido.*
- Pone a prueba motores de replicación de registros frente a particiones de red asíncronas, pérdida de paquetes y desvío de relojes.
- Ejecuta miles de nodos virtuales en un solo proceso mediante avance por pasos temporales discretos para reproducir errores de forma fiable.
- Verificación exhaustiva de transiciones de estado en implementaciones de referencia de Raft y Multi-Paxos.

### [vfs-spdk](https://github.com/mateo-silva/vfs-spdk)
*Controlador experimental de Virtual File System (VFS) para SQLite que envía consultas directamente a unidades SSD NVMe.*
- Suprime la sobrecarga del sistema de archivos del SO escribiendo directamente a rangos de bloques con controladores NVMe SPDK.
- Reduce la latencia de confirmación de transacciones en el percentil p99 en un 42% en cargas intensivas.

---

## Principios Mecánicos e Invariantes de Almacenamiento

Al programar componentes de almacenamiento de bajo nivel, sigo rigurosamente estas cuatro reglas inquebrantables:

```
[Invariantes Mecánicas del Motor de Almacenamiento]
├── 1. La durabilidad no es negociable
│   └── Nunca devolver éxito a un cliente síncrono antes de completar la barrera fsync / fdatasync.
├── 2. Respetar estrictamente la alineación de memoria y caché
│   └── Cada estructura de página en disco debe estar rigurosamente alineada a 4096 bytes (apto para O_DIRECT).
├── 3. Eliminar la contención de bloqueos en las rutas críticas
│   └── Las rutas de lectura y anexado deben permanecer completamente libres de bloqueos mediante CAS atómico y punteros de secuencia.
└── 4. Demostrar la resiliencia bajo condiciones extremas
    └── Un motor de almacenamiento solo es válido si supera pruebas de pánico de kernel y cortes repentinos de energía.
```

---

## Banco de Pruebas y Estación de Trabajo

- **Bastidor de Pruebas de Almacenamiento**: Doble procesador AMD EPYC 7543, 256GB ECC DDR4-3200, 4 unidades Solidigm P44 Pro 2TB PCIe 4.0 NVMe (software RAID0 / JBOD)
- **Portátil de Trabajo Principal**: Framework Laptop 16 (Linux Debian Sid / Wayland)
- **Periféricos de Entrada**: Teclado mecánico dividido Corne LP (interruptores lubricados Gateron Low Profile Red, distribución personalizada Colemak-DH)
- **Rincón de Café**: Molinillo manual Timemore Chestnut C3, cafetera manual de palanca Cafelat Robot

---

## Contacto y Colaboración

- **GitHub Discussions**: Para conversar sobre arquitectura de almacenamiento e internos de sistemas, bienvenido a abrir un issue o debate en mis repositorios.
- **Correo Electrónico**: `mateo.silva.carvalho` [en] `proton.me`
- **Huella de Clave GPG**: `9F8A 4C1D 2E5B 8A73 01D4 6B2E 5A8C 3F19 4E27 91B0`
