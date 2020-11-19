## Introduction
The OSHMPI project provides an OpenSHMEM 1.4 implementation on top of MPI-3.
MPI is the standard communication library for HPC parallel programming. OSHMPI
provides a lightweight OpenSHMEM implementation on top of the portable MPI-3
interface and thus can utilize various high-performance MPI libraries on
HPC systems.


## Getting Started
#### 1. Installation
The simplest way to configure OSHMPI is using the following command-line:
```
./configure --prefix=/your/oshmpi/installation/dir CC=/path/to/mpicc CXX=/path/to/mpic++
```
Once you are done configuring, you can build the source and install it using:
```
make
make install
```

#### 2. Test
OSHMPI contains some simple test programs under the `tests/` directory. To check
if OSHMPI built correctly:
```
cd tests/ && make check
```
For comprehensive test, we recommend using external test suites such as the
**SOS Test Suite** at https://github.com/openshmem-org/tests-sos.

#### 3. Compile an OpenSHMEM program with OSHMPI
Compile a C program `test.c`:
```
/your/oshmpi/installation/dir/bin/oshcc -o test test.c
```
Compile a C++ program `test.cpp`:
```
/your/oshmpi/installation/dir/bin/oshc++ -o test test.cpp
```

#### 4. Execute an OSHMPI program
OSHMPI uses the **mpiexec** command to execute an OSHMPI program.
```
/path/to/mpiexec -np 2 ./test
```
For more information about the mpiexec command, please check the MPI
library's documentation.


## Support
If you have problems or need any assistance about the OSHMPI
installation and usage, please contact oshmpi-users@lists.mcs.anl.gov mailing
list.


## Bug Reporting
If you have found a bug in OSHMPI, please contact
oshmpi-users@lists.mcs.anl.gov mailing list, or create an issue ticket on
github: https://github.com/pmodels/oshmpi.  If possible, please try to
reproduce the error with a smaller application or benchmark and send
that along in your bug report.


## Configure Options
    
## Environment Variables
#### OpenSHMEM standard environment variables
  - **SHMEM_SYMMETRIC_SIZE** (default 128 MiB)   
      Number of bytes to allocate for symmetric heap. The size value can be
      either number of bytes or scaled with a suffix of
          'K' or 'k' for kilobytes (B * 1024),
          'M' or 'm' for Megabytes (KiB * 1024)
          'G' or 'g' for Gigabytes (MiB * 1024)
          'T' or 't' for Terabytes (GiB * 1024)

  - **SHMEM_DEBUG** (0|1, default 0)   
      If defined enables debugging messages from OpenSHMEM runtime. It is
      invalid when configured with --enable-fast (see `--enable-fast`).

  - **SHMEM_VERSION** (0|1, default 0)   
      If defined prints SHMEM library version at start_pes(),
      shmem_init(), or shmem_init_thread().

  - **SHMEM_INFO** (0|1, default 0)   
      If defined prints SHMEM environment variables at stdout.

#### OSHMPI environment variables
  - **OSHMPI_VERBOSE** (0|1, default 0)   
      If defined prints both SHMEM and OSHMPI environment variables at stdout.

  - **OSHMPI_AMO_OPS**   
      Defines the atomic operations used in the SHMEM program. If all PEs issue
      concurrent atomics only with the same operation, or with the same operation
      and fetch, then OSHMPI can directly utilize MPI accumulate operations.
      This is because, MPI grantees the atomicity of accumulate operations
      only with `same_op` or `same_op_no_op`. The default value of OSHMPI_AMO_OPS
      are  "cswap,finc,inc,fadd,add,fetch,set,swap,fand,and,for,or,fxor,xor".
      Arbitrary combination of the above operations can be given at execution
      time.

      The variable can be set only with configure `--enable-amo=auto` (see --enable-amo).

  - **OSHMPI_ENABLE_ASYNC_THREAD** (0|1, default 0)   
      Runtime control of asynchronous progress thread for atomic active message.
      When atomics cannot be directly translated to MPI accumulates (see OSHMPI_AMO_OPS),
      each atomic operations is issued via active-message based on the MPI two
      sided routines (i.e., MPI send/receive). If OSHMPI_ENABLE_ASYNC_THREAD
      is defined an internal thread is enabled to make asynchronous progress
      for the active message communication.

      The variable can be set only with configure `--enable-async-thread=runtime`
      (see --enable-async-thread) and `--enable-threads=multiple` (see --enable-thread).


## Debugging Options
- Enable debugging flag by setting the configure option `--enable-g`.

- Set environment variable SHMEM_DEBUG=1 to print OSHMPI internal debugging message.


## Test Framework
OSHMPI uses SOS Test Suite (https://github.com/openshmem-org/tests-sos) for
correctness test. The automatic test framework performs SOS tests on Ubuntu-64bit
platforms and uses MPICH-3.3rc1 and Open MPI 3.0 as the MPI provider. The test
status can be found at:
https://www.mcs.anl.gov/project/oshmpi/jenkins/

## Known Issues
1. Some OpenSHMEM features are not supported in OSHMPI.
  - **Context**: 
    OSHMPI cannot create separate or shared context on top of MPI interfaces.
    This feature may be enabled after the MPI end-point concept is accepted
    at MPI forum and implemented in MPI libraries. Current version always
    returns `SHMEM_NO_CTX` error at `shmem_ctx_create()`; `shmem_ctx_destroy()`
    is a no-op.

2. OSHMPI does not provide Fortran APIs.

3. OSHMPI may not work on 32-bit platforms. This is because some internal routines
   rely on 64-bit integer, e.g., `shmem_set_lock()`, `shmem_clear_lock()`, `shmem_test_lock`.

4. OSHMPI may not correctly initialize symmetric data on OSX platform.
