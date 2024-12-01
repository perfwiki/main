# Main Page

## `perf`: Linux profiling with performance counters
...More than just counters...

### Introduction

This is the wiki page for the Linux `perf` command, also called perf_events. `perf` is powerful: it can instrument CPU performance counters, tracepoints, kprobes, and uprobes (dynamic tracing). It is capable of lightweight profiling. It is also included in the Linux kernel, under `tools/perf`, and is frequently updated and enhanced.

`perf` began as a tool for using the performance counters subsystem in Linux, and has had various enhancements to add tracing capabilities.

Performance counters are CPU hardware registers that count hardware events such as instructions executed, cache-misses suffered, or branches mispredicted. They form a basis for profiling applications to trace dynamic control flow and identify hotspots. `perf` provides rich generalized abstractions over hardware specific capabilities. Among others, it provides per task, per CPU and per-workload counters, sampling on top of these and source code event annotation.

Tracepoints are instrumentation points placed at logical locations in code, such as for system calls, TCP/IP events, file system operations, etc. These have negligible overhead when not in use, and can be enabled by the `perf` command to collect information including timestamps and stack traces. `perf` can also dynamically create tracepoints using the kprobes and uprobes frameworks, for kernel and userspace dynamic tracing. The possibilities with these are endless.

The userspace `perf` command present a simple to use interface with commands like:

- [`perf stat`](tutorial.md#counting-with-perf-stat): obtain event counts
- [`perf record`](tutorial.md#sampling-with-perf-record): record events for later reporting
- [`perf report`](tutorial.md#sample-analysis-with-perf-report): break down events by process, function, etc.
- [`perf annotate`](tutorial.md#source-level-analysis-with-perf-annotate): annotate assembly or source code with event counts
- [`perf top`](tutorial.md#live-analysis-with-perf-top): see live event count 
- [`perf bench`](tutorial.md#benchmarking-with-perf-bench): run different kernel microbenchmarks

To learn more, see the examples in the [Tutorial](tutorial.md) or how to do a [Top-Down Analysis](top-down-analysis.md).

To ask questions, report bugs/issues mail the [mailing list📨](https://lore.kernel.org/linux-perf-users/) or use [bugzilla🐞](https://bugzilla.kernel.org/buglist.cgi?bug_status=__open__&order=changeddate%20DESC%2Cpriority%2Cbug_severity&product=Tracing%2FProfiling&query_format=advanced).

In order to subscribe to receive perf mailing lists messages via email you will need to send a message to a special subscription address of the list.

* to receive **an individual copy of every message** sent to the list, send an email to:

    * **[linux-perf-users+subscribe@vger.kernel.org](mailto:linux-perf-users+subscribe@vger.kernel.org)**

* to receive **a daily digest of all messages** sent to the list, send an email to:

    * **[linux-perf-users+subscribe-digest@vger.kernel.org](mailto:linux-perf-users+subscribe-digest@vger.kernel.org)**

### Wiki Contents

- [Tutorial](tutorial.md)
- [Top-Down Analysis](top-down-analysis.md)
- [Kernel lock contention](lock-contention.md)
- [Todo](todo.md)
- [Hardware Reference](hardware-reference.md)
- [perf_events kernel ABI](perf_events-kernel-abi.md)
- [perf tools support for Intel® Processor Trace](perf-tools-support-for-intel-processor-trace.md)
- [Useful Links](useful-links.md): How perf tools work, examples of usage to solve real problems, observability articles, hardware manuals
- [Glossary](glossary.md)
- [Latest Manual Pages](man-pages.md#latest-man-pages)
- [Development](development.md)

### Google Summer of Code

As part of the [Linux Foundation](https://www.linuxfoundation.org/) the perf tool has participated in the [Google Summer-of-Code](https://summerofcode.withgoogle.com/) since 2021. [Check out the 2024 process](https://wiki.linuxfoundation.org/gsoc/2024-gsoc-perf).
