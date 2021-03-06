set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.17)

set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

project(testHK32 C CXX ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

add_compile_options(-mcpu=cortex-m0 -mthumb -mthumb-interwork)
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0 -O2)

#if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
#    message(STATUS "Maximum optimization for speed")
#    add_compile_options(-Ofast)
#elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
#    message(STATUS "Maximum optimization for speed, debug info included")
#    add_compile_options(-Ofast -g)
#elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
#    message(STATUS "Maximum optimization for size")
#    add_compile_options(-Os)
#else ()
#    message(STATUS "Minimal optimization, debug info included")
#    add_compile_options(-Og -g)
#endif ()

include_directories(Core/inc)
include_directories(Drivers/CMSIS/CM0/Core)
include_directories(Drivers/CMSIS/HK32F030M/Include)
include_directories(Drivers/HK32F030M_Lib/inc)

add_definitions(-DHK32F030MF4P6)

file(GLOB_RECURSE SOURCES "StartUp/*.*" "Drivers/*.*" "Core/*.*" "Hardware/*.*")

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/HK32F030M_FLASH.ld)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_link_options(-T ${LINKER_SCRIPT})

add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
        Building ${BIN_FILE}")

