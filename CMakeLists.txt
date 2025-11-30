#
# project: fips-libdeflate
# High performance compression/decompression library
#
cmake_minimum_required(VERSION 3.21)

project(fips-libdeflate)

get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
include("${FIPS_ROOT_DIR}/cmake/fips.cmake")

fips_setup()

fips_begin_lib(libdeflate)

    # Header files
    fips_files(
        common_defs.h
        libdeflate.h
    )

    # Core source files
    fips_dir(lib)
    fips_files(
        lib_common.h
        cpu_features_common.h
        deflate_constants.h
        utils.c
    )

    # ARM support
    fips_dir(lib/arm)
    fips_files(
        cpu_features.c
        cpu_features.h
    )

    # x86 support
    fips_dir(lib/x86)
    fips_files(
        cpu_features.c
        cpu_features.h
    )

    # Compression support
    fips_dir(lib)
    fips_files(
        bt_matchfinder.h
        deflate_compress.c
        deflate_compress.h
        hc_matchfinder.h
        ht_matchfinder.h
        matchfinder_common.h
    )

    fips_dir(lib/arm)
    fips_files(matchfinder_impl.h)

    fips_dir(lib/riscv)
    fips_files(matchfinder_impl.h)

    fips_dir(lib/x86)
    fips_files(matchfinder_impl.h)

    # Decompression support
    fips_dir(lib)
    fips_files(
        decompress_template.h
        deflate_decompress.c
    )

    fips_dir(lib/x86)
    fips_files(decompress_impl.h)

    # Zlib support
    fips_dir(lib)
    fips_files(
        adler32.c
        zlib_constants.h
        zlib_compress.c
        zlib_decompress.c
    )

    fips_dir(lib/arm)
    fips_files(adler32_impl.h)

    fips_dir(lib/x86)
    fips_files(
        adler32_impl.h
        adler32_template.h
    )

    # Gzip support
    fips_dir(lib)
    fips_files(
        crc32.c
        crc32_multipliers.h
        crc32_tables.h
        gzip_constants.h
        gzip_compress.c
        gzip_decompress.c
    )

    fips_dir(lib/arm)
    fips_files(
        crc32_impl.h
        crc32_pmull_helpers.h
        crc32_pmull_wide.h
    )

    fips_dir(lib/x86)
    fips_files(
        crc32_impl.h
        crc32_pclmul_template.h
    )

fips_end_lib()

# Include directories
target_include_directories(libdeflate PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

# C99 standard
set_target_properties(libdeflate PROPERTIES C_STANDARD 99)
