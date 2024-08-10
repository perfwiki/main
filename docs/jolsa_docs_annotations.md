## ANNOTATIONS

TODO: description & examples

The perf annotations is based on **objdump** tool from binutils package.

## BINUTILS RESTRICTIONS

Annotations are based on debuginfo, which on most (Fedora) distributions
come in separated *-debuginfo packages. Unfortunately **objdump** does not
connect binaries with its debuginfo counterparts.

The result is that for binaries with separated debuginfo you'll not
see annotations in **perf**.

### WORKAROUND

There's a following workaround, that merges debuginfo back to the
binary. Such binary will display proper annotations in perf.

The binary we want to annotate properly is **ls**.
- find out the debuginfo holder:
```sh
$ rpm -ql coreutils-debuginfo | grep ls.debug
/usr/lib/debug/usr/bin/ls.debug
```

- **OPTIONAL** if your distribution uses prelink, you need to undo it:
```sh
$ cp /bin/ls .
$ prelink -u ls
```

- create the **ls-di** binary with debuginfo merged:
```sh
$ eu-unstrip -o ls-di ./ls /usr/lib/debug/usr/bin/ls.debug
$ file ls-di
ls-di: ELF 64-bit LSB executable, x86-64, version 1 (SYSV) [SNIP] not stripped
```

- clean the build-id cache, since the **ls** share same build-id with **ls-di**:
```sh
$ perf buildid-cache -r ls
# or more efectively:
$ rm -rf ~/.debug/
```

- enjoy your annotations via:
```sh
$ perf record ./ls-di [SNIP]
$ perf annotate [FUNC]
$ perf report
```
