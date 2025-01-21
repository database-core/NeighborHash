cmake_minimum_required(VERSION 3.14)
project(NeighborHash)

set(CMAKE_CXX_STANDARD 14)


add_definitions(-DABSL_HAVE_PREFETCH)
add_definitions(-DNEIGHBOR_HASH_SIMD_FIND)
add_definitions(-DNEIGHBOR_HASH_HUGEPAGE)


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
