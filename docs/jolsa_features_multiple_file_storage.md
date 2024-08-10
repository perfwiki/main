## Description
This feature adds the possibility to store perf record data
into multiple files.

- new version of perf.data file was added (v3)
- `-M/--multi` option was added into perf record

- example:
```sh
  $ perf record -M 5M yes > /dev/null
  ^C[ perf record: Woken up 228 times to write data ]
  [ perf record: Captured and wrote 20.246 MB perf.data-[0-4](~884542 samples) ]
  yes: Interrupt
  $ ls -l perf.data-0*
  -rw------- 1 jolsa jolsa 5289856 Aug 16 16:07 perf.data-00000
  -rw------- 1 jolsa jolsa 5296008 Aug 16 16:08 perf.data-00001
  -rw------- 1 jolsa jolsa 5344968 Aug 16 16:09 perf.data-00002
  -rw------- 1 jolsa jolsa 5309144 Aug 16 16:09 perf.data-00003
  -rw------- 1 jolsa jolsa 2358268 Aug 16 16:09 perf.data-00004
  $ ./perf diff perf.data-0000*
  # Event 'cycles'
  #
  # Data files:
  #  [0] perf.data-00000 (Baseline)
  #  [1] perf.data-00001
  #  [2] perf.data-00002
  #  [3] perf.data-00003
  #  [4] perf.data-00004
  #
  # Baseline/0  Delta/1  Delta/2  Delta/3  Delta/4      Shared Object                                      Symbol
  # ..........  .......  .......  .......  .......  .................  ..........................................
  #
        37.70%   -0.17%   -0.42%   -0.24%   -0.31%  libc-2.15.so       [.] _IO_file_xsputn@@GLIBC_2.2.5        
        30.31%   +0.28%   +0.22%   +0.07%   +0.06%  yes                [.] main                                
        16.73%   +0.02%   +0.10%   -0.03%   +0.11%  libc-2.15.so       [.] __strlen_sse2                       
        14.22%   -0.30%   -0.10%   -0.31%   -0.14%  libc-2.15.so       [.] fputs_unlocked                      
         0.39%                              -0.01%  yes                [.] fputs_unlocked@plt                  
         0.06%                                      [kernel.kallsyms]  [k] system_call                         
         0.06%                     +0.01%           [kernel.kallsyms]  [k] __srcu_read_lock                    
         0.05%                     +0.01%           [kernel.kallsyms]  [k] __srcu_read_unlock                  
  ...

```

- `-M/--multi` option accepts size and time parameters, distinguished by following terminal character:
  **s** - seconds   (-M 10s)
  **m** - minutes   (-M 10m)
  **h** - hours     (-M 10h)
  **B** - bytes     (-M 10B)
  **K** - kilobytes (-M 10K)
  **M** - megabytes (-M 10M)

### Limitations

- minimal file size is 100K
- each file is started with initiall COMM/MMAP events, this could race with SAMPLE storage leaving some SAMPLES w/o process/mmap translations

### TODO

- move file storing into special thread inorder not to abuse main polling thread
- keep live COMM/MMAP info to save some time when creating new file and limit the race window described above

### Branch
- perf/core_file*
