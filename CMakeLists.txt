cmake_minimum_required(VERSION 3.11)
project(loki11) # later: VERSION x.y.z

add_library(loki-library STATIC)

target_sources(loki-library
               PRIVATE
               ${loki11_SOURCE_DIR}/src/Singleton.cpp
               ${loki11_SOURCE_DIR}/src/SmallObj.cpp
)
target_include_directories(loki-library
                           PUBLIC
                           ${loki11_SOURCE_DIR}/inc
)
set_target_properties(loki-library PROPERTIES
                      CXX_STANDARD 11
                      CXX_STANDARD_REQUIRED ON
)
add_library(loki::loki ALIAS loki-library)

if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)

    # GCC for now
    set(warnings
        -Wall -Wextra -pedantic -Wcast-qual -Wcast-align -Wduplicated-branches -Wduplicated-cond -Wfloat-equal -Winit-self
        -Wno-unknown-pragmas -Wredundant-decls -Wshadow -Wsign-conversion -Wuninitialized)

    target_compile_options(loki-library PRIVATE ${warnings})

    set(LOKI_TEST_SOURCES
        ${loki11_SOURCE_DIR}/test/AbstractFactoryTest.h
    )

    add_executable(loki-tests)

target_sources(loki-tests PRIVATE ${LOKI_TEST_SOURCES})
    set_target_properties(loki-tests PROPERTIES
                          CXX_STANDARD 17
                          CXX_STANDARD_REQUIRED ON
    )
    target_compile_options(loki-tests PRIVATE ${warnings})

    Include(FetchContent)
    FetchContent_Declare(
        Catch2
        GIT_REPOSITORY https://github.com/catchorg/Catch2.git
        GIT_TAG v3.5.2
    )
    FetchContent_MakeAvailable(Catch2)

    target_link_libraries(loki-tests PRIVATE loki::loki Catch2WithMain)

endif()