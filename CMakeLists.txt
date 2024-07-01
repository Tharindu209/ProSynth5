
cmake_minimum_required(VERSION 3.16)

project(
    Pro54
    VERSION 1.0
    LANGUAGES CXX C
)

set(CMAKE_INTERPROCEDURAL_OPTIMIZATION_RELEASE ON)
set(JUCE_ENABLE_MODULE_SOURCE_GROUPS ON)
set(JUCE_PATH "/Applications/JUCE")



if (JUCE_PATH)
    add_subdirectory(${JUCE_PATH} juce)
else()
    message (FATAL_ERROR "You must define the JUCE_PATH variable to point to your local JUCE folder")
endif()

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    set(CMAKE_OSX_ARCHITECTURES "arm64;x86_64")
    set(CMAKE_OSX_DEPLOYMENT_TARGET "10.15")
endif()

if (MSVC)
    add_compile_options (/Zc:__cplusplus)
endif()

juce_add_plugin(Pro5
    FORMATS Standalone AU AUv3 VST3
    DESCRIPTION "Pro5"
    BUNDLE_ID "com.DRMusic.Pro5"
    PLUGIN_CODE "Pr54"
    PLUGIN_MANUFACTURER_CODE "Cmaj"
    COMPANY_NAME "DRMusic"
    COMPANY_WEBSITE ""
    NEEDS_MIDI_INPUT TRUE
    NEEDS_MIDI_OUTPUT FALSE
    MICROPHONE_PERMISSION_ENABLED TRUE
    IS_SYNTH TRUE
    ICON_BIG "icon.png"

)

juce_generate_juce_header(Pro5)

add_compile_definitions (
    $<$<CONFIG:Debug>:DEBUG=1>
    $<$<CONFIG:Debug>:CMAJ_ENABLE_ALLOCATION_CHECKER=1>
    CMAJ_ENABLE_WEBVIEW_DEV_TOOLS=0
    JUCE_VST3_CAN_REPLACE_VST2=0
    JUCE_USE_CURL=0
    CMAJOR_DLL=1
)

file(GLOB_RECURSE HEADERS
    include/*.h
)

target_sources(Pro5 PRIVATE
    cmajor_plugin.cpp
    ${HEADERS}
)

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/" FILES ${HEADERS})

target_compile_features(Pro5 PRIVATE cxx_std_17)

target_include_directories(Pro5 PRIVATE include)

target_link_libraries(Pro5
    PRIVATE
        juce::juce_audio_utils
        $<$<AND:$<CXX_COMPILER_ID:GNU>,$<VERSION_LESS:$<CXX_COMPILER_VERSION>,9.0>>:stdc++fs>
)
