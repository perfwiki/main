# Glossary

## B

### Beat
 A beat is the smallest single unit of data transfer within an AXI transaction. Its size is determined by the bus width. A Burst can be comprised of multiple beats. See the [Arm Glossary](https://developer.arm.com/documentation/105565/0200/B)

### BPF
 [Berkeley Packet Filter](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter).

### BPF Counter
 An option on `perf stat` where a BPF program reads counters to a BPF map rather than the regular `read` of a [perf event](#perf-event) file descriptor. The BPF program  
 reads the counter on context switches from an event in system wide mode. If the number of system wide per CPU events is less than the number of threads  
 or cgroups, this option can reduce file descriptor overhead.

## C

### C2C
 Cache-to-cache. `perf c2c` is a builtin perf tool for analyzing shared data accesses and problems like (false sharing)[#false-sharing].

### Core
 1. Used to refer to events on the CPU's PMU, contrast with [Uncore](#uncore) and [Offcore](#offcore).
 2. Part of the [topology](https://www.kernel.org/doc/Documentation/devicetree/bindings/cpu/cpu-topology.txt) containing multiple [SMT](#smt) threads.

## D

### DSB
 In modern Intel CPUs, the DSB (Decoded Stream Buffer) is a uop cache storing pre-decoded instructions in the form of micro-operations (uops).  The DSB allows the CPU to bypass the  decoding stage for frequently used instructions sequences.

## E

### Events
 The list of measurable events is supported by the perf tool. 

### Event Selector

See [evsel](#evsel).

### evlist

 1. A [perf command](latest-manual-pages.md) to list the events within a [perf.data](#perfdata) file.
 2. A list of [event selectors](#evsel). The [evlist API is part of libperf](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/tools/lib/perf/include/perf/evlist.h).

### evsel
 An event selector describes a [`perf event`](#perf-event) that will be opened on multiple CPUs or threads. The [evsel API is part of libperf](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/tools/lib/perf/include/perf/evsel.h).


## F

### False Sharing
 When a cache line is accidentally shared, and written-to, by two threads because of the layout of data being so that it is on the same cache line.

### Flexible Event
 The default kind of event that can be [multiplexed](#multiplexing), contrast with [pinned event](#pinned-event).

### Free Running Counter
 A counter that runs through its full range of values before repeating.

## H

### HITM
 A load that Hit a Modified cacheline.

### Hyperthread
 Intel/x86 term for [SMT](#smt).

## I

### IDQ
 The IDQ (Instruction Decode Queue) serves as a buffer between the instruction fetch/decode stages and the rest of the out-of-order execution engine in modern CPUs. It stores decoded instructions often in the form of micro-ops.

## J

### JEClear
 Jump Execution Clear is a kind of pipeline flush on Intel CPUs cause by branch misprediction. [Article on branching in Intel CPUs](https://stackoverflow.com/questions/31280817/what-branch-misprediction-does-the-branch-target-buffer-detect).

## K

### KVM
 KVM stands for Kernel-based Virtual Machine, perf command with kvm helps us to trace/measure kvm guest os

## L

### LFB
 The Line Fill Buffer is a small, temporary buffer that sits between different levels of a CPU's cache (e.g., between L1 and L2). Its purpose is to speed up data access when the CPU needs data not found in the fastest cache level (a cache miss). Instead of making the CPU wait for an entire cache line to transfer from slower memory, the LFB stores the first chunk of data, allowing the CPU to start working immediately.

### LSD
 The Loop Stream Detector is a frontend component within Intel CPUs designed to boost performance for tight loops. It detects loops in code, pre-decodes their instructions, and stores these decoded micro-ops (uops) in a dedicated buffer.  By feeding these uops directly to the execution engine, the LSD bypasses the slower instruction fetch and decode stages for loops, resulting in speedups.

## M

### Metric
 Taking multiple performance counters, typically in `perf stat`, and then computing a human meaningful value like memory bandwidth.

### MITE
 The Micro-instruction Translation Engine is a legacy component within modern CPUs that acts as a secondary, simpler instruction decoder. While the primary decoder handles most instructions efficiently, the MITE is responsible for decoding specific complex instructions or instruction sequences that aren't well-suited for the main decoder.  The MITE often works alongside the microcode sequencer to break down these complex instructions into simpler micro-operations for the CPU to execute.

### MS
 The microcode sequencer is a specialized unit that handles the execution of complex instructions not directly supported as a single hardware operation. It breaks down these instructions into sequences of simpler micro-operations (microcode) that the CPU's execution units can directly understand.

### Multiplexing
 When the number of events is greater than the hardware counters the kernel will multiplex the events. Each event is scheduled for a period of time (by default 1kHz) and then rotated.

## O

### Offcore
 Devices and buses outside of the core CPU.

### Off-CPU
 Off-CPU is data collected when a [task](#task) gets context switched. Brendan Gregg discusses [Off-CPU analysis](https://www.brendangregg.com/offcpuanalysis.html). Combining on-CPU time, measured with a performance  
 counter, with off-CPU time will give wall clock time. Linux perf 5.20 adds a command line option to ''perf record'' to gather off-CPU data.

## P

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

## R

### RAT
 In modern CPUs, a Resource Allocation Table (RAT) is a structure that renames architectural registers to a larger set of physical registers during the out-of-order execution process. This renaming breaks false dependencies caused by register naming limitations, allowing instructions to execute in parallel and boosting performance. The RAT also tracks which physical registers hold the results of in-flight instructions, ensuring that data is used at the correct time.

### ROB
 Reorder Buffer, tracks in-flight instructions and their results in out-of-order CPUs.

### RS
 Reservation Station, buffers instructions awaiting resources (functional units, data) before execution.

## S

### Software Event
 They are kernel/os-based low-level counters. To identify the list of supported software events use the command: perf list software

### SMT
 [Simultaneous multithreading](https://en.wikipedia.org/wiki/Simultaneous_multithreading).

## T

### TOR
 The Table-of-Requests (TOR) is a data structure within Intel's Integrated Memory Controller (IMC) that stores and tracks in-flight memory requests made by CPU cores.

### Task
 Kernel term for a process (if just 1 thread) or thread.

## U

### Uncore
 [Functions of the microprocessor not in the core](https://en.wikipedia.org/wiki/Uncore).

### UOp
 Abbreviation for micro-operation commonly used by Intel.
