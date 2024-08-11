## Description
This feature adds the possibility to define specific counters
by using arithmetic formulas and use them via **stat** command.

- Formula config file looks like:

```sh
set {
     events {
             CY = cycles
             IN = instructions
             BR = branches
     }   
     cpi      = CY / IN
     bdensity = IN / BR

     print cpi 
     print bdensity
}
```

The **set** defines set of counter that share same events.
Each **set** defines:

- events   - event string that would go into stat -e option
- counters - any number of counters based on above events

Each **event** and **counter** is defined as an assignment (via =)
to the name(tag), which could be later used within counter
formulas.

Each **counter** defines formula that produces the counter number.

- Formula grammar:

```sh
expr: '-' expr       | 
      expr '+' expr  | 
      expr '-' expr  | 
      expr '*' expr  | 
      expr '/' expr  | 
      value          | 
      name
```

where **name** could be any event or counter name(tag), like:
(considering the architectural events name patchset is included)

```sh
set { 
      events { 
              IN = instructions
              R0 = cpu/OFFCORE_RESPONSE_0,offcore_rsp=OFFCORE_RESPONSE.(DMND_IFETCH|LLC_MISS_LOCAL)/
      } 

      kinst     = IN / 1000
      llc_imiss = R0 / kinst

      print llc_imiss
} 
```

It's possible to enable counter to be displayed via **print**
label on separate line. Only counters marked like that
will be displayed to the user.

- Default configuration file is provided within perf installation:
```sh
cpi {
        events {
                CY = cycles:u
                IN = instructions:u
        }

        cpi = CY / IN

        print cpi
}

branch {
        events {
                IN = instructions:u
                BI = branch-instructions:u
                BM = branch-misses:u
        }

        branch-rate       = BI / IN
        branch-miss-rate  = BM / IN
        branch-miss-ratio = BM / BI

        print branch-rate
        print branch-miss-rate
        print branch-miss-ratio
}
```

- **perf stat** allows to specify formula configuration file via **-f** option
- user needs to specify set via **-e** option via **formula** keyword like formula-**set**

```sh
$ perf stat -e formula-cpi ls
```

## Example

- krava.conf
```sh
cpi {
        events {
                CY = cycles:u
                IN = instructions:u
        }

        cpi = CY / IN

        print cpi
}

branch {
        events {
                IN = instructions:u
                BI = branch-instructions:u
                BM = branch-misses:u
        }

        branch-rate       = BI / IN
        branch-miss-rate  = BM / IN
        branch-miss-ratio = BM / BI

        print branch-rate
        print branch-miss-rate
        print branch-miss-ratio
}
```

- get cpi set formulas:

```sh
$ ./perf stat -f ./krava.conf -e formula-cpi yes > /dev/null
^Cyes: Interrupt

 Performance counter stats for 'yes':

     5,504,661,193  cycles:u                 #    0.000 GHz                    
    15,795,271,321  instructions:u           #    2.87  insns per cycle        

       1.656520050 seconds time elapsed

        0.34850058 cpi                      
```

- get branch set formulas:

```sh
[jolsa@krava perf]$ ./perf stat -f ./krava.conf -e formula-branch yes > /dev/null
^Cyes: Interrupt

 Performance counter stats for 'yes':

    16,081,772,528  instructions:u           #    0.00  insns per cycle        
     2,806,823,146  branch-instructions:u                                      
            56,618  branch-misses:u          #    0.00% of all branches        

       1.688431091 seconds time elapsed

        0.17453444 branch-rate              
        0.00000352 branch-miss-rate         
        0.00002017 branch-miss-ratio        
```
