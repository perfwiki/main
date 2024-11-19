# Downloading and building the latest perf tools

The example below is for a Debian-based system, specifically Ubuntu 20.04 in
this case, although there is a package list also for Fedora.  We will need 4G of
disk space.

`git` is needed:
```sh
$ sudo apt-get install git
```

`perf` is in the Linux source tree so get that:
```sh
$ cd
$ mkdir git
$ cd git
$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```
Alternatively the source code of just the perf tool, and not the whole kernel
tree, is [archived here](https://www.kernel.org/pub/linux/kernel/tools/perf/).

Get the minimum tools to build it:
```sh
$ sudo apt-get install build-essential flex bison
```

It should be possible to build `perf` at this stage but it will be missing
essential features.  Add some more development libraries:
```sh
$ sudo apt-get install libelf-dev libnewt-dev libdw-dev libaudit-dev libiberty-dev libunwind-dev libcap-dev libzstd-dev libnuma-dev libssl-dev python3-dev python3-setuptools binutils-dev gcc-multilib liblzma-dev
```

Note, with v5.19 and earlier, python3-distutils was used instead of
python3-setuptools, but that was changed in [this
commit](http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ee87a0841aa538ab7ad49cf5679ac5ea2682c909).

Aside: example packages for Fedora instead of Ubuntu:
```sh
sudo yum install flex bison gcc make elfutils-libelf-devel elfutils-devel libunwind-devel python-devel libcap-devel slang-devel binutils-devel numactl-devel openssl-devel
```

To build `perf` (with script bindings for python3 instead of python2) and put it
in `~/bin/perf` :
```sh
$ cd ~/git/linux
$ PYTHON=python3 make -C tools/perf install
```

To use `~/bin/perf`, ~/bin must be in $PATH.  In Ubuntu, that is added
automatically when a user logs in if the ~/bin directory exists (refer
~/.bashrc). If it is not in $PATH, log out and in again.  We can echo $PATH to
check:
```sh
$ echo $PATH
/home/user/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
$ which perf
/home/user/bin/perf
```
