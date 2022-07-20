cmake_minimum_required(VERSION 3.15)

set (
	CMAKE_INSTALL_PREFIX
	"$(CMAKE_BINARY_DIR)/install"
	CACHE STRING
	"Final install location."
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "-O1")
if(MINGW AND ARCH_64)
  add_definitions(-DASSIMP_BUILD_NO_IFC_IMPORTER)
endif()

project(ReactiveCurl LANGUAGES CXX C)
add_definitions(-DCURL_STATICLIB)
include(ExternalProject)

set(CURL_CA_PATH_SET FALSE)
set(CURL_CA_BUNDLE_SET FALSE)
if(UNIX)
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
endif(UNIX)

if (WIN32)
	set(OPENSSL_USE_STATIC_LIBS TRUE) 
	find_package(OpenSSL REQUIRED)
endif() 

ExternalProject_Add(curl
  URL "https://github.com/curl/curl/archive/refs/tags/curl-7_84_0.zip"
  CMAKE_ARGS
	-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
	-DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
	-DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}
	-DCURL_USE_OPENSSL=ON
	-DOPENSSL_ROOT_DIR=${OPENSSL_ROOT_DIR}
)

find_package(CURL REQUIRED)
if(WIN32)
	set(CURL_INCLUDE_DIRS ${CMAKE_BINARY_DIR}/install/include)
	set(CURL_LIBRARIES ${CMAKE_BINARY_DIR}/install/lib/libcurl.dll.a)
endif(WIN32)


add_executable(ReactiveCurl src/reactive-curl.cpp src/singleton-rx.cpp src/singleton-rx.hpp src/rx-curl.hpp src/types.hpp)
include_directories("include")
target_include_directories(ReactiveCurl PUBLIC
${CURL_INCLUDE_DIRS}
${OPENSSL_INCLUDE_DIR}
)
if(WIN32)
target_link_libraries(ReactiveCurl
${CURL_LIBRARIES} wldap32 ws2_32 Crypt32.lib Wldap32 Normaliz 
${OPENSSL_LIBRARIES}
)
else()
target_link_libraries(ReactiveCurl
${CURL_LIBRARIES}
${OPENSSL_LIBRARIES}
)
endif(WIN32)
if(WIN32)
add_custom_command(TARGET ReactiveCurl POST_BUILD        # Adds a post-build event to MyTest
    COMMAND ${CMAKE_COMMAND} -E copy  # which executes "cmake - E copy_if_different..."
        "${CMAKE_BINARY_DIR}/install/bin/libcurl.dll"      # <--this is in-file
		"${CMAKE_BINARY_DIR}/")                 # <--this is out-file pat
endif(WIN32)