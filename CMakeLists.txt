cmake_minimum_required(VERSION 2.8.12)

project(webrtc)
set(CMAKE_CXX_STANDARD 17)
#[[
add_subdirectory(api)
add_subdirectory(audio)
add_subdirectory(call)
add_subdirectory(common_audio)
add_subdirectory(common_video)
add_subdirectory(logging)
add_subdirectory(media)
add_subdirectory(modules)
add_subdirectory(p2p)
add_subdirectory(pc)
add_subdirectory(rtc_base)
add_subdirectory(stats)
add_subdirectory(system_wrappers)
add_subdirectory(video)
]]
set(webrtc_sources_list)
function(filter value sources_list)
    foreach(VAR ${ARGV})
        if (${VAR} STREQUAL ${ARGV0})
            continue()
        elseif (VAR MATCHES "unittest.cc$")
            continue()
        elseif(VAR MATCHES "test+")
            continue()
        elseif(VAR MATCHES "neon.cc$")
            continue()
        elseif(VAR MATCHES "neon.c$")
            continue()
        elseif(VAR MATCHES "mips+")
            continue()
        elseif(VAR MATCHES "window.c$")
            continue()
        elseif(VAR MATCHES "linux+")
            continue()
        elseif(VAR MATCHES "android+")
            continue()
        elseif(VAR MATCHES "win+")
            continue()
        elseif(VAR MATCHES "task_queue_libevent+")
            continue()
        endif(${VAR} STREQUAL ${ARGV0})
        list(APPEND sources ${VAR})
    endforeach(VAR)
    set(${ARGV0} ${sources} PARENT_SCOPE)
endfunction()

file(GLOB_RECURSE api_sources
        ${PROJECT_SOURCE_DIR}/api/*.cc
        ${PROJECT_SOURCE_DIR}/api/*.c)

file(GLOB_RECURSE audio_sources
        ${PROJECT_SOURCE_DIR}/audio/*.cc
        ${PROJECT_SOURCE_DIR}/audio/*.c)

file(GLOB_RECURSE call_sources
        ${PROJECT_SOURCE_DIR}/call/*.cc
        ${PROJECT_SOURCE_DIR}/call/*.c)

file(GLOB_RECURSE common_audio_sources
        ${PROJECT_SOURCE_DIR}/common_audio/*.cc
        ${PROJECT_SOURCE_DIR}/common_audio/*.c)

file(GLOB_RECURSE common_audio_sources
        ${PROJECT_SOURCE_DIR}/common_video/*.cc
        ${PROJECT_SOURCE_DIR}/common_video/*.c)

file(GLOB_RECURSE logging_sources
        ${PROJECT_SOURCE_DIR}/logging/*.cc
        ${PROJECT_SOURCE_DIR}/logging/*.c)

file(GLOB_RECURSE media_sources
        ${PROJECT_SOURCE_DIR}/media/*.cc
        ${PROJECT_SOURCE_DIR}/media/*.c)

file(GLOB_RECURSE modules_sources
        ${PROJECT_SOURCE_DIR}/modules/*.cc
        ${PROJECT_SOURCE_DIR}/modules/*.c)

file(GLOB_RECURSE p2p_sources
        ${PROJECT_SOURCE_DIR}/p2p/*.cc
        ${PROJECT_SOURCE_DIR}/p2p/*.c)

file(GLOB_RECURSE pc_sources
        ${PROJECT_SOURCE_DIR}/pc/*.cc
        ${PROJECT_SOURCE_DIR}/pc/*.c)

file(GLOB_RECURSE rtc_base_sources
        ${PROJECT_SOURCE_DIR}/rtc_base/*.cc
        ${PROJECT_SOURCE_DIR}/rtc_base/*.c)

file(GLOB_RECURSE stats_sources
        ${PROJECT_SOURCE_DIR}/stats/*.cc
        ${PROJECT_SOURCE_DIR}/stats/*.c)

file(GLOB_RECURSE system_wrappers_sources
        ${PROJECT_SOURCE_DIR}/system_wrappers/*.cc
        ${PROJECT_SOURCE_DIR}/system_wrappers/*.c)

file(GLOB_RECURSE video_sources
        ${PROJECT_SOURCE_DIR}/video/*.cc
        ${PROJECT_SOURCE_DIR}/video/*.c)

#[[
filter(api_sources ${api_sources})
filter(call_sources ${call_sources})
filter(common_audio_sources ${common_audio_sources})
filter(common_audio_sources ${common_audio_sources})
filter(logging_sources ${logging_sources})
filter(media_sources ${media_sources})
filter(modules_sources ${modules_sources})
filter(p2p_sources ${p2p_sources})
filter(pc_sources ${pc_sources})
filter(rtc_base_sources ${rtc_base_sources})
filter(stats_sources ${stats_sources})
filter(system_wrappers_sources ${system_wrappers_sources})
filter(video_sources ${video_sources})
]]

list(APPEND webrtc_sources_list ${api_sources} ${audio_sources} ${call_sources} ${common_audio_sources}
        ${common_video_sources} ${logging_sources} ${media_sources} ${modules_sources} ${p2p_sources}
        ${pc_sources} ${rtc_base_sources} ${stats_sources} ${system_wrappers_sources} ${video_sources})
filter(webrtc_sources_list ${webrtc_sources_list})

add_compile_options(-march=native)
set(CMAKE_CXX_FLAGS "${DEBUG_DEFINE} -pipe -Wall -W -fstrict-aliasing -Wno-invalid-offsetof -Wno-unused-parameter")
set(CMAKE_C_FLAGS "${DEBUG_DEFINE} -pipe -Wall -fstrict-aliasing -Wno-unused-parameter")

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    set(platform_defines WEBRTC_LINUX)
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    set(platform_defines WEBRTC_MAC)
    #    set(ssl_deps /usr/local/Cellar/openssl@1.1/1.1.1d)
endif()
set(BUILD_TESTING OFF)
set(ABSL_ENABLE_INSTALL ON)
set(CMAKE_INSTALL_PREFIX ${PROJECT_BINARY_DIR})
set(CMAKE_INSTALL_INCLUDEDIR )
#add_subdirectory(third_party/abseil-cpp)
#add_subdirectory(third_party/googletest)
#add_subdirectory(third_party/jsoncpp)
#add_subdirectory(third_party/opus)
#add_subdirectory(third_party/libyuv)
#add_subdirectory(third_party/usrsctp)
#add_subdirectory(third_party/FFmpeg)
#add_subdirectory(third_party/libaom/source/libaom)

#add_subdirectory(third_party/protobuf)

add_library(libwebrtc STATIC
        ${webrtc_sources_list})

#[[
add_library(libwebrtc STATIC
        ${api_sources}
        ${audio_sources}
        ${call_sources}
        ${common_audio_sources}
        ${common_video_sources}
        ${logging_sources}
        ${media_sources}
        ${modules_sources}
        ${p2p_sources}
        ${pc_sources}
        ${rtc_base_sources}
        ${stats_sources}
        ${system_wrappers_sources}
        ${video_sources}
        )
]]
target_compile_definitions(libwebrtc PUBLIC
        WEBRTC_APM_DEBUG_DUMP=0
        WEBRTC_USE_H264
        WEBRTC_POSIX
        ${platform_defines}
        #        WEBRTC_MAC #check platform
        WEBRTC_EXTERNAL_JSON
        WEBRTC_USE_BUILTIN_ISAC_FIX
        WEBRTC_ENABLE_PROTOBUF
        WEBRTC_OPUS_VARIABLE_COMPLEXITY
        WEBRTC_INCLUDE_INTERNAL_AUDIO_DEVICE
        WEBRTC_DUMMY_FILE_DEVICES
        WEBRTC_USE_BUILTIN_OPUS
        HAVE_WEBRTC_VIDEO
        HAVE_NETINET_IN_H
        )

target_include_directories(libwebrtc PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${PROJECT_SOURCE_DIR}/third_party/libaom
        ${PROJECT_SOURCE_DIR}/third_party/libvpx
        ${PROJECT_SOURCE_DIR}/third_party/FFmpeg
        ${PROJECT_SOURCE_DIR}/third_party/googletest
        ${PROJECT_SOURCE_DIR}/third_party/googletest/googlemock/include
        ${PROJECT_SOURCE_DIR}/third_party/googletest/googletest/include
        ${PROJECT_SOURCE_DIR}/third_party/protobuf/src
        ${PROJECT_SOURCE_DIR}/third_party/libyuv/include
        ${PROJECT_SOURCE_DIR}/third_party/abseil-cpp
        ${PROJECT_SOURCE_DIR}/third_party/opus/include
        ${PROJECT_SOURCE_DIR}/third_party/pffft
        ${PROJECT_SOURCE_DIR}/third_party/rnnoise
        ${PROJECT_SOURCE_DIR}/third_party/usrsctp/usrsctplib
        ${PROJECT_SOURCE_DIR}/third_party/libaom/source/libaom
        ${PROJECT_SOURCE_DIR}/third_party/libsrtp/crypto/include
        ${PROJECT_SOURCE_DIR}/third_party/libsrtp/include
        ${PROJECT_SOURCE_DIR}/third_party/libsrtp/config
        #${PROJECT_SOURCE_DIR}/third_party/openssl/include
        ${PROJECT_SOURCE_DIR}/third_party/boringssl/include
        ${PROJECT_SOURCE_DIR}/third_party/benchmark/include
        #${PROJECT_SOURCE_DIR}/third_party/libchrome/base/third_party/libevent
        #${PROJECT_SOURCE_DIR}/third_party/libchrome
        #${PROJECT_SOURCE_DIR}/rtc_base
        ${PROJECT_SOURCE_DIR}/third_party/build/include
        third_party/build
        /opt/ffmpeg/include
        )
target_link_libraries(libwebrtc PUBLIC
        ${PROJECT_SOURCE_DIR}/third_party/build/lib)