# Glossary

## A

### AMBA

 Principally an ARM term, [Advanced Microcontroller Bus Architecture
 (AMBA)](https://developer.arm.com/Architectures/AMBA) for the connection and
 management of functional blocks in a System-on-Chip (SoC).

### AXI

 Principally an ARM term, [Advanced eXtensible
 Interface](https://developer.arm.com/documentation/102202/0300/AXI-protocol-overview).
 There is also the AXI Coherency Extensions (ACE)

## B

### Backend

 The part of the CPU pipeline responsible for out-of-order execution of micro-operations (uops), accessing data in memory/caches, and retiring completed instructions.

### Beat
 A beat is the smallest single unit of data transfer within an [AXI](#axi)
 transaction. Its size is determined by the bus width. A Burst can be comprised
 of multiple beats. See the [Arm
 Glossary](https://developer.arm.com/documentation/105565/0200/B)

### BIOS

 [Basic Input/Output System](https://en.wikipedia.org/wiki/BIOS).

### BPF
 [Berkeley Packet Filter](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter). Now often referring to eBPF, a technology that allows running sandboxed programs in the Linux kernel for advanced observability and tracing without changing kernel source code.

### BPF Counter
 An option on `perf stat` where a BPF program reads counters to a BPF map rather than the regular `read` of a [perf event](#perf-event) file descriptor. The BPF program
 reads the counter on context switches from an event in system wide mode. If the number of system wide per CPU events is less than the number of threads
 or cgroups, this option can reduce file descriptor overhead.

### Branch Stack
 A general term for the hardware or software structure that tracks a history of branches taken by the CPU. In `perf`, it often refers to data collected via features like Intel's [LBR](#lbr).

### BTS
 Intel Branch Trace Store. A hardware feature that records a log of taken branches into a memory buffer. An older trace mechanism largely superseded by Intel PT.

## C

### C2C
 Cache-to-cache. `perf c2c` is a builtin perf tool for analyzing shared data accesses and problems like [false sharing](#false-sharing).

### CAKE

 [Coherent AMD socKet Extender](https://en.wikichip.org/wiki/amd/infinity_fabric#CAKE)

### CCD

 Principally an AMD term, Core Complex Dies are usually part of a [CCX](#ccx).

### CCX

 Prinicpally an AMD term, [Core CompleX](https://en.wikichip.org/wiki/amd/ccx)
 where dies are connected and share the L3 cache.

### CHA

 Principally an Intel term for [Caching and Home Agent
 (CHA)](https://www.intel.com/content/www/us/en/developer/articles/technical/xeon-processor-scalable-family-technical-overview.html).

### CHI

 Principally an ARM term for the [AMBA](#amba) bus [Coherent Hub
 Interface](https://developer.arm.com/documentation/102407/0100/Introducing-the-AMBA-Coherent-Hub-Interface)

### CMN

 Principally an ARM term for [Coherenet Mesh
 Network](https://docs.kernel.org/admin-guide/perf/arm-cmn.html).

### Core
 1. Used to refer to events on the CPU's PMU, contrast with [Uncore](#uncore) and [Offcore](#offcore).
 2. Part of the [topology](https://www.kernel.org/doc/Documentation/devicetree/bindings/cpu/cpu-topology.txt) containing multiple [SMT](#smt) threads.

### CoreSight
 ARM's hardware architecture for system-wide debug and trace. It allows capturing trace data from processor cores, buses, and other components.

### CPUID

 On Intel an instruction that identifies the type and properties of the
 CPU. ARM64 CPUs are generally identified from
 `/sys/devices/system/cpu/cpu0/regs/identification/midr_el1`.

### C-State
 CPU power states. C0 is the operational state, while higher numbers (C1, C6, etc.) represent deeper sleep states with lower power consumption but higher wakeup latency.

## D

### Decoder
 The component of the CPU frontend that translates complex architectural instructions (e.g., x86 instructions) into simpler micro-operations (uops) that the execution units can process.

### DRM
 Direct Rendering Manager. A subsystem of the Linux kernel responsible for interfacing with GPU drivers. Tracepoints related to DRM are often used to profile graphics activity.

### DSB
 In modern Intel CPUs, the DSB (Decoded Stream Buffer) is a uop cache storing pre-decoded instructions in the form of micro-operations (uops). The DSB allows the CPU to bypass the decoding stage for frequently used instructions sequences.

### DTG

 Termed used with Marvell CN10K chips, Lookaside Translation Group used for
 regular virtual to physical memory management.

## E

### Events
 The list of measurable events is supported by the perf tool.

### Event Selector

See [evsel](#evsel).

### evlist

 1. A [perf command](man-pages.md#latest-man-pages) to list the events within a [perf.data](#perfdata) file.
 2. A list of [event selectors](#evsel). The [evlist API is part of libperf](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/tools/lib/perf/include/perf/evlist.h).

### evsel
 An event selector describes a [`perf event`](#perf-event) that will be opened on multiple CPUs or threads. The [evsel API is part of libperf](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/tools/lib/perf/include/perf/evsel.h).

## F

### False Sharing
 When a cache line is accidentally shared, and written-to, by two threads because of the layout of data being so that it is on the same cache line.

### FB
 Fill Buffer. A small buffer used to hold data being fetched from memory or higher cache levels before it is placed in the cache.

### Flexible Event
 The default kind of event that can be [multiplexed](#multiplexing), contrast with [pinned event](#pinned-event).

### Free Running Counter
 A counter that runs through its full range of values before repeating.

### Frontend
 The part of the CPU pipeline responsible for fetching instructions from memory, predicting branches, and decoding them into micro-operations (uops) to be sent to the backend.

### Ftrace
 The official function tracer of the Linux kernel. `perf` has a subcommand `perf ftrace` which is a simple wrapper for it.

## G

### GMI

 AMD [Global Memory Interconnect](https://en.wikichip.org/wiki/amd/microarchitectures/zen).

## H

### HITM
 A load that Hit a Modified cacheline.

### Hwmon

 [The Linux Hardware Monitoring kernel API](https://docs.kernel.org/hwmon/hwmon-kernel-api.html).

### Hyperthread
 Intel/x86 term for [SMT](#smt).

## I

### IBS

 AMD Instruction-Based Sampling (IBS) allows precise a hardware performance
 monitoring similar to Intel [PEBS](#pebs). Unlike Intel PEBS, IBS provides
 separate [PMUs](#pmu) to gather relevant data.

### IDQ
 The IDQ (Instruction Decode Queue) serves as a buffer between the instruction fetch/decode stages and the rest of the out-of-order execution engine in modern CPUs. It stores decoded instructions often in the form of micro-ops.

### ITLB
 Instruction Translation Lookaside Buffer. A specialized cache for instruction address translations. On some AMD systems, this may be referred to in relation to the branch predictor (e.g., `bp_tlb`).

## J

### JEClear
 Jump Execution Clear is a kind of pipeline flush on Intel CPUs cause by branch misprediction. [Article on branching in Intel CPUs](https://stackoverflow.com/questions/31280817/what-branch-misprediction-does-the-branch-target-buffer-detect).

### JSON

 [JavaScript Object Notation](https://en.wikipedia.org/wiki/JSON).

## K

### Kprobe
 A mechanism in Linux for dynamic instrumentation of the kernel, allowing probes to be inserted at almost any instruction in the kernel code.

### KVM
 KVM stands for Kernel-based Virtual Machine, perf command with kvm helps us to trace/measure kvm guest os

## L

### L\[12\]\[DI\]

 Level 1 or 2, data or instruction cache.

### LBR
 Last Branch Record. A feature in Intel processors that records a history of recently taken branches in a set of MSRs, allowing for low-overhead call graph construction.

### Legacy Events

 Perf events are identified by a type and a config value. The type identifies
 the [PMU](#pmu). To determine the config value, data is read from
 [sysfs](#sysfs) or embedded in the perf tool via json files - the appropriate
 [json](#json) file data is identified via a [cpuid](#cpuid). Initially all
 hardware events had a single type and the mapping of the event to the config
 value was hard coded. To allow extension, sysfs and json encodings were
 added. Legacy events fail in heterogeneous systems as typically a different
 core/CPU [PMU](#pmu) is needed for each type of event. As users expect event
 event names/encodings to behave in certain ways, the removal of legacy events
 has proven contentious.

### LFB
 The Line Fill Buffer is a small, temporary buffer that sits between different levels of a CPU's cache (e.g., between L1 and L2). Its purpose is to speed up data access when the CPU needs data not found in the fastest cache level (a cache miss). Instead of making the CPU wait for an entire cache line to transfer from slower memory, the LFB stores the first chunk of data, allowing the CPU to start working immediately.

### LSD
 The Loop Stream Detector is a frontend component within Intel CPUs designed to boost performance for tight loops. It detects loops in code, pre-decodes their instructions, and stores these decoded micro-ops (uops) in a dedicated buffer. By feeding these uops directly to the execution engine, the LSD bypasses the slower instruction fetch and decode stages for loops, resulting in speedups.

### LLC

 The Last Level Cache (LLC) before accessing memory, generally level 3. Will
 generally have its own [PMU](#pmu)

### LTG

 Termed used with Marvell CN10K chips, Lookaside Translation Group used for
 special virtual to physical memory management.

## M

### Metric
 Taking multiple performance counters, typically in `perf stat`, and then computing a human meaningful value like memory bandwidth.

### MITE
 The Micro-instruction Translation Engine is a component within modern CPUs that acts as a secondary instruction decoder for complex instructions or instruction sequences.

### MPAM
 Principally an ARM term for [Memory System Resource Partitioning and Monitoring](https://developer.arm.com/documentation/107768/0100/Overview).

### MPKI
 Cache, [TLB](#tlb) or branch Misses Per Kilo (1,000) Instructions.

### MS
 The microcode sequencer is a specialized unit that handles the execution of complex instructions not directly supported as a single hardware operation. It breaks down these instructions into sequences of simpler micro-operations (microcode) that the CPU's execution units can directly understand.

### MSR
 Model Specific Register. Control registers used to control and query features in x86 processors (e.g., enabling features or reading performance counters directly).

### Multiplexing
 When the number of events is greater than the hardware counters the kernel will multiplex the events. Each event is scheduled for a period of time (by default 1kHz) and then rotated.

## O

### OCR
 Offcore Response. In Intel PMU, events used to monitor requests that go outside the core (e.g., to L3 or memory).

### Offcore
 Devices and buses outside of the core CPU.

### Off-CPU
 Off-CPU is data collected when a [task](#task) gets context switched. Brendan Gregg discusses [Off-CPU analysis](https://www.brendangregg.com/offcpuanalysis.html). Combining on-CPU time, measured with a performance
 counter, with off-CPU time will give wall clock time. Linux perf 5.20 adds a command line option to ''perf record'' to gather off-CPU data.

## P

### PDIST
 Precise Distribution. A property of certain Intel PMU counters indicating support for precise event distribution, often associated with PEBS.

### PEBS

 Intel Processor Event-Based Sampling (PEBS) is a hardware mechanism that
 enables precise performance profiling by capturing specific processor events
 and their associated instruction pointers. Unlike traditional sampling methods
 that rely on periodic interrupts, PEBS allows developers to collect performance
 data with minimal overhead and pinpoint performance bottlenecks with high
 accuracy. When a predefined event occurs, such as a cache miss or branch
 misprediction, PEBS records the instruction pointer and other relevant
 information in a dedicated buffer. This data can then be analyzed to identify
 performance hotspots and optimize code for improved efficiency. PEBS is a
 powerful tool for software developers and performance analysts seeking to
 understand and optimize application performance on Intel processors.

### perf.data
 By default output from various perf commands is stored in a file named perf.data.

### perf-event
 A kernel abstraction used to reference data being gathered about a particular kind of kernel event.

### perf-evlist
 See [man1/perf-evlist.1](https://man7.org/linux/man-pages/man1/perf-evlist.1.html).

### Pinned Event
 Pinned events are scheduled at a higher priority to flexible events. If they can't be scheduled within the kernel they don't multiplex and enter an error state.

### PMU
 A Performance Monitoring Unit is a device within the Linux kernel that the perf tool interacts with initially through the [perf_event_open](https://man7.org/linux/man-pages/man2/perf_event_open.2.html) system call.

### Port
 Execution Port. In superscalar CPUs, a portal to one or more execution units. Decoded instructions are dispatched to ports when ready.

### Precise Event

 When sampling an interrupt is triggered and the sample gathered in the
 kernel. There is typically a delay between the interrupt and the sample,
 leading the accuracy of the sample to be limited and [skid](#skid). CPU
 extensions like [PEBS](#pebs), [IBS](#ibs) and [SPE](#spe) can be used to
 reduce this. Placing the modifier 'p' after an event modifies its
 precision/[skid](#skid):

 0. SAMPLE_IP can have arbitrary [skid](#skid)
 1. 'p' -SAMPLE_IP must have constant [skid](#skid)
 2. 'pp' - SAMPLE_IP requested to have 0 [skid](#skid)
 3. 'ppp' -SAMPLE_IP must have 0 [skid](#skid), or uses randomization to avoid sample shadowing effects.

## Q

### QPI

 Intel [Quick Path Interconnect](https://en.wikipedia.org/wiki/Intel_QuickPath_Interconnect).

## R

### RAT
 In modern CPUs, a Resource Allocation Table (RAT) is a structure that renames architectural registers to a larger set of physical registers during the out-of-order execution process. This renaming breaks false dependencies caused by register naming limitations, allowing instructions to execute in parallel and boosting performance. The RAT also tracks which physical registers hold the results of in-flight instructions, ensuring that data is used at the correct time.

### RDT
 Intel Resource Director Technology. A suite of technologies providing monitoring and allocation capabilities over shared platform resources like L3 cache and memory bandwidth.

### ROB
 Reorder Buffer, tracks in-flight instructions and their results in out-of-order CPUs.

### RS
 Reservation Station, buffers instructions awaiting resources (functional units, data) before execution.

## S

### SBI (RISC-V)
 Supervisor Binary Interface. The standard interface between the supervisor mode (OS) and machine mode (firmware) in RISC-V systems.

### SDF

 [Scalable Data
 Fabric](https://en.wikichip.org/wiki/amd/infinity_fabric#Scalable_Data_Fabric_.28SDF.29).

### Skid

The distance between where a sample is, an instruction, and where the sampling
event was actually triggered. Ideally there would be no skid on samples.

### SLC

 System Level Cache generally used interchangeably with [Last Level Cache](#llc)
 but may incorporate caches outside of the processor.

### Slots / TMA Slots
 In Top-down analysis, a "slot" represents the potential to execute a micro-operation (uop) in a given cycle. The total number of slots depends on the pipeline width and cycles.

### SMC
 Self-Modifying Code. Code that modifies itself while executing, which can cause pipeline clears or performance penalties in modern out-of-order CPUs.

### SMI

  System Management Interrupts trasition the CPU into [System Management
  Mode](#smm) to hanle high privilege events.

### SMM

 On x86 generally [System Management
 Mode](https://en.wikipedia.org/wiki/System_Management_Mode), on ARM Secure
 Monitor Mode, is a highly privileged firmware/[BIOS](#bios) code.

### SMT
 [Simultaneous multithreading](https://en.wikipedia.org/wiki/Simultaneous_multithreading).

### Software Event

 They are kernel/os-based low-level counters. To identify the list of supported
 software events use the command: perf list software

### SPE

 ARM's [Statistical Profiling Extension](
 https://developer.arm.com/documentation/ka005362/1-0) is an optional ARM v8
 feature that provides precise sampling. Currently the feature is common on
 server ARM CPUs.

### Sscofpmf (RISC-V)
 Supervisor-level Count Overflow and Filtering PMU Extension. The standard extension for hardware performance monitoring in RISC-V.

### Stall
 A condition where the CPU pipeline cannot make forward progress due to a hazard or resource conflict.

### STLB
 Second-level TLB. A larger, slower cache for address translations that backs the first-level TLBs (ITLB, DTLB).

### sysfs

 [A filesystem for exporting kernel
 objects](https://man7.org/linux/man-pages/man5/sysfs.5.html).

## T

### TAD

 [Last-Level cache](#llc) Tag-and-data Units on Marvell CN10K family silicon.

### TLB

 TLB stands for Translation Lookaside Buffer. It is a specialized cache that
 stores the recent translations, via the page table, of virtual memory addresses
 to physical memory addresses.

### TMA
 Top-down Microarchitecture Analysis. A methodology for identifying performance bottlenecks by categorizing CPU cycles into a hierarchy of metrics.

### TPEBS

 Timed Process Event Based Sampling on newer Intel processors adds retirement
 latency information to samples. The retirement latency is the number of cycles
 between the sampled and the previous instructions retiring. One use is to avoid
 having hard coded constants in [top-down analysis metrics](
 https://www.intel.com/content/www/us/en/developer/articles/technical/timed-process-event-based-sampling-tpebs.html),
 so that overheads are more reflective of the actual workload.

### TOR
 The Table-of-Requests (TOR) is a data structure within Intel's Integrated Memory Controller (IMC) that stores and tracks in-flight memory requests made by CPU cores.

### Task
 Kernel term for a process (if just 1 thread) or thread.

### Tracepoint
 A static instrumentation point placed in the kernel source code for stable event tracking.

### TSX

 Principally an Intel term, [Transactional Synchronization
 Extensions](https://en.wikipedia.org/wiki/Transactional_Synchronization_Extensions)

## U

### Uncore
 [Functions of the microprocessor not in the core](https://en.wikipedia.org/wiki/Uncore).

### UOp
 Abbreviation for micro-operation commonly used by Intel.

### UPI
 Intel [Ultra Path Interconnect](https://en.wikipedia.org/wiki/Intel_Ultra_Path_Interconnect).

### Uprobe
 Similar to kprobe, but for dynamic instrumentation of user-space applications.

## V

### VMA

 Virtual Memory Area used by the kernel to manage process memory.

## X

### xGMI

 eXternal [GMI](#gmi) link is a socket-to-socket interconnect that is part of AMD's
 Infinity Fabric.
