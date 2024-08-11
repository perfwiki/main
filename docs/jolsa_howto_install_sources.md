## Sources

Clone any kernel source git tree:
```sh
# linus git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux-2.6.git
# tip   git://git.kernel.org/pub/scm/linux/kernel/git/tip/tip.git
# acme  git://git.kernel.org/pub/scm/linux/kernel/git/acme/linux
# jolsa git://git.kernel.org/pub/scm/linux/kernel/git/jolsa/perf.git

$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux-2.6.git
```

## Dependencies

Packages you need to compile perf with all features (Fedora based):

- `gcc`
- `glibc-devel`
- `glibc-headers`
- `binutils-devel`
- `elfutils-libelf-devel`
- `elfutils-devel`
- `libunwind-devel`
- `audit-libs-devel`
- `slang-devel`
- `gtk2-devel`
- `numa-libs-devel`
- `python-devel`
- `perl-devel`
- `perl-ExtUtils-Embed`
- `numactl-devel`
- `asciidoc`
- `xmlto`
- `flex`
- `bison`

```sh
$ sudo yum install gcc binutils-devel elfutils-libelf-devel \
  elfutils-devel libunwind-devel audit-libs-devel slang-devel \
  gtk2-devel python-devel perl-devel perl-ExtUtils-Embed numactl-devel \
  asciidoc xmlto flex bison glibc-devel

...
```

## Build & Install

```sh
$ cd linux-2.6.git/tools/perf
$ make && make install
```

## Binaries
The perf is by default installed under user's $(HOME)

```sh
[jolsa@krava perf]$ ls -l ~/bin/perf 
-rwxr-xr-x 1 jolsa jolsa 8383047 Jun 20 15:43 /home/jolsa/bin/perf
[jolsa@krava perf]$ ls -l ~/libexec/perf-core/ ~/share/man/man1/

...
```
