# perf lock contention

The `perf lock` command is to trace kernel lock tracepoints.
`CONFIG_LOCKDEP` enables a couple of lock tracepoints but it's for
debugging and adds a lot of overheads.  So  most distros don't ship
the kernel with the config.  Another set of tracepoints are focusing
on the contended locks and it's always available and has lower overhead.
The `perf lock contention` command is to deal with the contention
tracepoints only.

## Analyzing kernel lock contention

The `perf lock contention` (shortly `perf lock con`) can run with or
without BPF.  Without BPF it requires two steps to record the data and
to analyze the data.  That is, you must save all tracepoints data to a
file first using `perf lock record` command.  This might be good when
users want to analyze the data multiple times probably in different
ways.

Running it with BPF (`-b` option) enables you to do live analysis
without the data copying.  The BPF aggregates the data in the kernel
and the tool just displays the result.  Also BPF can do more things
like accessing the owner info (if available) and symbolizing lock
addresses (in some cases).

There are 3 modes in the tool to aggregate the result by stack trace,
task or the lock.  Newer versions of the tool will likely have more
features.  This document will use BPF and tool version 6.2.

### Common options

These options are to control the collection target.

* `-a` (`--all-cpus`) : enable system-wide collection from all-CPUs.
* `-C` (`--cpu`) NUM : collect lock contention from the given CPUs only.
* `-p` (`--pid`) NUM : collect lock contention from the given process only.
* `--tid` NUM : collect lock contention from the given thread only.
    Unfortunately the `-t` option was already taken.
* `-G` (`--cgroup-filter`) NAME : collect lock contention from the given cgroup only. (from v6.7)

These options are used for all aggregation modes.

* `-b` (`--use-bpf`) : use BPF programs to collect lock contention stats.
* `-E` (`--entries`) NUM : limit the output by this many entries
* `-F` (`--field`) NAMES : select the output columns (comma-separate values).
    Users can customize the output.  Available values are `contended`, `wait_total`,
	`wait_max`, `wait_min`, `avg_wait`.  The default values are
	“contended,wait_total,wait_max,avg_wait”.
* `-k` (`--key`) NAME` : select the key to sort the entries.  Available values
    are the same with the `-F` option.  The default value is “wait_total”.
* `-v` (`--verbose`) : show verbose output including debug stats.

There are filter options when you have information on target locks (usually from the previous runs).

* `-L` (`--lock-filter`) ADDR : limit collection for the given lock only.
    It also accepts a symbolic name if the lock has a symbol (for global locks).
	Note that ‘mmap_lock’, ‘rq_lock’ and ‘siglock’ are not global locks (they got
	the symbol by post-processing) so they cannot be used for the filter option.
* `-Y` (`--type-filter`) FLAGS : collect contention stats for the given lock
    (access) types.  Available values are `spinlock`, `rwlock`, `rwlock:R`,
	`rwlock:W`, `mutex`, `rwsem`, `rwsem:R`, `rwsem:W`, `semaphore`, …
	(anything that appears in the type column).

### By stack trace

The default behavior of the tool collects the contention stat by stack
trace (in kernel only) and shows the key function for each entry.

```
# perf lock con -ab sleep 1
 contended   total wait     max wait     avg wait         type   caller

         2     27.49 us     15.86 us     13.74 us     spinlock   raw_spin_rq_lock_nested+0x1b
         2     24.58 us     14.10 us     12.29 us     rwlock:W   ep_done_scan+0x2d
         1     19.18 us     19.18 us     19.18 us     rwlock:W   do_epoll_wait+0x47b
         1     16.65 us     16.65 us     16.65 us     spinlock   tick_do_update_jiffies64+0x25
         1     13.82 us     13.82 us     13.82 us     spinlock   __queue_work+0x174
         2     12.80 us      9.55 us      6.40 us     spinlock   process_one_work+0x1f0
         1     10.31 us     10.31 us     10.31 us     rwlock:R   ep_poll_callback+0x2d
```

It uses system wide mode (`-a`) with BPF (`-b`).  I think these are
options you want to use mostly.  The ‘sleep 1’ part is just to run
the tool for 1 second.  It’s a common pattern for the perf tools.

The first entry is a spinlock from `raw_spin_rq_lock_nested()` kernel
function and it’s contended twice.  The caller was chosen after
collecting the stack trace.  It skips internal lock functions and
picks whatever follows them immediately.


The following command shows the whole stack traces (`-v`) for top 3
lock contentions (`-E 3`).

```
# perf lock con -abv -E3 sleep 1
Looking at the vmlinux_path (8 entries long)
symsrc__init: cannot get elf header.
Using /proc/kcore for kernel data
Using /proc/kallsyms for symbols
 contended   total wait     max wait     avg wait         type   caller

         1     26.10 us     26.10 us     26.10 us     spinlock   raw_spin_rq_lock_nested+0x1b
                        0xffffffff9d0407c0  _raw_spin_lock+0x30
                        0xffffffff9c6d9cfb  raw_spin_rq_lock_nested+0x1b
                        0xffffffff9c6e3437  _raw_spin_rq_lock_irqsave+0x17
                        0xffffffff9c6f1de2  _nohz_idle_balance.isra.0+0x1a2
                        0xffffffff9c6f7c48  do_idle+0x38
                        0xffffffff9c6f8099  cpu_startup_entry+0x19
                        0xffffffff9d02eaeb  rest_init+0xcb
                        0xffffffff9e673da2  arch_call_rest_init+0xa
         1     22.52 us     22.52 us     22.52 us     spinlock   tick_do_update_jiffies64+0x25
                        0xffffffff9d0407c0  _raw_spin_lock+0x30
                        0xffffffff9c7657c5  tick_do_update_jiffies64+0x25
                        0xffffffff9c765903  tick_sched_do_timer+0x93
                        0xffffffff9c765943  tick_sched_timer+0x33
                        0xffffffff9c75237f  __hrtimer_run_queues+0x10f
                        0xffffffff9c75333e  hrtimer_interrupt+0xfe
                        0xffffffff9c6693fc  __sysvec_apic_timer_interrupt+0x7c
                        0xffffffff9d02d0d9  sysvec_apic_timer_interrupt+0x99
         1     22.37 us     22.37 us     22.37 us     rwlock:R   ep_poll_callback+0x2d
                        0xffffffff9d040555  _raw_read_lock_irqsave+0x45
                        0xffffffff9c9bc81d  ep_poll_callback+0x2d
                        0xffffffff9c702813  __wake_up_common+0x73
                        0xffffffff9c7029a2  __wake_up_common_lock+0x82
                        0xffffffff9cf10f25  unix_write_space+0x55
                        0xffffffff9cda6343  sock_wfree+0x93
                        0xffffffff9cf17af4  unix_destruct_scm+0x84
                        0xffffffff9cdaecc0  skb_release_head_state+0x20

=== output for debug ===

bad: 0, total: 14
bad rate: 0.00 %
histogram of failure reasons
       task: 0
      stack: 0
       time: 0
       data: 0
```
The debug output at the end might look different as it’s changed recently.

Here is the list of relevant options:

* `--max-stack` NUM : maximum depth of stack trace to collect.  The default value is 8.
* `--stack-skip` NUM : how many entries to skip before collecting the stack trace.  The default value is 4.

If you know a specific lock is very contended, you can use the lock
filter (`-L ffff9f88c0248000`) to see the contention for the lock only.
To get the address or symbol name (if available), run the tool with `-l`
option to output them first.  See below (“By lock” section).

```
# perf lock con -ab -L ffff9f88c0248000 sleep 1
 contended   total wait     max wait     avg wait         type   caller

     39412      1.58 s     123.50 us     40.15 us     spinlock   __cache_free_alien+0x6c
```

In this example, all the contention came from the same stack trace and
that’s why it showed a single entry.  But it could have multiple
entries if the contention has diverse paths.

Or you can pass a lock name (`-L jiffies_lock`).

```
# perf lock con -ab -L jiffies_lock sleep 1
 contended   total wait     max wait     avg wait         type   caller

         3     24.47 us     12.09 us      8.16 us     spinlock   tick_irq_enter+0xf0
```

### By task

It’s possible to collect lock stats by task using the `-t` (`--threads`) option.

```
# perf lock con -abt sleep 1
 contended   total wait     max wait     avg wait          pid   comm

         6    173.91 us    114.87 us     28.99 us         7738   chrome
         1    124.70 us    124.70 us    124.70 us        54858   kworker/4:0
         1     41.45 us     41.45 us     41.45 us         7740   ThreadPoolForeg
         1     19.41 us     19.41 us     19.41 us         7742   ThreadPoolForeg
         1      4.25 us      4.25 us      4.25 us            0   swapper
         1      2.25 us      2.25 us      2.25 us         7733   Chrome_ChildIOT
         1      2.24 us      2.24 us      2.24 us         5516   pipewire-pulse
         1       900 ns       900 ns       900 ns        56628   kworker/4:1
```

You can only see contentions for mutex using the `-Y mutex` option.

```
# perf lock con -abt -Y mutex sleep 1
 contended   total wait     max wait     avg wait          pid   comm

         2    346.15 us    203.47 us    173.08 us        60409   kworker/7:0
         2    294.77 us    164.66 us    147.38 us        55417   kworker/7:2
```

The perf tool v6.3 added the `-o` option to track lock owners instead
of waiters.  This is only available with BPF.

```
# perf lock con -abto -Y mutex sleep 1
 contended   total wait     max wait     avg wait          pid   comm

         1    199.45 us    199.45 us    199.45 us      1091507   kworker/2:0
         1    149.16 us    149.16 us    149.16 us         3583   NetworkManager
       146     63.42 us     12.50 us       434 ns           -1   Unknown
```

You can get the stack trace of the owner using PID like with this script.

```
# perf lock con -abtoq -Y mutex -x, sleep 1 2>&1 | cut -d, -f5 | tr -d ' ' | \
  awk '{ system(“cat /proc/$$1/stack”); }'
```

### By lock

Also you can get per-lock contention stats using the `-l` (lower L, also
with `--lock-addr`) option.

```
# perf lock con -abl sleep 1
 contended   total wait     max wait     avg wait            address   symbol

        15      2.57 ms    187.89 us    171.09 us   ffffa64fdb3f11b8    (mutex)
         9     66.01 us     10.37 us      7.33 us   ffffa105becb45c0   mmap_lock (rwsem)
         7     43.95 us      9.12 us      6.28 us   ffffa105bf7f45c0   mmap_lock (rwsem)
        12      3.28 us       740 ns       273 ns   ffff9f88cf26a660   mmap_lock (rwsem)
         1      2.27 us      2.27 us      2.27 us   ffffa105bda345c0    (rwlock)
         2      2.03 us      1.46 us      1.01 us   ffff9f88cf26a160    (mutex)
         1      1.91 us      1.91 us      1.91 us   ffffffff92a06e00   jiffies_lock (spinlock)
         1      1.78 us      1.78 us      1.78 us   ffffa0060d8745c0    (mutex)
         1      1.50 us      1.50 us      1.50 us   ffffffff92ecdad0   input_pool (spinlock)
         1      1.16 us      1.16 us      1.16 us   ffffa0060e7345c0    (spinlock)
         1       240 ns       240 ns       240 ns   ffffffff92e99800   kernfs_open_file_mutex (mutex)
         1       220 ns       220 ns       220 ns   ffff9f88cf9c53e0    (spinlock)
```

The symbols are displayed when found in the kallsysms and followed by
lock type name.  This output can be useful to apply filters for other
modes.

The perf tool v6.3 added more symbols for dynamic locks like "mmap_lock"
and "siglock" as well as some global locks which lack symbols like
"rq_lock".  This is done by BPF so you need the `-b` option.

### By cgroup

You can also get per-cgroup lock contention stats using `--lock-cgroup` option.

```
# perf lock con -ab --lock-cgroup -E 2 sleep 3
   contended   total wait     max wait     avg wait   cgroup

         214      9.24 ms    338.39 us     43.18 us   /
         782      1.56 ms     23.21 us      2.00 us   /sys
```

You can also use other filter options (like `-L` or `-Y`) together to
limit the target lock if needed.

The perf tool v6.7 added this option.  Please check if you are using
the recent version.

## With vs. without BPF

Using BPF for lock contention analysis is good for quick live
debugging since it’d be more efficient.  But as it doesn’t save the
result, each run might report different data depending on the system
characteristics.  And the BPF can give more detailed information about
the lock because it can access kernel internals.  But the BPF programs
should be written with care to handle multiple kernel versions
properly since some kernel data structure it accesses (like owner)
changed over time.

These options work only when BPF is used:

* `--map-nr-entries` NUM : max number of BPF map entries.
    This controls memory usage of BPF maps.  The default is 16384
	(which means around 2~3MB of memory is used by the BPF maps with
	the default options).

To use this tool without BPF, you need to run perf lock record command
first.  But it’d cause some disk IO unless it’s directed to a pipe.
Also saving the trace data in a ring buffer and then to a file can
cause data loss.  Running perf lock contention command on the save
data would give a consistent result but it has a limited functionality
than the BPF.

Note that perf lock record and perf lock report used to work with
lockdep - kernel lock dependency checker - which uses a different set
of tracepoints.  It’d not be recommended to run this command when the
kernel enabled the lockdep because it can confuse the tool.

The collection target options like `-a`, `-C`, `-p` and `--tid` should be
used during the record.  Then the rest will work just like the BPF case.
