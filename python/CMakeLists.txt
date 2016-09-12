set(SETUP_PY    "${CMAKE_CURRENT_SOURCE_DIR}/setup.py")
set(OUTPUT      "${CMAKE_CURRENT_BINARY_DIR}/build")

add_custom_command(OUTPUT ${OUTPUT}/timestamp
                   COMMAND ${CMAKE_COMMAND}
                   ARGS -E env "CFLAGS=-I${PROJECT_SOURCE_DIR} -I${PROJECT_BINARY_DIR}/cmsat5-src"
                               "LDFLAGS=-L${PROJECT_BINARY_DIR}/lib"
                        ${PYTHON_EXECUTABLE} ${SETUP_PY} build_ext --inplace --rpath ../lib
                   WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                   DEPENDS libcryptominisat5
)

add_custom_target(python_interface ALL DEPENDS ${OUTPUT}/timestamp)

install(CODE "execute_process(COMMAND ${PYTHON_EXECUTABLE} ${SETUP_PY} install --record files.txt)")

if (ENABLE_TESTING)
    add_test (NAME pytest
              WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
              COMMAND ${PYTHON_EXECUTABLE} "${CMAKE_CURRENT_SOURCE_DIR}/test_pycryptosat.py"
    )

    message(STATUS "Will run python test from ${CMAKE_CURRENT_BINARY_DIR} directory")
endif()
