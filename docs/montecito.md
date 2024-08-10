# Montecito

## Intel® Itanium® 2 Montecito® processor PMU

### Introduction

Intel defines the architected PMU in the 'Intel IA-64 Architecture Software Developer's Manual'. However the architected PMU is a bare bone version of what is actually implemented. It is noteworthy that the Itanium 2
PMU has not changed too much, so support of the whole familly does not require rewriting the tools for each member
of the familly. Therefore readers interrested in the capabilities of the Itanium PMU should go directly to the model specific updated documentation of a processor's functionality.

The Itanium-1 (Merced) is obsolete, so there's two PMU implementations that are available: The McKinley class PMU (with 4 counters), and the Montecito class PMU (with 12 counters). The Montecito processor supports hardware threads
(hyperthreading), so the 4 architected counters allow monitoring of activity of the whole core or the hardware thread,
while the 8 new counters can only monitor thread activity.

## Overview

Itanium defines configuration and data registers: PMC, PMDs. Counting registers are paired PMC/PMD, where you program what the counter will count in the PMC and read the actual counter in the PMD. Other PMC/PMDs that are not be counters may not be paired-up.

The Itanium PMU is a synchronized subsystem with a global state, where all counters can be started or stopped synchronously. When a counter wraps around, it can trigger a PMU overflow interrupt and freeze the whole PMU, which allows for consistent measurment of multiple events. The state of the PMU can be read in the overflow PMCs. Counters are 47bits physically but presented as a 64-bit sign extended entity.

The PMU can filter events through multiple mechanisms to focus on specific areas. It can filter by privilege level with a counter's plm bitmaks. It can filter by instruction op-codes, by virtual address range using the debug registers.

The PMU also has an '''EAR''', event address registers which are special PMCs & PMDs to collect address traces and memory latencies; and the ETB, the event trace buffer, to collect branch history.

![Montecito_pmu_list](./Montecito_pmu_list.png)

Montecito control registers go beyond controlling event counters. The following picture shows the bit field configuration for the control registers.

![Montecito_pmc](./Montecito_pmc.jpg)

Brief description:
- CNT: regular performance counters configuration
- OVF: overflow bitmask. A bit set to 1 indicates the associated event counter overflowed. PMC0.fr indicates that the whole PMU was frozen by an overflowing counter.
- IARC: filter event collection to a range of instruction addresses. Events happening while instructions are outside that range will be ignored.
- OM: filter event collection to specific instruction op-codes.
- MPEC: filter event collection to specific memory events.
- IEAR: gather a trace sample for specific instruction events into the EAR.
- DEAR: gather a trace sample specific data event into the EAR.
- BTB: collect a trace stack of branches into the ETB buffer.

## External links
- Wikipedia (Intel Itanium)[http://en.wikipedia.org/wiki/Itanium]
- Wikipedia [Montecito processor](http://en.wikipedia.org/wiki/Montecito_(processor))
- Intel (Intel® Itanium® Architecture)[http://www.intel.com/design/itanium/manuals/iiasdmanual.htm]
- Intel (Introduction to Microarchitectural Optimization for Itanium2 Processors)[http://cache-www.intel.com/cd/00/00/21/93/219348_software_optimization.pdf]
- Intel (Dual-Core Update to the Intel® Itanium® 2 Processor Reference Manual)[http://download.intel.com/design/Itanium2/manuals/30806501.pdf] (PDF 2.29MB)

----
Last edit by: --Dantruong 01:48, 28 August 2009 (UTC)
