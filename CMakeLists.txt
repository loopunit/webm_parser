cmake_minimum_required(VERSION 3.23)
project(webm_parser)

include(GNUInstallDirs)
include("${CMAKE_CURRENT_SOURCE_DIR}/build/cxx_flags.cmake")

if (NOT BUILD_SHARED_LIBS)
  include("${CMAKE_CURRENT_SOURCE_DIR}/build/msvc_runtime.cmake")
endif ()

if(WIN32 OR CYGWIN OR MSYS)
  # Allow use of rand_r() / fdopen() and other POSIX functions.
  require_cxx_flag_nomsvc("-std=gnu++11")
else()
  require_cxx_flag_nomsvc("-std=c++11")
endif()

if (MSVC)
  add_cxx_flag_if_supported("/W4")
  # Disable MSVC warnings that suggest making code non-portable.
  add_cxx_flag_if_supported("/wd4996")
  if (ENABLE_WERROR)
    add_cxx_flag_if_supported("/WX")
  endif ()
else ()
  add_cxx_flag_if_supported("-Wall")
  add_cxx_flag_if_supported("-Wextra")
  add_cxx_flag_if_supported("-Wnarrowing")
  add_cxx_flag_if_supported("-Wno-deprecated")
  add_cxx_flag_if_supported("-Wshorten-64-to-32")
  if (ENABLE_WERROR)
    add_cxx_flag_if_supported("-Werror")
  endif ()
endif ()

option(ENABLE_TESTS "Enables tests." OFF)
option(ENABLE_SAMPLES "Enables samples." OFF)

set(LIBWEBM_SRC_DIR ${CMAKE_CURRENT_LIST_DIR})

set(webm_parser_public_headers
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/buffer_reader.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/callback.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/dom_types.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/element.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/file_reader.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/id.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/istream_reader.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/reader.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/status.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/include/webm/webm_parser.h")

set(webm_parser_sources
    ${webm_parser_public_headers}
    "${LIBWEBM_SRC_DIR}/webm_parser/src/ancestory.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/ancestory.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/audio_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/bit_utils.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/bit_utils.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_additions_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_group_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_header_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_header_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_more_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/block_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/bool_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/buffer_reader.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/byte_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/callback.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/chapter_atom_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/chapter_display_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/chapters_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/cluster_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/colour_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/content_enc_aes_settings_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/content_encoding_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/content_encodings_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/content_encryption_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/cue_point_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/cue_track_positions_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/cues_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/date_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/date_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/ebml_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/edition_entry_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/element_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/file_reader.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/float_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/float_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/id_element_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/id_element_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/id_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/id_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/info_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/int_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/istream_reader.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/master_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/master_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/master_value_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/mastering_metadata_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/parser_utils.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/parser_utils.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/projection_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/recursive_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/seek_head_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/seek_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/segment_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/segment_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/simple_tag_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/size_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/size_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/skip_callback.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/skip_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/skip_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/slices_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/tag_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/tags_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/targets_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/time_slice_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/track_entry_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/tracks_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/unknown_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/unknown_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/var_int_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/var_int_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/video_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/virtual_block_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/virtual_block_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/void_parser.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/void_parser.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/src/webm_parser.cc")
    
set(webm_parser_demo_sources "${LIBWEBM_SRC_DIR}/webm_parser/demo/demo.cc")

set(webm_parser_tests_sources
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/audio_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/bit_utils_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/block_additions_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/block_group_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/block_header_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/block_more_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/block_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/bool_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/buffer_reader_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/byte_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/callback_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/chapter_atom_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/chapter_display_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/chapters_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/cluster_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/colour_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/content_enc_aes_settings_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/content_encoding_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/content_encodings_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/content_encryption_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/cue_point_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/cue_track_positions_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/cues_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/date_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/ebml_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/edition_entry_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/element_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/float_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/id_element_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/id_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/info_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/int_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/istream_reader_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/limited_reader_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/master_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/master_value_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/mastering_metadata_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/parser_utils_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/projection_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/recursive_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/seek_head_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/seek_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/segment_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/simple_tag_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/size_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/skip_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/slices_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/tag_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/tags_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/targets_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/time_slice_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/track_entry_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/tracks_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/unknown_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/var_int_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/video_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/virtual_block_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/void_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/webm_parser_test.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/test_utils/element_parser_test.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/test_utils/limited_reader.cc"
    "${LIBWEBM_SRC_DIR}/webm_parser/test_utils/limited_reader.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/test_utils/mock_callback.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/test_utils/parser_test.h"
    "${LIBWEBM_SRC_DIR}/webm_parser/tests/webm_parser_tests.cc")

add_library(webm_parser ${webm_parser_sources})

target_include_directories(webm_parser 
    PUBLIC 
        $<BUILD_INTERFACE:${LIBWEBM_SRC_DIR}/webm_parser/include>
        $<BUILD_INTERFACE:${LIBWEBM_SRC_DIR}/webm_parser>
        $<INSTALL_INTERFACE:include>)

set_target_properties(webm_parser PROPERTIES PUBLIC_HEADER "${webm_parser_public_headers}")

set_target_properties(webm_parser
    PROPERTIES
        CXX_STANDARD 20
        CXX_EXTENSIONS off)
    
target_compile_definitions(webm_parser 
    PRIVATE
        -D__STDC_CONSTANT_MACROS
        -D__STDC_FORMAT_MACROS
        -D__STDC_LIMIT_MACROS)

if (WIN32)
  # Use libwebm and libwebm.lib for project and library name on Windows (instead
  # webm and webm.lib).
  set_target_properties(webm_parser PROPERTIES PROJECT_LABEL libwebm_parser)
  set_target_properties(webm_parser PROPERTIES PREFIX lib)
endif ()

install(TARGETS webm_parser
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/webm)

if(${ENABLE_SAMPLES})
    add_executable(webm_parser_demo ${webm_parser_demo_sources})
    target_link_libraries(webm_parser_demo PRIVATE webm_parser)
endif()

if(${ENABLE_TESTS})
    add_executable(webm_parser_tests ${webm_parser_tests_sources})
    target_include_directories(webm_parser_tests PRIVATE "${GTEST_SRC_DIR}/googlemock/include")
    target_link_libraries(webm_parser_tests PRIVATE gmock gtest webm_parser)
endif()
