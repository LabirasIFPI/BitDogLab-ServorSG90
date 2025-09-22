# Generated Cmake Pico project file

cmake_minimum_required(VERSION 3.13)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Initialise pico_sdk from installed location
# (note this can come from environment, CMake cache etc)

# == DO NOT EDIT THE FOLLOWING LINES for the Raspberry Pi Pico VS Code Extension to work ==
if(WIN32)
    set(USERHOME $ENV{USERPROFILE})
else()
    set(USERHOME $ENV{HOME})
endif()
set(sdkVersion 2.1.1)
set(toolchainVersion 14_2_Rel1)
set(picotoolVersion 2.1.1)
set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)
if (EXISTS ${picoVscode})
    include(${picoVscode})
endif()
# ====================================================================================
set(PICO_BOARD pico_w CACHE STRING "Board type")

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

project(main C CXX ASM)

# Initialise the Raspberry Pi Pico SDK
pico_sdk_init()

# ======================= FETCHCONTENT PARA SSD1306 =======================
# 1. Inclui o módulo necessário para buscar dependências externas
include(FetchContent)

# 2. Declara a dependência: de onde baixar e qual versão usar
FetchContent_Declare(
    pico_ssd1306
    GIT_REPOSITORY https://github.com/daschr/pico-ssd1306.git
    # Para garantir builds consistentes, o ideal é usar um commit hash específico.
    # Se preferir a última versão, pode usar GIT_TAG main ou master.
    GIT_TAG         main
)

# 3. Efetivamente baixa o código da dependência para o diretório de build
FetchContent_MakeAvailable(pico_ssd1306)

# 4. Como a lib não tem um CMakeLists.txt, criamos uma biblioteca para ela
#    Isso nos permite tratá-la como um componente modular do CMake
add_library(ssd1306_lib INTERFACE)
target_include_directories(ssd1306_lib INTERFACE ${pico_ssd1306_SOURCE_DIR})
target_sources(ssd1306_lib INTERFACE ${pico_ssd1306_SOURCE_DIR}/ssd1306.c)
# ===============================================================================

# Add executable.
# Default name is the project name, version 0.1
file(GLOB SRC_FILES "src/*.c")

add_executable(main 
    ${SRC_FILES}
)

pico_set_program_name(main "main")
pico_set_program_version(main "0.1")

# Modify the below lines to enable/disable output over UART/USB
pico_enable_stdio_uart(main 0)
pico_enable_stdio_usb(main 1)


# Add the standard library to the build
target_link_libraries(main
    pico_stdlib
    hardware_i2c
    hardware_pwm
    ssd1306_lib
)

# Add the standard include files to the build
target_include_directories(main PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}
    ${CMAKE_CURRENT_LIST_DIR}/inc
)

# Add any user requested libraries
target_link_libraries(main 
        
)

pico_add_extra_outputs(main)