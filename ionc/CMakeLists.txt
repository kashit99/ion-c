cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

set(libsrc
        decQuadHelpers.c
        ion_allocation.c
        ion_binary.c
        ion_catalog.c
        ion_collection.c
        ion_debug.c
        ion_errors.c
        ion_helpers.c
        ion_index.c
        ion_initialize.c
        ion_int.c
        ion_reader_binary.c
        ion_reader.c
        ion_reader_text.c
        ion_scanner.c
        ion_stream.c
        ion_string.c
        ion_symbol_table.c
        ion_timestamp.c
        ion_writer_binary.c
        ion_writer.c
        ion_writer_text.c
        ion_decimal.c
        ion_float.c
        ion_extractor.c)


# this is the "object library" target: compiles the sources only once
add_library(objlib OBJECT ${libsrc})

# shared libraries need PIC
set_property(TARGET objlib PROPERTY POSITION_INDEPENDENT_CODE 1)
set_property(TARGET objlib
             PROPERTY C_STANDARD 99)


if (MSVC)
  add_library(ionc $<TARGET_OBJECTS:objlib>) 
else()
  add_library(ionc SHARED $<TARGET_OBJECTS:objlib>)
  add_library(ionc_static STATIC $<TARGET_OBJECTS:objlib>)
endif()


target_include_directories(objlib
        PUBLIC
            $<INSTALL_INTERFACE:include>
            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
            $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../decNumber/include>
        PRIVATE
            ${CMAKE_CURRENT_SOURCE_DIR}

)

if (MSVC)
    target_link_libraries(ionc decNumber)
else()
    # Unix requires linking against lib m explicitly.
    target_link_libraries(ionc decNumber m)
endif()



install(TARGETS ionc
        DESTINATION lib)

install(DIRECTORY include/ionc
        DESTINATION include/ionc
        FILES_MATCHING PATTERN "*.h")
