# Memory Bandwidth Benchmarks

## Overview
This repository intends to provides a set of benchmarks that can be used to measure the memory bandwidth performance of CPU's. In the initial release, we provide the de-facto memory bandwidth benchmark, [STREAM](https://www.cs.virginia.edu/stream/) [1] along with compilation and run scripts to obtain ideal performance on Intel(R) Processors.

## STREAM Overview
STREAM is a simple, synthetic benchmark designed to measure sustainable memory bandwidth (in MB/s) for four simple vector kernels: Copy, Scale, Add and Triad. Its source code is freely available [here](https://www.cs.virginia.edu/stream/FTP/Code/)

There are two categories created by STREAM benchmark author for citing memory bandwidth performance -- Standard and Tuned. Results obtained from the unmodified source code are considered as "Standard" whereas a "Tuned" category has been added to allow users or vendors to submit results based on modified source code. On Intel(R) processors, we don't need to modify the source code of the benchmark for optimal performance. We provide instructions to compile and run STREAM without any source code modifications. Hence, the performance results obtained would fall under the Standard category.

The general rule for STREAM is that each array must be at least 4x the size of the sum of all the last-level caches used in the run, or 1 million elements, whichever is larger.

## Pre-requisites
- Intel C Compiler: Performance of STREAM benchmark is dependent on the Compiler options used. Hence, we rely on the Intel C Compiler to generate the underlying non-temporal store instructions to achieve optimal performance on Intel CPU's.
- Linux environment: Currently, the makefile assume Linux OS environment.

## Compilation
- Ensure Intel C Compiler (icc) is available in your shell environment.
- Run `make`. This would generate the following binaries:
  - stream_avx.bin        => Targeted for Intel CPU's that support AVX ISA
  - stream_avx2.bin       => Targeted for Intel CPU's that support AVX2 ISA
  - stream_avx512.bin     => Targeted for Intel CPU's that support AVX512 ISA

Be default, the following STREAM configuration parameters are used in compiling the binaries:
- STREAM_TYPE = double
- STREAM_ARRAY_SIZE = 269000000 (this translates to about 2 GB per array of memory footprint)
- NTIMES = 100
- OFFSET = 0

Makefile supports the following options:
- size=<number_of_elements_per_array>
- cpu=<avx,avx2,avx512>
- rfo=1 forces to use regular cached stores instead of non-temporal stores
- help

Few examples:
- To compile STREAM benchmark only for Intel AVX512 CPU's, do: `make cpu=avx512`
- To compile STREAM benchmark for Intel AVX512 CPU's with each buffer containing 67108864 elements, do:  `make size=67108864 cpu=avx512`
- To explicitly use regular cached stores, do: `make rfo=1`

## Running STREAM
We provide a run script (`run.sh`) that can be used for benchmarking purposes. The run script does the following --

1.  Binary: Use the appropriate STREAM binary produced from the compilation step, i.e picks the highest supported ISA on your target system
2.  OpenMP settings: Sets the OMP_NUM_THREADS to number of physical cores on the system. KMP_AFFINITY (thread affininity control variable of Intel OpenMP runtime) set to compact pinning. Ignores Hyper-threading cores even if enabled on system.
3.  Store the results to a log file. Also, output relevant system info such as number of sockets, cores, threads, NUMA domains, memory sub-system etc. Running with sudo would result in more detailed info on memory subsystem as it parses output of `dmidecode`


[1]: McCalpin, John D., 1995: "Memory Bandwidth and Machine Balance in Current High Performance Computers", IEEE Computer Society Technical Committee on Computer Architecture (TCCA) Newsletter, December 1995.
