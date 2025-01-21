cmake_minimum_required(VERSION 3.14)
project(NeighborHash)

set(CMAKE_CXX_STANDARD 14)

include_directories(.)
include_directories(neighbor_hash)

add_executable(NeighborHash
        neighbor_hash/bucketing_simd.h
        neighbor_hash/common_policy.h
        neighbor_hash/linear_probing.h
        neighbor_hash/neighbor_hash.h
        neighbor_hash/slot_type.h
        testing/ankerl_bench.cc
        testing/hash_map_benchmark.cc
        testing/mixed_multi_threading.cc
        testing/neighbor_hash_test.cc
        testing/random_erase_test.cc)
