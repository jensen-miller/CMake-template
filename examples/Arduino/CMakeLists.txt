#[[
===============================================================================
	CMakeDuino

	Used in conjunction with VS2019 launch.vs.json. This builds Arduino
		projects and prepares them for debugging.

	Copyright (C) 2019 Jensen Miller.
	Licensed under the GNU GPLv3 license.
===============================================================================
]]
							  
cmake_minimum_required(VERSION 3.8)

project("${PROJECT_NAME}-Examples")

function(SANITIZE_FWD_SLASH IN_ARG OUT_ARG)
	string(REPLACE "/"
		"\\\\" IN_ARG
		OUT_ARG
	)
endfunction()

set(ARDUINO_DIR "%PROGRAMFILES(X86)%\\Arduino" CACHE STRING "The Arduino IDE path")
set(ARDUINO_BOARD_VENDOR "adafruit" CACHE STRING "The board's vendor")
set(ARDUINO_BOARD_ARCH "samd" CACHE STRING "The board's architecture")
set(ARDUINO_BOARD_NAME "adafruit_feather_m0_express" CACHE STRING "The Board name")

option(ARDUINO_DEBUG "Whether to output a debug of the sketch" ON)
set(ARDUINO_FQBN "${ARDUINO_BOARD_VENDOR}:${ARDUINO_BOARD_ARCH}:${ARDUINO_BOARD_NAME}:usbstack=arduino,debug=on")

set(ARDUINO_BUILDER "${ARDUINO_DIR}\\arduino-builder.exe")

##	PATCH[non-fatal]	Arduino location is hardcoded in some locations due to
##						issues sanitizing paths. Fortunately, this is default.
set(ARDUINO_BUILD_ARGS
	"-compile"
	"-logger=humantags"
	"-hardware"
	"${ARDUINO_DIR}\\hardware"
	"-hardware"
	"%userprofile%\\AppData\\Local\\Arduino15\\packages"
	"-tools"
	"C:\\Program Files (x86)\\Arduino\\tools-builder"
	"-tools"
	"C:\\Program Files (x86)\\Arduino\\hardware\\tools\\avr"
	"-tools"
	"%userprofile%\\AppData\\Local\\Arduino15\\packages"
	"-built-in-libraries"
	"C:\\Program Files (x86)\\Arduino\\libraries"
	"-libraries"
	"%userprofile%\\Documents\\Arduino\\libraries"
	"-libraries"
	"%userprofile%\\GitHub\\LooUQ"
	"-fqbn=${ARDUINO_FQBN}"
	"-ide-version=10810"
	"-warnings=all"
	"-debug-level=10"
	"-prefs=build.warn_data_percentage=75"
	"-prefs=runtime.tools.CMSIS.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\CMSIS\\4.5.0"
	"-prefs=runtime.tools.CMSIS-4.5.0.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\CMSIS\\4.5.0"
	"-prefs=runtime.tools.bossac.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\bossac\\1.8.0-48-gb176eee"
	"-prefs=runtime.tools.bossac-1.7.0.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\bossac\\1.7.0"
	"-prefs=runtime.tools.bossac-1.8.0-48-gb176eee.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\bossac\\1.8.0-48-gb176eee"
	"-prefs=runtime.tools.openocd.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\openocd\\0.9.0-arduino"
	"-prefs=runtime.tools.openocd-0.9.0-arduino.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\openocd\\0.9.0-arduino"
	"-prefs=runtime.tools.arm-none-eabi-gcc.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\arm-none-eabi-gcc\\4.8.3-2014q1"
	"-prefs=runtime.tools.arm-none-eabi-gcc-4.8.3-2014q1.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\arm-none-eabi-gcc\\4.8.3-2014q1"
	"-prefs=runtime.tools.CMSIS-Atmel.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\CMSIS-Atmel\\1.2.0"
	"-prefs=runtime.tools.CMSIS-Atmel-1.2.0.path=%userprofile%\\AppData\\Local\\Arduino15\\packages\\arduino\\tools\\CMSIS-Atmel\\1.2.0"
	"-verbose"
)

#	Loop to sanitize arguments from forward slashes
foreach(build_arg ${ARDUINO_BUILD_ARGS})
	SANITIZE_FWD_SLASH(build_arg build_arg)
endforeach()


#
#	List of examples
#
set(EXAMPLES
	sketch-example
)


include_directories("../../include")

add_definitions(-DARDUINO)
set_property(DIRECTORY PROPERTY COMPILE_OPTIONS ${EXTRA_CXX_FLAGS})

#if("{CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
	
#endif()

set(CMAKE_EXAMPLE_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/out/IoT-Debug/examples")
include_directories("../../../include")

#foreach(example ${EXAMPLES})
#	message("Copying example to ${CMAKE_SOURCE_DIR}/out/IoT-Debug/examples")
#	configure_file(${CMAKE_SOURCE_DIR}/examples/arduino/${example}/${example}.ino ${CMAKE_EXAMPLE_OUTPUT_DIRECTORY}/arduino/${example}/${example}.cpp COPYONLY)
#	add_executable(${example} ${CMAKE_EXAMPLE_OUTPUT_DIRECTORY}/arduino/${example}/${example}.cpp)	
#endforeach()

#find_package(PythonInterp REQUIRED)

foreach (example "${EXAMPLES}")
	add_custom_target("${example}-Sketch" ALL
		SOURCES  "${example}/${example}.ino"
		COMMAND ${CMAKE_COMMAND} -E make_directory "${CMAKE_SOURCE_DIR}/out/build/IoT-Debug/examples/${example}"
	)	

	add_custom_command(TARGET "${example}-Sketch"
		COMMAND ${ARDUINO_BUILDER} ${ARDUINO_BUILD_ARGS} "-build-path" "..\\..\\out\\build\\IoT-Debug\\examples\\${example}" "-build-cache" "..\\..\\out\\build\\IoT-Debug\\examples\\${example}\\.cache" ${example}\\${example}.ino
		WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
	)
endforeach()