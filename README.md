# neighbor_hash_map

This repo contains codes and steps necessary to reproduce the artifacts for our paper titled **NeighborHash : A Faster HashTable and Key-Value Store Serving in Recommendation System**

## Setting up the hardware

We conducted tests in the paper using Intel 5218 CPUs. In fact, any hardware that supports AVX512 can be used for reproduction, but the sresult may not necessarily match the results presented in the paper.

## Software Requirements

* linux x86_64 >= 4.9.0
* Hugepage (2 GiB) support
* gcc >= 11.4.0 or clang >= 16.800.6
* Bazel >= 6.3.0

## Build benchmark

```bash
git clone https://github.com/slow-steppers/NeighborHash
cd NeighborHash
bazel build -c opt --config=neighbor_hugepage --config=neighbor_simd //testing:all
```

## Run benchmarks

Our benchmark implementation utilizes the `google benchmark` framework, which allows selecting benchmarks to run using regular expressions.
The regular expression for running random access is: `BM_RandomAccess<.*HashMapType.*>/DS/SQR/LF`.
For instance, to run benchmarks for all scalar hashmaps with a 1M DatasetSize, SQR=90, and LF=80, execute the following code:

```bash
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/hash_map_benchmark --benchmark_filter="BM_RandomAccess<.*Scalar.*uniform>/1048576/90/79"
```


We have integrated the following components into our benchmark:
- `NeighborHash`
- `std::unordered_map`
- `LinearProbing`
- `BucketingSIMD_16x16`
- `ankerl::unordered_dense::map`
- `absl::flat_hash_map`
- `ska::bytell_hash_map`
- `emhash7::HashMap`
- `tsl::hopscotch_map`
- Native array (Random Access)


For ease of comparison, we have categorized each hashmap into the following classes: Scalar, IntraVec, Vec, MultiThreading.
The candidate values for each component of the expression are as follows:
- SQR: 30/50/90/100
- LF: 79
- DS: 1024/16384/131072/1048576/2097152/16777216/67108864/134217728
- HashMapType: Scalar/Vec/IntraVec/MultiThreading/QFGO


The following presents the code for reproducing the results from the paper:

```bash
# all scalar hashmaps under all dataset size with SQR=90,LF=80
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/hash_map_benchmark --benchmark_filter="BM_RandomAccess<.*Scalar.*>/.*/90/79"

# Neighbor/LinearProbing/RandomAccess with AMAC under all dataset size with SQR=90,LF=80
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/hash_map_benchmark --benchmark_filter="BM_RandomAccess<(Neighbor|LinearProbing|Array).*uniform.*AMAC.*>/.*/90/79$"

# all intra-vectorization hashmaps under all dataset size with SQR=90,LF=80
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/hash_map_benchmark --benchmark_filter="BM_RandomAccess<.*IntraVec.*>/.*/90/79"

# all vectorization hashmaps under all dataset size with SQR=90,LF=80
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/hash_map_benchmark --benchmark_filter="BM_RandomAccess<.*Vec.*>/.*/90/79"

# NeighborHash with QFGO under all dataset size with SQR=90,LF=80
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/hash_map_benchmark --benchmark_filter="BM_RandomAccess<.*QFGO.*>/.*/90/79"
```

To perform `Mixed insertions and lookups`, use the following code:
```bash
numactl --membind=0 --cpunodebind=0 ./bazel-bin/testing/mixed_multi_threading
```
