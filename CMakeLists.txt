cmake_minimum_required(VERSION 3.12)

project("SkeletonMinRes" LANGUAGES CXX VERSION 1.0.0)
include(GNUInstallDirs)

function(embed_resources out_var)
    set(result)
  
    # Binary resource files and the resource header file will be generated in the following directoy:
    set(out_dir "${PROJECT_BINARY_DIR}/SkeletonMinRes")
    file(MAKE_DIRECTORY ${out_dir})

    # File name of the resource header file:
    set(out_hdr "${out_dir}/Resources.h")
    set_property(DIRECTORY APPEND PROPERTY ADDITIONAL_CLEAN_FILES ${out_hdr})
    list(APPEND ADDITIONAL_CLEAN_FILES ${out_hdr})
    foreach(in_f ${ARGN})
        file(RELATIVE_PATH src_f ${CMAKE_CURRENT_SOURCE_DIR} "${CMAKE_CURRENT_SOURCE_DIR}/${in_f}")
        set(out_f "${out_dir}/${in_f}.cpp")
        file(SIZE "${CMAKE_CURRENT_SOURCE_DIR}/${in_f}" file_size)
        set_property(DIRECTORY APPEND PROPERTY ADDITIONAL_CLEAN_FILES ${out_f})
        get_filename_component(out_f_dir "${out_f}" DIRECTORY)
        add_custom_command(OUTPUT ${out_f}
            COMMAND ${CMAKE_COMMAND} -E make_directory "${out_f_dir}"
            COMMAND SkeletonMinResBuilder "${out_f}" "${src_f}" "${out_hdr}"
            DEPENDS "${in_f}" SkeletonMinResBuilder
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
            COMMENT "Building binary file (${file_size} bytes) for embedding ${out_f}"
            VERBATIM)
        list(APPEND result "${out_f}")
    endforeach()

    set(${out_var} "${result}" PARENT_SCOPE)
    set(${out_var}_INCLUDE_DIR "${out_dir}" PARENT_SCOPE)
endfunction()

add_executable(SkeletonMinResBuilder src/SkeletonMinRes.cpp)
target_compile_definitions(SkeletonMinResBuilder PRIVATE SKMINRES_VERSION="0.1")

add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(
    ${PROJECT_NAME}
    INTERFACE $<BUILD_INTERFACE:${${PROJECT_NAME}_SOURCE_DIR}/include>
            $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
            ${PROJECT_BINARY_DIR})
