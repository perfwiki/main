# Top-down analysis with the perf tool

Note, the description here is for Linux 6.1. Earlier versions of the perf tool
supported top-down analysis but due to naming inconsistencies in old versions,
Linux 6.1 is focused upon on this page.

## What is top-down analysis?

Top-down analysis is an approach for identifying software performance
bottlenecks. It is described in [A Top-Down method for performance analysis and
counters architecture](https://doi.org/10.1109/ISPASS.2014.6844459) but is often
come across in Appendix B of [Intel's Optimization Reference Manual in their
software development
manuals](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html).
The approach gathers a group of metrics and from these a problematic metric is
identified. A group of metrics associated with the problematic metric are then
measured drilling down to the metric/issue that identifies the performance
problem.

More recent Intel processors feature improvements for top-down analysis, the
most recent being Timed Process Event Based Sampling (TPEBS). A description for
using perf from Intel is given
[here](https://www.intel.com/content/www/us/en/developer/articles/technical/timed-process-event-based-sampling-tpebs.html).

## Starting at the top

In this tutorial **perf bench mem memcpy** will be used for the **<benchmark>**. First of all gather the level 1 metrics:
```sh
  $ perf stat -M TopdownL1 <benchmark>
```
The output will be like:
```sh
  $ perf stat -M TopdownL1 perf bench mem memcpy
  
  # Running 'mem/memcpy' benchmark:
  # function 'default' (Default memcpy() provided by glibc)
  # Copying 1MB bytes ...
  
         5.677689 GB/sec
  # function 'x86-64-unrolled' (unrolled memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         5.425347 GB/sec
  # function 'x86-64-movsq' (movsq-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.141903 GB/sec
  # function 'x86-64-movsb' (movsb-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         7.454676 GB/sec
  
   Performance counter stats for 'perf bench mem memcpy':
  
          72,813,380      TOPDOWN.SLOTS                    #     25.5 %  tma_retiring        
                                                    #     44.3 %  tma_backend_bound   
                                                    #     24.3 %  tma_frontend_bound  
                                                    #      6.0 %  tma_bad_speculation 
          18,560,273      topdown-retiring                 #     25.5% Retiring              
          17,703,645      topdown-fe-bound                 #     24.3% Frontend Bound        
              43,171      INT_MISC.UOP_DROPPING                                              
          31,980,778      topdown-be-bound                 #     43.9% Backend Bound         
              55,052      cpu/INT_MISC.RECOVERY_CYCLES,cmask=1,edge/                                   
           4,568,682      topdown-bad-spec                 #      6.3% Bad Speculation       
  
         0.012820482 seconds time elapsed
  
         0.004290000 seconds user
         0.008581000 seconds sys
```

The TopdownL1 metrics may be computed by default if no metric is provided:
```sh
  $ perf stat perf bench mem memcpy
  # Running 'mem/memcpy' benchmark:
  # function 'default' (Default memcpy() provided by glibc)
  # Copying 1MB bytes ...
  
        16.837284 GB/sec
  # function 'x86-64-unrolled' (unrolled memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
        16.551907 GB/sec
  # function 'x86-64-movsq' (movsq-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
        19.148284 GB/sec
  
   Performance counter stats for 'perf bench mem memcpy':
  
               21.38 msec task-clock                       #    0.927 CPUs utilized             
                   7      context-switches                 #  327.406 /sec                      
                   0      cpu-migrations                   #    0.000 /sec                      
               6,247      page-faults                      #  292.187 K/sec                     
          94,684,914      cycles                           #    4.429 GHz                       
         117,788,965      instructions                     #    1.24  insn per cycle            
          25,949,650      branches                         #    1.214 G/sec                     
             252,622      branch-misses                    #    0.97% of all branches           
                          TopdownL1                 #     45.8 %  tma_backend_bound      
                                                    #     11.2 %  tma_bad_speculation    
                                                    #     18.2 %  tma_frontend_bound     
                                                    #     24.8 %  tma_retiring           
  
         0.023069998 seconds time elapsed
  
         0.000000000 seconds user
         0.023455000 seconds sys
```

On the right of the counter values are the metrics. For TopdownL1 there are the metrics tma_retiring, tma_backend_bound, tma_frontend_bound and tma_bad_speculation. For the benchmark tma_backend_bound is the largest. We can drill down into this metric by adding the suffix **_group** to the metric name:
```sh
  $ perf stat -M tma_backend_bound_group perf bench mem memcpy
  
  # Running 'mem/memcpy' benchmark:
  # function 'default' (Default memcpy() provided by glibc)
  # Copying 1MB bytes ...
  
         6.781684 GB/sec
  # function 'x86-64-unrolled' (unrolled memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         5.033827 GB/sec
  # function 'x86-64-movsq' (movsq-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.688784 GB/sec
  # function 'x86-64-movsb' (movsb-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.829108 GB/sec
  
   Performance counter stats for 'perf bench mem memcpy':
  
          69,746,420      TOPDOWN.SLOTS                    #     24.9 %  tma_core_bound      
                                                    #     17.5 %  tma_memory_bound    
          18,599,045      topdown-retiring                 #     26.7% Retiring              
           1,253,770      EXE_ACTIVITY.BOUND_ON_STORES                                       
          17,504,983      topdown-fe-bound                 #     25.1% Frontend Bound        
           1,769,443      EXE_ACTIVITY.1_PORTS_UTIL                                          
          29,266,144      topdown-be-bound                 #     42.0% Backend Bound         
              55,050      cpu/INT_MISC.RECOVERY_CYCLES,cmask=1,edge/                                   
           2,934,845      CYCLE_ACTIVITY.STALLS_MEM_ANY                                      
           6,667,954      CYCLE_ACTIVITY.STALLS_TOTAL                                        
           1,775,168      EXE_ACTIVITY.2_PORTS_UTIL                                          
           4,376,245      topdown-bad-spec                 #      6.3% Bad Speculation       
  
         0.012655913 seconds time elapsed
  
         0.008451000 seconds user
         0.004225000 seconds sys
```

This time tma_core_bound is the largest TMA metric and so we drill down in to it:
```sh
  $ perf stat -M tma_core_bound_group perf bench mem memcpy
  # Running 'mem/memcpy' benchmark:
  # function 'default' (Default memcpy() provided by glibc)
  # Copying 1MB bytes ...
  
         6.510417 GB/sec
  # function 'x86-64-unrolled' (unrolled memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.065606 GB/sec
  # function 'x86-64-movsq' (movsq-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         7.512019 GB/sec
  # function 'x86-64-movsb' (movsb-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.781684 GB/sec
  
   Performance counter stats for 'perf bench mem memcpy':
  
        70,285,910      TOPDOWN.SLOTS                    #     31.2 %  tma_ports_utilization
        18,467,278      topdown-retiring                 #     26.3% Retiring              
         2,165,618      cpu/EXE_ACTIVITY.3_PORTS_UTIL,umask=0x80/                                   
        17,364,754      topdown-fe-bound                 #     24.7% Frontend Bound        
         1,754,496      EXE_ACTIVITY.1_PORTS_UTIL                                          
        30,043,781      topdown-be-bound                 #     42.7% Backend Bound         
        14,057,182      CPU_CLK_UNHALTED.THREAD          #      0.1 %  tma_divider         
         3,054,356      CYCLE_ACTIVITY.STALLS_MEM_ANY                                      
         6,685,779      CYCLE_ACTIVITY.STALLS_TOTAL                                        
         1,767,046      EXE_ACTIVITY.2_PORTS_UTIL                                          
         4,410,096      topdown-bad-spec                 #      6.3% Bad Speculation       
             9,354      ARITH.DIVIDER_ACTIVE                                               
  
       0.011282941 seconds time elapsed
  
       0.000000000 seconds user
       0.011349000 seconds sys
```

And then tma_ports_utilization:
```sh
  $ perf stat -M tma_ports_utilization_group perf bench mem memcpy
  
  # Running 'mem/memcpy' benchmark:
  # function 'default' (Default memcpy() provided by glibc)
  # Copying 1MB bytes ...
  
         6.554111 GB/sec
  # function 'x86-64-unrolled' (unrolled memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...  
  
         5.710892 GB/sec
  # function 'x86-64-movsq' (movsq-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.467301 GB/sec
  # function 'x86-64-movsb' (movsb-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
         6.300403 GB/sec
  
   Performance counter stats for 'perf bench mem memcpy':
  
         1,812,959      RESOURCE_STALLS.SCOREBOARD       #     16.6 %  tma_ports_utilized_0  (34.73%)
         1,991,726      cpu/EXE_ACTIVITY.3_PORTS_UTIL,umask=0x80/                                     (34.73%)
        14,159,441      CPU_CLK_UNHALTED.THREAD                                              (34.73%)
         6,689,757      CYCLE_ACTIVITY.STALLS_TOTAL                                          (34.73%)
         3,838,402      CYCLE_ACTIVITY.STALLS_MEM_ANY                                        (34.73%)
         3,282,823      UOPS_EXECUTED.CYCLES_GE_3        #     22.9 %  tma_ports_utilized_3m  (52.98%)
        14,324,185      CPU_CLK_UNHALTED.THREAD                                              (52.98%)
        14,599,955      CPU_CLK_UNHALTED.THREAD          #     12.5 %  tma_ports_utilized_2  (65.27%)
         1,823,495      EXE_ACTIVITY.2_PORTS_UTIL                                            (65.27%)
         1,819,926      EXE_ACTIVITY.1_PORTS_UTIL        #     12.5 %  tma_ports_utilized_1  (79.65%)
        14,591,940      CPU_CLK_UNHALTED.THREAD                                              (79.65%)
  
       0.012931961 seconds time elapsed
  
       0.008647000 seconds user
       0.004323000 seconds sys
```

For tma_ports_utilization_group we can see numbers like **(34.73%)** that indicate there were insufficient performance counters to gather the metric and the performance counters had to be multiplexed during the benchmark run. Multiplexing lowers accuracy and can be worked around by just measuring the metric on its own:
```sh
  $ perf stat -M tma_ports_utilized_0 perf bench mem memcpy
  ...
         2,268,815      RESOURCE_STALLS.SCOREBOARD       #     19.8 %  tma_ports_utilized_0
```

Finally we see tma_ports_utilized_3m as the largest metric. Looking at `perf list`, (sometimes `perf list -v`) we can see the metrics meaning:

 - tma_ports_utilized_3m

    This metric represents fraction of cycles CPU executed total of 3 or more uops per cycle on all execution ports (Logical Processor cycles since ICL, Physical Core cycles otherwise). Sample with: UOPS_EXECUTED.CYCLES_GE_3

The 'Sample with' event can be used with `perf record` to identify where in the benchmark the performance bottleneck is:
```sh
  $ perf record -e UOPS_EXECUTED.CYCLES_GE_3 perf bench mem memcpy; perf report
  
  # Running 'mem/memcpy' benchmark:
  # function 'default' (Default memcpy() provided by glibc)
  # Copying 1MB bytes ...
  
        23.251488 GB/sec
  # function 'x86-64-unrolled' (unrolled memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
        23.251488 GB/sec
  # function 'x86-64-movsq' (movsq-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
        28.722426 GB/sec
  # function 'x86-64-movsb' (movsb-based memcpy() in arch/x86/lib/memcpy_64.S)
  # Copying 1MB bytes ...
  
        27.901786 GB/sec
  [ perf record: Woken up 1 times to write data ]
  [ perf record: Captured and wrote 0.034 MB perf.data (1 samples) ]
  
  # To display the perf.data header info, please use --header/--header-only options.  
  #
  #
  # Total Lost Samples: 0  
  #
  # Samples: 1  of event 'UOPS_EXECUTED.CYCLES_GE_3/period=1000000/'
  # Event count (approx.): 2000003 
  #
  # Overhead  Command     Shared Object  Symbol         
  # ........  ..........  .............  ...............  
  #
     100.00%  mem-memcpy  perf           [.] memcpy_orig
```
