cmake_minimum_required(VERSION 3.2.2)

project(syclblas_unittest)

set(SYCLBLAS_UNITTEST ${CMAKE_CURRENT_SOURCE_DIR})
set(SYCLBLAS_TEST_INCLUDE "${SYCLBLAS_TEST}/include")

include_directories(${SYCLBLAS_TEST})

# compiling tests
file(GLOB SYCL_UNITTEST_SRCS
  # Blas 1 tests
  ${SYCLBLAS_UNITTEST}/blas1/blas1_copy_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_swap_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_axpy_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_scal_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_asum_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_dot_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_nrm2_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_rotg_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_iamax_test.cpp
  ${SYCLBLAS_UNITTEST}/blas1/blas1_iamin_test.cpp

  # Blas 2 tests
  ${SYCLBLAS_UNITTEST}/blas2/blas2_gemv_test.cpp
  ${SYCLBLAS_UNITTEST}/blas2/blas2_ger_test.cpp

  # Blas 3 tests
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_nn_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_nt_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_nc_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_tn_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_tt_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_tc_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_cc_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_cn_test.cpp
  ${SYCLBLAS_UNITTEST}/blas3/gemm/blas3_gemm_ct_test.cpp

  # Blas buffer tests
  ${SYCLBLAS_UNITTEST}/buffers/sycl_buffer_test.cpp
)

foreach(blas_test ${SYCL_UNITTEST_SRCS})
  get_filename_component(test_exec ${blas_test} NAME_WE)
  set(test_exec, ${blas_test})
  add_executable(${test_exec} main.cpp ${blas_test})
  set_property(TARGET ${test_exec} PROPERTY CXX_STANDARD 11)
  add_dependencies(${test_exec} libgtest libgmock)
  target_link_libraries(${test_exec} PUBLIC libgtest libgmock ${BLAS_LIBRARIES})
  add_sycl_to_target(
    TARGET ${test_exec}
    SOURCES ${blas_test}
  )
  add_test(NAME ${test_exec} COMMAND ${CMAKE_CURRENT_BINARY_DIR}/${test_exec})
  message("-- Created google test ${test_exec}")
  install(TARGETS ${test_exec} RUNTIME DESTINATION bin)

endforeach()
