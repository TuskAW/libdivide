# libdivide
[![Build Status](https://ci.appveyor.com/api/projects/status/github/ridiculousfish/libdivide?branch=master&svg=true)](https://ci.appveyor.com/project/kimwalisch/libdivide)

```libdivide.h```  is a header-only C/C++ library for optimizing integer division, it has both
a C API and a C++ API. This is a summary of how to use libdivide's testing tools to develop on libdivide itself.

See http://libdivide.com for more information on libdivide.

libdivide has 2 test tools:

* A verification utility **tester** used to help ensure that the division algorithm is correct.
* A benchmarking utility **benchmark** used to measure the speed increase.

## Build instructions

The tester and benchmark programs can be built using cmake and a recent C++ compiler
that supports C++11 or later. Optionally ```libdivide.h``` can also be installed to
```/usr/local/include```.

```bash
cmake .
make -j
sudo make install
```

## Tester binary

You can pass the **tester** binary one or more of the following arguments: ```u32```,
```s32```, ```u64```, ```s64``` to test the four cases (signed, unsigned, 32 bit, or 64 bit), or
run it with no arguments to test all four. The tester is multithreaded so it can test multiple
cases simultaneously. The tester will verify the correctness of libdivide via a set of randomly
chosen denominators, by comparing the result of libdivide's division to hardware division. It
may take a long time to run, but it will output as soon as it finds a discrepancy.

## Benchmark binary

You can pass the **benchmark** binary one or more of the following arguments: ```u32```,
```s32```, ```u64```, ```s64``` to compare libdivide's speed against hardware division.
**benchmark** tests a simple function that inputs an array of random numerators and a single
divisor, and returns the sum of their quotients.  It tests this using both hardware division, and
the various division approaches supported by libdivide, including vector division.

It will output data like this:

```bash
 #  system  scalar  scl_us  vector  vec_us   gener  algo
 1   5.733   0.849   0.580   0.431   0.431   1.663     0
 2   6.716   0.847   0.580   0.431   0.431   1.663     0
 3   6.687   1.425   1.427   1.862   1.444  22.156     1
 4   6.668   0.851   0.580   0.431   0.431   1.663     0
 5   6.697   1.425   1.425   1.837   1.425  22.156     1
...
```

It will keep going as long as you let it, so it's best to stop it when you are happy with the
denominators tested.  These columns have the following significance.  All times are in
nanoseconds, and lower is better.

```bash
     #:  The divisor that is tested
system:  Hardware divide time
scalar:  libdivide time, using scalar functions
scl_us:  libdivide time, using scalar unswitching functions
vector:  libdivide time, using vector functions
vec_us:  libdivide time, using vector unswitching
 gener:  Time taken to generate the divider struct
  algo:  The algorithm used.  See libdivide_*_get_algorithm
```

The benchmarking utility will also verify that each function returns the same value,
so "benchmark" is valuable for its verification as well.

## Contributing

Before sending in patches to libdivide, please run the tester to completion with all four types,
and the benchmark utility for a reasonable period, to ensure that you have not introduced a
regression.

Happy hacking!
