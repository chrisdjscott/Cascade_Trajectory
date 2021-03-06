# add the benchmark executables
add_executable(cctest cc.f90)
add_executable(cctest_matrix cc_matrix.f90)

# copy the input file to the main directory
configure_file(params.nml ${CMAKE_BINARY_DIR} COPYONLY)

if (BUILD_TESTING)
    # Python is required for comparison
    find_package(PythonInterp)
    if (NOT PYTHONINTERP_FOUND)
        message(WARNING "Python is not available to compare results - tests will run but fail")
    endif()

    # macro for adding BMtest
    macro(add_bmtest TEST_TARGET TEST_NAME MAX_TIME)
        set(REF_FILE ${CMAKE_CURRENT_SOURCE_DIR}/BMoriginal_${MAX_TIME}_${CMAKE_Fortran_COMPILER_ID}.txt)
        if (NOT EXISTS ${REF_FILE})
            message(WARNING "No reference file for test ${TEST_NAME} ${MAX_TIME} - test will run but fail - ${REF_FILE}")
        endif()
        set(TEST_RUN_DIR "${CMAKE_CURRENT_BINARY_DIR}/${TEST_NAME}_${MAX_TIME}")
        add_test(
            NAME ${TEST_NAME}
            COMMAND ${CMAKE_COMMAND}
                -DTEST_RUN_DIR=${TEST_RUN_DIR}
                -DTEST_SRC_DIR=${CMAKE_CURRENT_SOURCE_DIR}
                -DTEST_BINARY=$<TARGET_FILE:${TEST_TARGET}>
                -DMAX_TIME=${MAX_TIME}
                -DCOMPARE_SCRIPT=${CMAKE_CURRENT_SOURCE_DIR}/BM.py
                -DREF_FILE=${REF_FILE}
                -DPYTHON_FOUND=${PYTHONINTERP_FOUND}
                -DPYTHON=${PYTHON_EXECUTABLE}
                -P ${CMAKE_CURRENT_SOURCE_DIR}/run_test.cmake
        )
    endmacro(add_bmtest)

    # add the short test
    add_bmtest(cctest "short-original" 1000)
    add_bmtest(cctest_matrix "short-matrix" 1000)

    # add the longer test
    add_bmtest(cctest "long-original" 25000)
    add_bmtest(cctest_matrix "long-matrix" 25000)
endif()
