cmake_minimum_required(VERSION 3.16)
project(safebox LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

include_directories(${CMAKE_SOURCE_DIR}/core/src)

add_library(safebox_process_lib STATIC
    core/src/controller/process_table.cpp
    core/src/controller/process_launcher.cpp
    core/src/controller/process_control.cpp
    core/src/controller/proc_reader.cpp
    core/src/controller/process_tree.cpp
)
target_include_directories(safebox_process_lib PUBLIC ${CMAKE_SOURCE_DIR}/core/src)

add_executable(safebox_banker
    core/src/deadlock/bankers_main.cpp
    core/src/deadlock/bankers_algorithm.cpp
)

add_executable(safebox_analyze
    core/src/analyzer/analyzer_main.cpp
    core/src/analyzer/findings_builder.cpp
    core/src/analyzer/json_writer.cpp
)
target_link_libraries(safebox_analyze PRIVATE
    safebox_process_lib
    safebox_scheduler_lib
)

add_executable(fork_tree test_programs/fork_tree.cpp)
add_executable(bounded_process_load test_programs/bounded_process_load.cpp)
add_executable(bounded_cpu_load test_programs/bounded_cpu_load.cpp)

add_library(safebox_scheduler_lib STATIC
    core/src/scheduler/round_robin.cpp
    core/src/scheduler/scheduler_metrics.cpp
    core/src/scheduler/scheduler_runner.cpp
)

target_include_directories(safebox_scheduler_lib
    PUBLIC
        ${CMAKE_SOURCE_DIR}/core/src
)

add_executable(safebox_sched
    core/src/scheduler/scheduler_demo_main.cpp
)
target_link_libraries(safebox_sched PRIVATE safebox_scheduler_lib)

add_executable(cpu_burner
    test_programs/cpu_burner.cpp
)

target_link_libraries(cpu_burner PRIVATE pthread)

add_executable(round_robin_test
    core/tests/round_robin_test.cpp
)
target_link_libraries(round_robin_test PRIVATE safebox_scheduler_lib)

add_executable(scheduler_runner_test
    core/tests/scheduler_runner_test.cpp
)
target_link_libraries(scheduler_runner_test PRIVATE safebox_scheduler_lib)

add_executable(benign_app test_programs/benign_app.cpp)

enable_testing()
add_test(NAME round_robin_test COMMAND round_robin_test)
add_test(NAME scheduler_runner_test COMMAND scheduler_runner_test)
