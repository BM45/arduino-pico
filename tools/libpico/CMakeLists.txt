cmake_minimum_required(VERSION 3.12)

include(pico_sdk_import.cmake)
include(pico_extras_import.cmake)

project(pico_lib C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_BUILD_TYPE RelWithDebInfo)

# Initialize the SDK
pico_sdk_init()

add_library(pico STATIC)

target_compile_definitions(pico PUBLIC
	PICO_PRINTF_ALWAYS_INCLUDED=1
	PICO_FLASH_SIZE_BYTES=16777216
)

target_compile_options(pico PUBLIC
	-fno-exceptions
	$<$<COMPILE_LANGUAGE:CXX>:-fno-rtti>
)

target_link_libraries(pico 
	boot_stage2
	hardware_adc
	hardware_base
	hardware_claim
	hardware_clocks
	hardware_divider
	hardware_dma
	hardware_flash
	hardware_gpio
	hardware_i2c
	hardware_interp
	hardware_irq
	hardware_pio
	hardware_pll
	hardware_pwm
	hardware_resets
	hardware_rtc
	hardware_spi
	hardware_sync
	hardware_timer
	hardware_uart
	hardware_vreg
	hardware_watchdog
	hardware_xosc
	pico_bit_ops
	pico_bootrom
	pico_bootsel_via_double_reset
	pico_cxx_options
	pico_divider
	pico_double
	pico_fix
	pico_float
	pico_int64_ops
	pico_malloc
	pico_mem_ops
	pico_multicore
	pico_platform
	pico_runtime
	pico_standard_link
	pico_stdio_usb
	pico_stdlib
	pico_unique_id
	tinyusb
	pico_audio
	pico_audio_i2s
)

add_custom_command(TARGET pico PRE_BUILD
	COMMAND arm-none-eabi-gcc -g -c ../../../assembly/crt0.S -I "${PICO_SDK_PATH}/src/rp2040/hardware_regs/include" -I "${PICO_SDK_PATH}/src/common/pico_binary_info/include/"
)

add_custom_command(TARGET pico POST_BUILD
	COMMAND ar d libpico.a crt0.S.obj
	COMMAND ar r libpico.a crt0.o
	COMMAND ar d libpico.a stdio.c.obj stdio_usb.c.obj stdio_usb_descriptors.c.obj
)