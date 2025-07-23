cmake_minimum_required(VERSION 3.21)

project(gtk4-example VERSION 1.0.0 LANGUAGES C)

find_package(PkgConfig REQUIRED)
pkg_check_modules(GTK4 REQUIRED IMPORTED_TARGET gtk4)

add_executable(${PROJECT_NAME})

set_target_properties(${PROJECT_NAME} PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    WIN32_EXECUTABLE ON
    DEBUG_POSTFIX d
    C_STANDARD 11
    C_STANDARD_REQUIRED ON
    C_EXTENSIONS OFF
)

execute_process(
    COMMAND git -C ${CMAKE_CURRENT_SOURCE_DIR} rev-parse --short --verify HEAD
    OUTPUT_VARIABLE GIT_COMMIT_HASH
    OUTPUT_STRIP_TRAILING_WHITESPACE
    ERROR_QUIET
)

if (GIT_COMMIT_HASH STREQUAL "")
    message(WARNING "Could not determine git commit hash")
    set(GIT_COMMIT_HASH "Unknown")
endif ()

target_compile_definitions(${PROJECT_NAME} PRIVATE
    GIT_COMMIT_HASH="${GIT_COMMIT_HASH}"
)

target_compile_options(${PROJECT_NAME} PRIVATE
    $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
    $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -Wpedantic -Werror>
    ${GTK4_CFLAGS_OTHER}
)

target_sources(${PROJECT_NAME} PRIVATE
)

target_sources(${PROJECT_NAME} PRIVATE
    main.c
)

target_link_libraries(${PROJECT_NAME} PRIVATE PkgConfig::GTK4)

install(TARGETS ${PROJECT_NAME} DESTINATION .)