cmake_minimum_required(VERSION 3.12.0 FATAL_ERROR)

include(GNUInstallDirs)

option(BUILD_EXAMPLE "Build example" ON)
option(BUILD_TEST "Build test" ON)

project(Serialize VERSION 1.0 LANGUAGES CXX)

add_library(Serialize INTERFACE)

target_include_directories(
  Serialize
  INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/serialize.h>
            $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/Serialize>)
			
string(APPEND CMAKE_CXX_FLAGS_DEBUG_INIT " -g SERIALIZE_DEBUG")
string(APPEND CMAKE_CXX_FLAGS_RELEASE_INIT " -O3 -DNDEBUG SERIALIZE_RELEASE")

if (BUILD_TEST)
	add_executable(test "test.cpp")
	target_compile_definitions(test PRIVATE
		"$<$<CONFIG:Debug>:"
            "SERIALIZE_DEBUG;"
        ">"
		"$<$<CONFIG:Release>:"
            "SERIALIZE_RELEASE;"
        ">"
		SERIALIZE_ENABLE_TESTS=1
        "_CONSOLE;"
    )
	target_link_libraries(test Serialize)
endif ()
if (BUILD_EXAMPLE)
	add_executable(example "example.cpp")
	target_compile_definitions(example PRIVATE
		"$<$<CONFIG:Debug>:"
			"SERIALIZE_DEBUG;"
            "_DEBUG;"
        ">"
		"$<$<CONFIG:Release>:"
			"SERIALIZE_RELEASE;"
            "NDEBUG;"
        ">"
        "_CONSOLE;"
    )
	target_link_libraries(example Serialize)
endif ()

# install and export the library
 
install(DIRECTORY
    Serialize
	DESTINATION
    include/Serialize
)
install(FILES
    serialize.h
  DESTINATION
    include/Serialize
)