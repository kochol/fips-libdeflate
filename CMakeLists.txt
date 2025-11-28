#
# project: fips-libdeflate
# Fast, whole-buffer DEFLATE-based compression and decompression library
#
cmake_minimum_required(VERSION 3.21)

project(fips-libdeflate)

get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
include("${FIPS_ROOT_DIR}/cmake/fips.cmake")

fips_setup()

fips_begin_lib(libdeflate)

    # Core library source files
    fips_dir(lib)
    fips_files(
        adler32.c
        crc32.c
        deflate_compress.c
        deflate_decompress.c
        gzip_compress.c
        gzip_decompress.c
        utils.c
        zlib_compress.c
        zlib_decompress.c
    )

    # Core headers
    fips_dir(lib)
    fips_files(
        bt_matchfinder.h
        cpu_features_common.h
        crc32_multipliers.h
        crc32_tables.h
        decompress_template.h
        deflate_compress.h
        deflate_constants.h
        gzip_constants.h
        hc_matchfinder.h
        ht_matchfinder.h
        lib_common.h
        matchfinder_common.h
        zlib_constants.h
    )

    # ARM-specific files
    fips_dir(lib/arm)
    fips_files(
        cpu_features.c
        cpu_features.h
        adler32_impl.h
        crc32_impl.h
        crc32_pmull_helpers.h
        crc32_pmull_wide.h
        matchfinder_impl.h
    )

    # x86-specific files
    fips_dir(lib/x86)
    fips_files(
        cpu_features.c
        cpu_features.h
        adler32_impl.h
        adler32_template.h
        crc32_impl.h
        crc32_pclmul_template.h
        decompress_impl.h
        matchfinder_impl.h
    )

    # Public headers
    fips_dir(.)
    fips_files(
        libdeflate.h
        common_defs.h
    )

fips_end_lib()

# Include directories
target_include_directories(libdeflate PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(libdeflate PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/lib)

# Compiler definitions
target_compile_definitions(libdeflate PRIVATE
    LIBDEFLATE_COMPRESSION_SUPPORT=1
    LIBDEFLATE_DECOMPRESSION_SUPPORT=1
    LIBDEFLATE_ZLIB_SUPPORT=1
    LIBDEFLATE_GZIP_SUPPORT=1
)

# Disable some warnings for third-party code
if (FIPS_MSVC)
    target_compile_options(libdeflate PRIVATE /wd4146 /wd4244 /wd4267)
elseif (FIPS_CLANG OR FIPS_GCC)
    target_compile_options(libdeflate PRIVATE -Wno-implicit-fallthrough)
endif()
