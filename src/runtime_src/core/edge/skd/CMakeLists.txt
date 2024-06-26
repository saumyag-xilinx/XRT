# SPDX-License-Identifier: Apache-2.0
# Copyright (C) 2019-2021 Xilinx, Inc. All rights reserved.
#

pkg_check_modules(LIBFFI REQUIRED libffi)
find_package(libffi REQUIRED)

include_directories(
  ${BOOST_SYSTEM_INCLUDE_DIRS}
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/../include
  ${LIBFFI_INCLUDE_DIR}
  )

file(GLOB XRT_SKD_FILES
  "*.h"
  "*.cpp"
  "*.c"
  )

set(SKD_SRC
  ${XRT_SKD_FILES}
  )

add_executable(skd ${SKD_SRC})

add_dependencies(skd
  xrt_core
  xrt_coreutil
  )

target_link_libraries(skd
  PRIVATE
  ${Boost_SYSTEM_LIBRARY}
  xrt_core
  xrt_coreutil
  dl
  ${LIBFFI_LDFLAGS}
  )

# This linking with xrt_coreutil resolves link path for indirect link
# dependencies for libxrt_core.  The link path is embedded correctly
# in libxrt_core.so but is for some reason ignored when cross
# compiling with petalinux.  Per google this is an issue others have
# faced also and has something to do with explicit setting of sysroot.
target_link_libraries(skd
  PRIVATE
  xrt_coreutil
)

set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -rdynamic")

install (TARGETS skd RUNTIME DESTINATION ${XRT_INSTALL_DIR}/bin)
