cmake_minimum_required (VERSION 2.8)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMake")

include (CodeGenerator)

set (KMS_CORE_INTERFACE_SOURCES
)
set (KMS_CORE_INTERFACE_HEADERS
  interface/KurentoException.hpp
)

set (KMS_CORE_IMPL_SOURCES
  implementation/EventHandler.cpp
  implementation/Factory.cpp
  implementation/MediaSet.cpp
  implementation/ModuleManager.cpp
  implementation/WorkerPool.cpp
  implementation/UUIDGenerator.cpp
  implementation/RegisterParent.cpp
  implementation/DotGraph.cpp
)

set (KMS_CORE_IMPL_HEADERS
  implementation/EventHandler.hpp
  implementation/Factory.hpp
  implementation/MediaSet.hpp
  implementation/FactoryRegistrar.hpp
  implementation/ModuleManager.hpp
  implementation/WorkerPool.hpp
  implementation/UUIDGenerator.hpp
  implementation/RegisterParent.hpp
  implementation/DotGraph.hpp
  implementation/SignalHandler.hpp
)

set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")

generate_code (
  MODELS ${CMAKE_CURRENT_SOURCE_DIR}/interface
  INTERFACE_LIB_EXTRA_SOURCES ${KMS_CORE_INTERFACE_SOURCES}
  INTERFACE_LIB_EXTRA_HEADERS ${KMS_CORE_INTERFACE_HEADERS}
  INTERFACE_LIB_EXTRA_INCLUDE_DIRS
      ${CMAKE_SOURCE_DIR}/src/gst-plugins
      ${CMAKE_CURRENT_SOURCE_DIR}/implementation
      ${CMAKE_CURRENT_SOURCE_DIR}/interface
      ${KmsJsonRpc_INCLUDE_DIRS}
      ${sigc++-2.0_INCLUDE_DIRS}
      ${glibmm-2.4_INCLUDE_DIRS}
  INTERFACE_LIB_EXTRA_LIBRARIES
      ${KmsJsonRpc_LIBRARIES}
      ${sigc++-2.0_LIBRARIES}
      ${glibmm-2.4_LIBRARIES}
  SERVER_IMPL_LIB_EXTRA_SOURCES
      ${KMS_CORE_IMPL_SOURCES}
  SERVER_IMPL_LIB_EXTRA_HEADERS
      ${KMS_CORE_IMPL_HEADERS}
  SERVER_IMPL_LIB_EXTRA_INCLUDE_DIRS
      ${CMAKE_SOURCE_DIR}/src/gst-plugins
      ${CMAKE_SOURCE_DIR}/src/gst-plugins/commons
      ${CMAKE_CURRENT_SOURCE_DIR}/implementation
      ${Boost_INCLUDE_DIRS}
      ${Boost_FILESYSTEM_INCLUDE_DIRS}
      ${Boost_SYSTEM_INCLUDE_DIRS}
      ${gstreamer-1.5_INCLUDE_DIRS}
      ${gstreamer-sdp-1.5_INCLUDE_DIRS}
      ${KmsJsonRpc_INCLUDE_DIRS}
  SERVER_IMPL_LIB_EXTRA_LIBRARIES
      ${gstreamer-sdp-1.5_LIBRARIES}
      ${Boost_LIBRARIES}
      ${gstreamer-1.5_LIBRARIES}
      ${KmsJsonRpc_LIBRARIES}
      kmsutils
      kmsgstcommons
  MODULE_EXTRA_INCLUDE_DIRS
      ${CMAKE_SOURCE_DIR}/src/gst-plugins
      ${CMAKE_CURRENT_SOURCE_DIR}/interface
      ${gstreamer-1.5_INCLUDE_DIRS}
      ${gstreamer-sdp-1.5_INCLUDE_DIRS}
      ${KmsJsonRpc_INCLUDE_DIRS}
      ${sigc++-2.0_INCLUDE_DIRS}
      ${glibmm-2.4_INCLUDE_DIRS}
  MODULE_EXTRA_LIBRARIES
      ${gstreamer-1.5_LIBRARIES}
      ${KmsJsonRpc_LIBRARIES}
      ${sigc++-2.0_LIBRARIES}
      ${glibmm-2.4_LIBRARIES}
  SERVER_STUB_DESTINATION
      ${CMAKE_CURRENT_SOURCE_DIR}/implementation/objects
  SERVER_IMPL_LIB_PKGCONFIG_EXTRA_REQUIRES
      "gstreamer-1.5 gstreamer-sdp-1.5 libjsonrpc sigc++-2.0 glibmm-2.4"
  SERVER_IMPL_LIB_FIND_CMAKE_EXTRA_LIBRARIES
      KmsGstCommons
      "gstreamer-1.5 ${GST_REQUIRED}"
      "gstreamer-sdp-1.5 ${GST_REQUIRED}"
      "KmsJsonRpc ${JSON_RPC_REQUIRED}"
      "sigc++-2.0 ${sigc++-2.0_REQUIRED}"
      "glibmm-2.4 ${glibmm-2.4_REQUIRED}"
      "LIBNAME Boost COMPONENTS filesystem system"
)

if (NOT ${DISABLE_LIBRARIES_GENERATION})
  add_dependencies(${LIBRARY_NAME}impl kmscoreplugins)
endif()
