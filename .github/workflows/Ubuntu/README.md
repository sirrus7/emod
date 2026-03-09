# Ubuntu Docker Images

This directory contains Dockerfiles for building and running the DTK (Disease Transmission Kernel) on Ubuntu 22.04.

## Image Overview

| File | Base OS | Purpose |
|---|---|---|
| `Dockerfile.buildenv.ubuntu` | Ubuntu 22.04 | Compile DTK |
| `Dockerfile.sfts.ubuntu` | Ubuntu 22.04 | Run Science Feature Tests |
| `Dockerfile.runtime.ubuntu` | Ubuntu 22.04 | Run DTK simulations |

These images are built and pushed to GHCR via the `build_docker_images.yml` pipeline as `emod-ubuntu-buildenv`, `emod-ubuntu-testenv`, and `emod-ubuntu-runtime`.

---

## `Dockerfile.buildenv.ubuntu`

Python 3.13 (via `deadsnakes` PPA), SCons (via pip), and the system packages needed to compile DTK with GCC 11 and MPICH.

### System Packages

| Package | Purpose |
|---|---|
| `g++` | C++ compiler |
| `mpich libmpich-dev` | MPI runtime and headers |
| `libboost-all-dev` | Boost libraries |
| `libsnappy-dev` | Snappy compression |
| `liblz4-dev` | LZ4 compression |
| `libsqlite3-dev` | SQLite |
| `libbz2-dev` | bzip2 compression |
| `libssl-dev` | OpenSSL |
| `libreadline-dev` | Readline |
| `libffi-dev` | FFI |
| `liblzma-dev` | XZ/LZMA compression |
| `uuid-dev` | UUID |
| `tk-dev` | Tk |

---

## `Dockerfile.sfts.ubuntu`

Extends `buildenv` with additional packages for running Science Feature Tests.

### Additional System Packages

| Added | Reason |
|---|---|
| `pkg-config` | Required by `python-snappy` and `lz4` to locate native libraries during compilation |
| `libhdf5-dev` | Required by `h5py` to compile its C extension; not pulled in transitively on Ubuntu |

### pip Install Structure

pip packages are installed in four separate layers for better error isolation and Docker layer caching:

```
1. Scientific computing  — numpy scipy pandas matplotlib seaborn
2. Utilities             — bs4 hypothesis jsonmerge openpyxl python-dateutil ...
3. C extension packages  — h5py lz4 python-snappy
4. Test runner           — unittest-xml-reporting
```

---

## `Dockerfile.runtime.ubuntu`

Minimal Ubuntu 22.04 image for running DTK simulations (without build tooling).

### System Packages

| Package | Purpose |
|---|---|
| `python3.13 python3.13-dev python3.13-venv` | Python 3.13 runtime (deadsnakes PPA), set as default via `update-alternatives` |
| `libsnappy1v5` | Snappy compression runtime |
| `libc6-dev` | glibc headers |
| `tzdata` | Timezone data |
| `mpich` | MPI runtime |

