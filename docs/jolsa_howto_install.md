perf tool comes as a single package, on Fedora run:

```sh
$ sudo yum install perf
Resolving Dependencies
--> Running transaction check
---> Package perf.x86_64 0:3.8.13-100.fc17 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==================================================================================================================================
 Package                   Arch                        Version                                 Repository                    Size
==================================================================================================================================
Installing:
 perf                      x86_64                      3.8.13-100.fc17                         updates                      588 k

Transaction Summary
==================================================================================================================================
Install  1 Package

Total download size: 588 k
Installed size: 1.3 M
Is this ok [y/N]: y
Downloading Packages:
perf-3.8.13-100.fc17.x86_64.rpm                                                                            | 588 kB  00:00:00     
Running Transaction Check
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing : perf-3.8.13-100.fc17.x86_64                                                                                    1/1 
  Verifying  : perf-3.8.13-100.fc17.x86_64                                                                                    1/1 

Installed:
  perf.x86_64 0:3.8.13-100.fc17

Complete!

$ which perf
/bin/perf
```
