# Hardware Reference

The capabilities of perf depends on the features of the CPU. The following list the features of common modern architectures.

# Manuals

- [Intel PMU event tables](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html), in particular Appendix B of the Intel® 64 and IA-32 Architectures Optimization Reference Manual.
- [Intel Software Developer's Manual (SDM)](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html), for details on Intel Processor Trace (PT) and Branch Trace Store (BTS) in Volume 3.
- [Intel perfmon GitHub](https://github.com/intel/perfmon), repository for Intel JSON event data.
- [AMD PMU event table](https://www.amd.com/en/developer/resources/technical-documents.html), the Processor Programming Reference Manual for AMD 17h family see Section 2.1.15 Performance Monitor Counters.
- [AMD Instruction Based Sampling (IBS)](https://www.amd.com/en/developer/resources/technical-documents.html), documented in the Processor Programming Reference manuals.
- [ARM PMU event tables](https://developer.arm.com/architectures/cpu-architecture/a-profile/docs), see Chapter D7. The Performance Monitors Extension, of the Arm Architecture Reference Manual Armv8, for Armv8-A architecture profile.
- [ARM Neovese N2 PMU guide](print/arm_neoverse_n2_pmu_guide_109710_0003_03_en.pdf).
- [ARM Statistical Profiling Extension (SPE)](https://developer.arm.com/architectures/cpu-architecture/a-profile/docs), see Chapter D7 or specific SPE documentation in the Armv8-A Reference Manual.
- [ARM CoreSight Architecture Specification](https://developer.arm.com/documentation/ihi0029/latest), official specification for ARM's system-wide debug and trace architecture.
- [ARM Telemetry Solution GitLab](https://git.gitlab.arm.com/telemetry-solution/telemetry-solution), repository for ARM JSON event data.
- [IBM Docs: CPU-Measurement Facilities counters](https://www.ibm.com/docs/en/linux-on-z?topic=reference-cpu-measurement-facilities-counters), for s390x architecture.
- [IBM POWER PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/powerpc/perf/), Linux kernel implementation details for POWER processors.
- [RISC-V Privileged Spec (v1.12)](https://github.com/riscv/riscv-isa-manual/releases/download/Priv-v1.12/riscv-privileged-20211203.pdf), see Chapter on "Supervisor-level Count Overflow and Filtering PMU Extension" (SSCOFPMF).

# Legacy

- [Intel(TM) Itanium(TM) 2 PMU](montecito.md)
- [Intel(TM) Nehalem](nehalem.md)

# Less Common & Legacy Architectures

These architectures are supported in the Linux kernel and have varying degrees of `perf` support for their hardware counters. Links point to the kernel implementation where official manuals are less accessible.

- [Alpha PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/alpha/kernel/perf_event.c), Linux kernel implementation.
- [ARC PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/arc/kernel/perf_event.c), Linux kernel implementation.
- [CSKY PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/csky/kernel/perf_event.c), Linux kernel implementation.
- [LoongArch PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/loongarch/kernel/perf_event.c), Linux kernel implementation.
- [MIPS PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/mips/kernel/perf_event.c), Linux kernel implementation.
- [PA-RISC (parisc) PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/parisc/kernel/perf_event.c), Linux kernel implementation.
- [Sparc PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/sparc/kernel/perf_event.c), Linux kernel implementation.
- [SuperH (sh) PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/sh/kernel/perf_event.c), Linux kernel implementation.
- [Xtensa PMU support](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/arch/xtensa/kernel/perf_event.c), Linux kernel implementation.
