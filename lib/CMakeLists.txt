add_subdirectory(includes)

include_directories(
  "${CMAKE_CURRENT_SOURCE_DIR}/includes"
  "${CMAKE_CURRENT_BINARY_DIR}/includes"
)

add_definitions(-DBUILDING_NGHTTP2)

set(NGHTTP2_SOURCES
  nghttp2_pq.c nghttp2_map.c nghttp2_queue.c
  nghttp2_frame.c
  nghttp2_buf.c
  nghttp2_stream.c nghttp2_outbound_item.c
  nghttp2_session.c nghttp2_submit.c
  nghttp2_helper.c
  nghttp2_npn.c
  nghttp2_hd.c nghttp2_hd_huffman.c nghttp2_hd_huffman_data.c
  nghttp2_version.c
  nghttp2_priority_spec.c
  nghttp2_option.c
  nghttp2_callbacks.c
  nghttp2_mem.c
  nghttp2_http.c
  nghttp2_rcbuf.c
)

# Public shared library
add_library(nghttp2 SHARED ${NGHTTP2_SOURCES})
set_target_properties(nghttp2 PROPERTIES
  COMPILE_FLAGS "${WARNCFLAGS}"
  VERSION ${LT_VERSION} SOVERSION ${LT_SOVERSION}
  C_VISIBILITY_PRESET hidden
)

if(HAVE_CUNIT)
  # Static library (for unittests because of symbol visibility)
  add_library(nghttp2_static STATIC ${NGHTTP2_SOURCES})
  set_target_properties(nghttp2_static PROPERTIES
    COMPILE_FLAGS "${WARNCFLAGS}"
    VERSION ${LT_VERSION} SOVERSION ${LT_SOVERSION}
    ARCHIVE_OUTPUT_NAME nghttp2
  )
  target_compile_definitions(nghttp2_static PUBLIC "-DNGHTTP2_STATICLIB")
endif()

install(TARGETS nghttp2
  DESTINATION "${CMAKE_INSTALL_LIBDIR}")

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/libnghttp2.pc"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/pkgconfig")