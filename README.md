# High-Performance Tensor Transpose library #

HPTT is a high-performance C++ library for out-of-place tensor transpositions of the general form: 

![hptt](https://github.com/springer13/hptt/blob/c++/misc/equation.png)

where A and B respectively denote the input and output tensor;
<img src=https://github.com/springer13/hptt/blob/c++/misc/pi.png height=16px/> represents the user-specified
transposition, and 
<img src=https://github.com/springer13/hptt/blob/c++/misc/alpha.png height=14px/> and
<img src=https://github.com/springer13/hptt/blob/c++/misc/beta.png height=16px/> being scalars
(i.e., setting <img src=https://github.com/springer13/hptt/blob/c++/misc/beta.png height=16px/> != 0 enables the user to update the output tensor B).

# Key Features

* Multi-threading support
* Explicit vectorization
* Auto-tuning (akin to FFTW)
    * Loop order
    * Parallelization
* Multi architecture support
    * Explicitly vectorized kernels for (AVX and ARM)
* Support float, double, complex and double complex data types

# Install

Clone the repository into a desired directory and change to that location:

    git clone https://github.com/springer13/hptt.git
    cd hptt
    export CXX=<desired compiler>

Now you have several options to build the desired version of the library:

    make avx
    make arm
    make scalar

This should create 'libhptt.so' inside the ./lib folder.


# Getting Started

Please have a look at the provided benchmark.cpp.

In general HPTT is used as follows:

    #include <hptt.h>

    // allocate tensors
    float A* = ...
    float B* = ...

    // specify permutation and size
    int dim = 6;
    int perm[dim] = {5,2,0,4,1,3};
    int size[dim] = {48,28,48,28,28};

    // create a plan (shared_ptr)
    auto plan = hptt::create_plan( perm, dim, 
                                   alpha, A, size, NULL, 
                                   beta,  B, NULL, 
                                   hptt::ESTIMATE, numThreads);

    // execute the transposition
    plan->execute();

The example above does not use any auto-tuning, but solely relies on HPTT's
performance model. To active auto-tuning, please use hptt::MEASURE, or
hptt::PATIENT instead of hptt::ESTIMATE.

# Requirements

You must have a working C++ compiler with c++11 support. I have tested HPTT with:

* Intel's ICPC 15.0.3, 16.0.3, 17.0.2
* GNU g++ 5.4, 6.2, 6.3

# Benchmark

The benchmark is the same as the original TTC benchmark [benchmark for tensor transpositions](https://github.com/HPAC/TTC/blob/master/benchmark).

You can compile the benchmark via:

    cd benchmark
    make

Before running the benchmark, please modify the number of threads and the thread
affinity within the benchmark.sh file. To run the benchmark just use:

    ./benshmark.sh

This will create hptt_benchmark.dat file containing all the runtime information
of HPTT and the reference implementation.

# Performance Results

![hptt](https://github.com/springer13/hptt/blob/c%2B%2B/benchmark/bw.png)

See [(pdf)](https://arxiv.org/abs/1704.04374) for details.

# TODOs

* Add explicit vectorization for IBM power
* Add explicit vectorization for complex types

# License

This project is under LGPLv3 for now. If this license is too restrictive for you,
please feel free to contact me via email (springer@aices.rwth-aachen.de).

# Citation

In case you want refer to HPTT as part of a research paper, please cite the following
article [(pdf)](https://arxiv.org/abs/1704.04374):
```
@article{springer2017a,
   author      = {Paul Springer and Tong Su and Paolo Bientinesi},
   title       = {{HPTT}: A High-Performance Tensor Transposition C++ Library},
   archivePrefix = "arXiv",
   eprint = {1704.04374},
   primaryClass = "quant-ph",
   journal     = {CoRR},
   year        = {2017},
   issue_date  = {April 2017},
   url         = {https://arxiv.org/abs/1704.04374}
}
``` 
