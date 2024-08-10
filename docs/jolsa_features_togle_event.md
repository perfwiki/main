## Branch
- perf/core_toggle

## Description
Adding perf interface that allows to create **toggle** events,
which can enable or disable another event. Whenever the toggle
event is triggered (has overflow), it toggles another event
state and either **starts or stops** it.

The goal is to be able to create toggling tracepoint events
to enable and disable HW counters, but the interface is generic
enough to be used for any kind of event.

### Interface

There are changes for both kernel and perf tool part.

### Kernel
The interface to create a toggle event is similar as the one
for defining event group. Use perf syscall with:

```sh
int sys_perf_event_open(struct perf_event_attr *attr,
                        pid_t pid, int cpu, int group_fd,
                        unsigned long flags)

flags    - PERF_FLAG_TOGGLE_ON or PERF_FLAG_TOGGLE_OFF
group_fd - event (or group) fd to be toggled
```

Created event will toggle **ON(start)** or **OFF(stop)** the event
specified via group_fd.

Obviously this way it's not possible for toggle event to
be part of group other than group leader. This is where
you need to use **PERF_EVENT_IOC_SET_TOGGLE ioctl**.

This ioctl has 2 goals:
- allowing the toggle event being part of the group
- allowing to define toggle setting after event is created

```sh
u64 args[2] = { toggled_fd, flag };
err = ioctl(fd, PERF_EVENT_IOC_SET_TOGGLE, args);
```

Where:
```sh
toggled_fd - is file description of the event we want to toggle
flag       - is one of PERF_FLAG_TOGGLE_ON|PERF_FLAG_TOGGLE_OFF
err        -  0 when successful
             -1 otherwise with errno:
                EBUSY  - event has already toggled event defined
                EFAULT - could not copy user data
                EINVAL - wrong data
```

### Perf Tool
The support for toggling events is added into record and stat commands.
    
The toggling events are defined via **on/off terms**, assigned with the name
of the event they should toggle.

Toggling events are define within -e option using on/off terms, like
```sh
-e 'cycles,irq_entry/on=cycles/,irq_exit/off=cycles/'
```

Meaning:
- irq_entry **toggles on** (starts) cycles, and irq_exit **toggled off** (stops) cycles.
- cycles is started as **paused**

## Examples

#### Example - using k(ret)probes

- Define toggle(on/off) events:
```sh
# perf probe -a fork_entry=do_fork
# perf probe -a fork_exit=do_fork%return
```

- Following record session samples only within do_fork function:
```sh
# perf record -g -e '{cycles,cache-misses}:k,probe:fork_entry/on=cycles/,probe:fork_exit/off=cycles/' \ 
  perf bench sched messaging
```

- Following stat session measure cycles within do_fork function:
```sh
# perf stat -e '{cycles,cache-misses}:k,probe:fork_entry/on=cycles/,probe:fork_exit/off=cycles/' \ 
  perf bench sched messaging

# Running sched/messaging benchmark...
# 20 sender and receiver processes per group
# 1 groups == 40 processes run

     Total time: 0.073 [sec]

 Performance counter stats for './perf bench sched messaging -g 1':

        20,935,464 cycles                    #    0.000 GHz
            18,897 cache-misses
                40 probe:fork_entry
                40 probe:fork_exit

       0.086319682 seconds time elapsed

```

#### Example - using u(ret)probes

- Sample program:
```sh
void krava(void)
{
        asm volatile ("nop; nop");
}

int main(void)
{
        krava();
        return 0;
}
```

- Define toggle(on/off) events:
```sh
# perf probe -x ./ex entry=krava
# perf probe -x ./ex exit=krava%return
```

- Following stat session measure instructions within krava function:
```sh
# perf stat -e instructions:u,probe_ex:entry/on=instructions/,probe_ex:exit/off=instructions/ ./ex

    Performance counter stats for './ex':

                 9 instructions:u            #    0.00  insns per cycle 
                 1 probe_ex:entry
                 1 probe_ex:exit

       0.000556743 seconds time elapsed
```

- Following stat session measure cycles, instructions and cache-misses within krava function:
```sh
# perf stat -e '{cycles,instructions,cache-misses}:u,probe_ex:entry/on=cycles/,probe_ex:exit/off=cycles/' ./ex

     Performance counter stats for './ex':

           2,068 cycles                    #    0.000 GHz
               9 instructions              #    0.00  insns per cycle 
               0 cache-misses
               1 probe_ex:entry
               1 probe_ex:exit

     0.000557504 seconds time elapsed
```

#### Example - Measure interrupts cycles
```sh
# ./perf stat -e 'cycles,cycles/name=cycles_irq/,irq:irq_handler_entry/on=cycles_irq/,irq:irq_handler_exit/off=cycles_irq/' -a sleep 10

   Performance counter stats for 'sleep 10':

      50,680,084,994 cycles                    #    0.000 GHz                     [100.00%]
             652,690 cycles_irq                #    0.000 GHz
                  33 irq:irq_handler_entry                                        [100.00%]
                  33 irq:irq_handler_exit

        10.002084400 seconds time elapsed
```
