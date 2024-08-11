## Example program

- ex.c
```sh
#define FOO(__f)        \
static int __f(int i)   \
{                       \
        while (i--);    \
}

FOO(krava_a);
FOO(krava_b);
FOO(krava_c);
FOO(krava_d);

static void krava_g(void)
{
        krava_a(50);
        krava_b(50);
        krava_c(50);
        krava_d(50);
}

int main(int argc, char **argv)
{
        while (1) {
                krava_a(200);
                krava_b(200);
                krava_c(200);
                krava_d(200);
                krava_g();
        }
}
```

- Makefile
```sh
ex: ex.o
```

## Measurements

- **krava_[abcd]** functions are taking 25% of the load

```sh
$ perf record  ./ex
^C[ perf record: Woken up 5 times to write data ]
[ perf record: Captured and wrote 1.200 MB perf.data (~52446 samples) ]
./ex: Interrupt
$ perf report --stdio
...
# Overhead  Command      Shared Object                                Symbol
# ........  .......  .................  ....................................
#
    26.41%       ex  ex                 [.] krava_d                         
    25.16%       ex  ex                 [.] krava_b                         
    24.03%       ex  ex                 [.] krava_a                         
    23.95%       ex  ex                 [.] krava_c                         
     0.18%       ex  ex                 [.] krava_g                         

```

- but how much **krava_g** function takes?
- we need parent info - **callchains**, so let's record them with **-g**

```sh
$ perf record -g -- ./ex
^C[ perf record: Woken up 7 times to write data ]
[ perf record: Captured and wrote 1.554 MB perf.data (~67889 samples) ]
./ex: Interrupt
```

- and say we want to report parent symbol with **-p krava_g**
```sh
$ perf report -p krava_g
Samples: 19K of event 'cycles', Event count (approx.): 17010569690
+  22.39%  ex  ex                 [.] krava_d                              [other]
+  20.11%  ex  ex                 [.] krava_c                              [other]
+  20.00%  ex  ex                 [.] krava_b                              [other]
+  18.80%  ex  ex                 [.] krava_a                              [other]
+   4.90%  ex  ex                 [.] krava_a                              krava_g
+   4.71%  ex  ex                 [.] krava_b                              krava_g
+   4.39%  ex  ex                 [.] krava_c                              krava_g
+   4.28%  ex  ex                 [.] krava_d                              krava_g
+   0.14%  ex  ex                 [.] krava_g                              krava_g
+   0.08%  ex  ex                 [.] main                                 [other]
```

Previous:

- parent symbol is displayed in the last column in above listing
- you can see load gets separated by parent **krava_g** and the rest **[other]**
- main based **krava_[abcd]** functions now eat only 20% now
- krava_g based **krava_[abcd]** functions eat ~20% as well (+- other trash)

Next:

- we can make the numbers more obvious and sort it by parent with **-s parent**

```sh
$ perf report -p krava_g -s parent --stdio
# Overhead  Parent symbol
# ........  .............
#
    81.54%  [other]      
            |          
            |--27.46%-- krava_d
            |          |          
            |          |--98.60%-- main
            |          |          __libc_start_main
            |          |          
            |           --1.40%-- __libc_start_main
            |          
            |--24.66%-- krava_c
            |          |          
            |          |--98.77%-- main
            |          |          __libc_start_main
            |          |          
            |           --1.23%-- __libc_start_main
            |          
            |--24.52%-- krava_b
            |          |          
            |          |--98.61%-- main
            |          |          __libc_start_main
            |          |          
            |           --1.39%-- __libc_start_main
            |          
            |--23.06%-- krava_a
            |          |          
            |          |--99.22%-- main
            |          |          __libc_start_main
            |          |          
            |           --0.78%-- __libc_start_main
             --0.30%-- [...]

    18.46%  krava_g      
            |          
            |--26.57%-- krava_a
            |          krava_g
            |          main
            |          __libc_start_main
            |          
            |--25.54%-- krava_b
            |          krava_g
            |          main
            |          __libc_start_main
            |          
            |--23.80%-- krava_c
            |          krava_g
            |          main
            |          __libc_start_main
            |          
            |--23.20%-- krava_d
            |          krava_g
            |          main
            |          __libc_start_main
            |          
            |--0.74%-- krava_g
            |          __libc_start_main
             --0.16%-- [...]
```
- **krava_g** takes ~20% plus we can see the load distribution for its child functions
