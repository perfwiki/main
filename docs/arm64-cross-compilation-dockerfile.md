# Arm64 cross-compilation Dockerfile
Save this as "Dockerfile" and follow the readme.

```Dockerfile
# Readme
# ======
#
# This is a Dockerfile that cross compiles an arm64 Perf from an x64
# host. It uses Ubuntu 20 as a base which has libc 2.31, which means
# the binary will run on any system that has libc 2.31+ (Provided there
# are no conflicting dependencies, but Ubuntu, Debian, Fedora etc are
# all likely to work).
#
# To build
# ========
#
# Replace <your_linux_checkout> and run the following commands. The Perf
# build and libtraceevent will be placed in the ./build folder.
#
# $ cd <this_folder>
# $ docker build -t perf-arm64 --network=host .
# $ docker run -v <your_linux_checkout>:/linux -v ./build:/build -v ./build-static:/build-static perf-arm64
#
# To copy and run the dynamic build
# =================================
#
# $ scp ./build/perf ./build/perf-iostat ./build/perf-archive <target_system>:~
# $ scp ./build/libtraceevent* <target_system>:/usr/lib/aarch64-linux-gnu/ # See NOTE below
# $ ssh <target_system>
# $ apt install <any missing library>
# $ ~/perf record ...
#
# To copy and run the static build
# ================================
# $ scp ./build-static/perf ./build-static/perf-iostat ./build-static/perf-archive <target_system>:~
# $ ssh <target_system>
# $ ~/perf record ...
#
# NOTE:
# If the target is more recent (Ubuntu 22.04+ for example), you should
# skip copying libtraceevent and apt install it instead.
#
# TODO:
# The Perf python library ends up with x86 stuff in the name, but this
# isn't really used by Perf anwyay so can be ignored for now.
#
FROM ubuntu:20.04
 
# Stop some packages prompting for user configuration
ENV DEBIAN_FRONTEND=noninteractive
 
# Completely replace sources.list
#
# Make sure regular sources are prefixed with amd64 so it doesn't try to
# pull arm64 packages where they don't exist. And add the ports.ubuntu
# sources for arm64.
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu focal main restricted universe multiverse" > /etc/apt/sources.list && \
    echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu focal-updates main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu focal-security main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports focal main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports focal-updates main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports focal-security main restricted universe multiverse" >> /etc/apt/sources.list
 
RUN dpkg --add-architecture arm64
RUN apt-get update && apt-get upgrade -y
 
# Native build stuff
RUN apt-get install -y \
    gcc-aarch64-linux-gnu g++-aarch64-linux-gnu \
    binutils-aarch64-linux-gnu flex \
    bison make \
    pkg-config-aarch64-linux-gnu python3 \
    python3-dev python3-setuptools \
    git dpkg-dev
 
# Arm64 Perf dependencies
RUN apt-get install -y \
    libelf-dev:arm64 libdwarf-dev:arm64 \
    libdw-dev:arm64 libaudit-dev:arm64 \
    libcap-dev:arm64 libelf-dev:arm64 \
    libunwind-dev:arm64 libzstd-dev:arm64 \
    libfl-dev:arm64 libbison-dev:arm64 \
    libpython3-dev:arm64 libslang2-dev:arm64 \
    libnuma-dev:arm64 libssl-dev:arm64 \
    libbz2-dev:arm64
 
# Build and install libtraceevent since there is no package on Ubuntu 20.04
WORKDIR /
RUN git clone --depth=1 https://git.kernel.org/pub/scm/libs/libtrace/libtraceevent.git/
WORKDIR /libtraceevent
RUN make CROSS_COMPILE=aarch64-linux-gnu- prefix=/usr/aarch64-linux-gnu \
    libdir_relative=lib pkgconfig_dir=/usr/lib/aarch64-linux-gnu/pkgconfig install
RUN ldconfig
 
# Install libtraceevent to the /build dir so it can be copied to the
# target system, then build both static and dynamic versions of Perf.
CMD make CROSS_COMPILE=aarch64-linux-gnu- prefix=/build \
    libdir_relative= pkgconfig_dir=/usr/lib/aarch64-linux-gnu/pkgconfig install && \
    cd /linux && \
    make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- O=/build -C tools/perf && \
    make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- O=/build-static LDFLAGS="-static" EXTRA_PERFLIBS="-lexpat" -C tools/perf
```
