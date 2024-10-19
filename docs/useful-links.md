# Useful web sites about the Linux perf tools

- [Julia Evans' Profiling & tracing with perf zine](https://wizardzines.com/zines/perf/)
- [Brendan Gregg's perf examples](http://www.brendangregg.com/perf.html)
- [Wikipedia](https://en.wikipedia.org/wiki/Perf_(Linux))
- [Franck Pachot's not so basic perf top tutorial "Linux perf-top basics: understand the %"](https://www.dbi-services.com/blog/linux-perf-top-basics-understand-the/)
- [Frank Pachot help going from 'time' to 'perf stat' while analysing database performance on "Should CPU-intensive logic be done in the DB or in application server?"](https://www.dbi-services.com/blog/should-cpu-intensive-logic-be-done-in-the-db-or-in-application-server/)
- [Ankur Arora uses perf and pahole to investigate "Syscall latency... and some uses of speculative execution"](https://blogs.oracle.com/linux/post/syscall-latency)
- [Leo Yan on supporting 'perf c2c' on Armv8.2 "Using the Arm Statistical Profiling Extension to detect false cache-line sharing"](https://old.linaro.org/blog/using-the-arm-statistical-profiling-extension-to-detect-false-cache-line-sharing/)
- [Mark Dawson, Jr. uses 'perf diff' on "5-level vs 4-level Page Tables: Does It Matter?"](https://www.jabperf.com/5-level-vs-4-level-page-tables-does-it-matter/)
- [Joe Mario's original 2016 article about 'perf c2c' "C2C - False Sharing Detection in Linux Perf"](https://joemario.github.io/blog/2016/09/01/c2c-blog/)
- [Paul Clarke reimplements AIX's 'curt' command using perf python scripting support, detailing the whole process on "How to analyze your system with perf and Python"](https://opensource.com/article/18/7/fun-perf-and-python)
- [Viacheslav Biriukov mentions using perf to get more info about page cache activity in the "Advanced tools" section in his "SRE deep dive into Linux Page Cache" series of articles](https://biriukov.dev/docs/page-cache/0-linux-page-cache-for-sre/)
- [Filip Busic uses perf and other BPF tools in "Examining Problematic Memory in C/C++ Applications with BPF, perf, and Memcheck"](https://doordash.engineering/2021/04/01/examining-problematic-memory-with-bpf-perf-and-memcheck/)
- (French) [Hadrien Grasland's perf tutorial (designed to be run on a specific system, but could be adapted to self-study without too much effort)](https://grasland.pages.in2p3.fr/tp-perf)
- [Leo Yan, Perf Ring Buffer](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/userspace-api/perf_ring_buffer.rst)
- [Leo Yan Debugging perf with perf](print/how_to_use_perf_to_debug_perf.pdf)
- [Paul Clarke helps transitioning from OProfile to perf](https://developer.ibm.com/tutorials/migrate-from-oprofile-to-perf/)
- [Timed Process Event Based Sampling](https://www.intel.com/content/www/us/en/developer/articles/technical/timed-process-event-based-sampling-tpebs.html)

# Useful web sites about profilers

- [ProfilerPedia A map of the Software Profiling Ecosystem](https://profilerpedia.markhansen.co.nz/)
- [OProfile](https://oprofile.sourceforge.io/news/)

# Presentations

- [Improving the Perf event subsystem after 15 years](https://lpc.events/event/18/contributions/1836/) Ian Rogers, LPC September 2024
- [Utilizing type info in the perf tools](https://lpc.events/event/18/contributions/1833/) Namhyung Kim, LPC September 2024
- [Linux perf tool metrics](https://lpc.events/event/17/contributions/1514/) Ian Rogers, Weilin Wang, LPC November 2023
- [Towards data type profiling](https://lpc.events/event/17/contributions/1616/) Namhyung Kim, LPC November 2023
- [Easyperf Twitter Spaces with Arnaldo Carvalho de Melo and Denis Bakhvalov](https://youtu.be/aUDtN0qjxD0) October 2021
- [Integration Arm SPE in Perf for Memory Profiling](print/lvc21-302.pdf) Spring 2021
- [Stephane Eranian's keynote at SuperComputing ProTools 2019: Hardware Performance Monitoring Landscape](print/Eranian_KeynoteSC19.pdf)
- [Chandler Carruth "Tuning C++: Benchmarks, and CPUs, and Compilers! Oh My!"](https://youtu.be/nXaxk27zwlk CppCon 2015)
- [Roberto Vitillo's presentation on Perf events](print/RobertoVitillo_FutureTech_EDI.pdf) June 2011

# In the press

- Discovery/analysis of `libxz` backdoor with `perf record -e intel_pt//ub` [by the discoverer Andres Freund](https://www.openwall.com/lists/oss-security/2024/03/29/4).
